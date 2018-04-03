# packages-galaxy
Artix galaxy and lib32 packages

### Maintainers

The packages git follows the archlinux svn structure.
It contains both core and extra PKGBUILD

Each package folder consists of a trunk and repos directory
The package will always be updated in trunk, and then based on trunk action taken in the repos directory.

~~~
$ buildtree -h
Usage: buildtree [options]
    -p <pkg>      Package name
    -s            Clone or pull repos
    -z            Don't clone or pull arch repos
    -c            Compare packages
    -x            Include unstable kde and gnome
    -u            Show upgrade packages
    -d            Show downgrade packages
    -a            Show testing and staging packages
    -i            Import a package from arch repos
    -t            Import from arch trunk
    -v            View package depends
    -q            Query settings
    -h            This help
~~~


To sync(clone or pull) the arch and artix git repos, run

    buildtree -s

To compare arch and artix repo versions, check updates, run

    buildtree -cu

To compare arch and artix repo versions, check downgrades, run

    buildtree -cd

To compare arch and artix repo versions, check for package move, run

    buildtree -ca

It will show a version comparison table between arch repos and artix repos.
The first repository column shows the archlinux repos the package is in.

To import package 'foo' from arch packages into artix foo/trunk, run

    buildtree -p foo -i

In order to manage packages in the git tree, commitpkg standardizes the actions with descriptive commit messages.

~~~
$ commitpkg -h
Usage: commitpkg [options]
    -s <name>          Source repository [default:trunk]
    -p <pkg>           Package name
    -r                 Delete from repo (commitpkg only)
    -u                 Push
    -q                 Query settings and pretend
    -h                 This help
~~~

###### commitpkg has following symlinks in place:

- extrapkg 
- corepkg 
- testingpkg 
- stagingpkg 
- communitypkg 
- community-testingpkg 
- community-stagingpkg 
- multilibpkg 
- multilib-testingpkg 
- multilib-stagingpkg


#### Examples

release package 'foo' from trunk into repos/testing-x86_64 and push

    testingpkg -p foo -s trunk -u

move package 'foo' from repos/testing-x86_64 to repos/core-x86_64 and push

    corepkg -p foo -s testing -u

release package 'foo2' from trunk into repos/staging-any and push

    stagingpkg -p foo2 -s trunk -u

move packages 'foo2' from repos/staging-any to repos/testing-any and push

    testingpkg -p foo2 -s staging -u

release package 'foo3' from trunk to repos/community-x86_64 and push

    communitypkg -p foo3 -s trunk -u

release package 'foo4' from trunk to repos/multilib-testing-x86_64 and push

    multilib-testingpkg -p foo4 -s trunk -u

move packages 'foo4' from repos/multilib-testing-x86_64 to repos/multilib-x86_64 and push

    multilibpkg -p foo4 -s multilib-testing -u


##### The jenkins pipeline will not trigger any action on the server, if only trunk has been modified, commited and pushed.

* Jenkinsfile stages
    * Checkout
    * Build
    * Add
    * Remove

* Any repo moving operation of a package in git will trigger Add and Remove stages.
* Any new repos subdirectory will trigger the Build stage.
* The trunk dir will only trigger a checkout, modifications won't be built.

### Contributors

If you want a new or updated package merged into the git repository, please send a Pull Request with only the trunk of a given package. Please do not send PRs containing repos/$repo folders.


