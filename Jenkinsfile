String PKG_TRUNK = ''
String PKG_PATH = ''
String ADD_ARGS = ''
String RM_ARGS = ''
String BUILD_ARGS = ''
Boolean IS_ADD = false
Boolean IS_REMOVE = false
Boolean IS_BUILD = false
Boolean IS_BUILD_SUCCESS = false

static String getRepo(String src) {
    String repo = ''
    if ( src == 'staging-x86_64' || src == 'staging-any' ) {
        repo = 'goblins'
    } else if ( src == 'testing-x86_64' || src == 'testing-any' ) {
        repo = 'gremlins'
    } else if ( src.contains('core') ) {
        repo = 'system'
    } else if ( src.contains('extra') ) {
        repo = 'world'
    } else if ( src.contains('community-staging') ) {
        repo = 'galaxy-goblins'
    } else if ( src.contains('community-testing') ) {
        repo = 'galaxy-gremlins'
    } else if ( src == 'community-x86_64' || src == 'community-any' ) {
        repo = 'galaxy'
    } else if ( src.contains('multilib-staging') ) {
        repo = 'lib32-goblins'
    } else if ( src.contains('multilib-testing') ) {
        repo = 'lib32-gremlins'
    } else if ( src.contains('multilib-x86_64') ) {
        repo = 'lib32'
    }
    return repo
}

static List<String> getRepos(String src, String dest) {
    List<String> repoList = []
    if ( src == 'staging-x86_64' && dest == 'testing-x86_64' ) {
        repoList.add('gremlins')
        repoList.add('goblins')
    } else if ( src == 'staging-any' && dest == 'testing-any' ) {
        repoList.add('gremlins')
        repoList.add('goblins')
    } else if ( src == 'testing-x86_64' && dest == 'staging-x86_64' ) {
        repoList.add('goblins')
        repoList.add('gremlins')
    } else if ( src == 'testing-any' && dest == 'staging-any' ) {
        repoList.add('goblins')
        repoList.add('gremlins')
    } else if ( src.contains('core') && dest == 'testing-x86_64' ) {
        repoList.add('gremlins')
        repoList.add('system')
    } else if ( src.contains('core') && dest == 'testing-any' ) {
        repoList.add('gremlins')
        repoList.add('system')
    } else if ( src == 'testing-x86_64' && dest.contains('core') ) {
        repoList.add('system')
        repoList.add('gremlins')
    } else if ( src == 'testing-any' && dest.contains('core') ) {
        repoList.add('system')
        repoList.add('gremlins')
    } else if ( src.contains('extra') && dest == 'testing-x86_64' ) {
        repoList.add('gremlins')
        repoList.add('world')
    } else if ( src.contains('extra') && dest == 'testing-any' ) {
        repoList.add('gremlins')
        repoList.add('world')
    } else if ( src == 'testing-x86_64' && dest.contains('extra') ) {
        repoList.add('world')
        repoList.add('gremlins')
    } else if ( src == 'testing-any' && dest.contains('extra') ) {
        repoList.add('world')
        repoList.add('gremlins')
    } else if ( src.contains('core') && dest.contains('extra') ) {
        repoList.add('world')
        repoList.add('system')
    } else if ( src.contains('extra') && dest.contains('core') ) {
        repoList.add('system')
        repoList.add('world')
    } else if ( src == 'community-x86_64' && dest.contains('extra') ) {
        repoList.add('world')
        repoList.add('galaxy')
    } else if ( src == 'community-any' && dest.contains('extra') ) {
        repoList.add('world')
        repoList.add('galaxy')
    } else if ( src.contains('extra') && dest == 'community-x86_64' ) {
        repoList.add('galaxy')
        repoList.add('world')
    } else if ( src.contains('extra') && dest == 'community-any' ) {
        repoList.add('galaxy')
        repoList.add('world')
    } else if ( src == 'community-x86_64' && dest.contains('core') ) {
        repoList.add('system')
        repoList.add('galaxy')
    } else if ( src == 'community-any' && dest.contains('core') ) {
        repoList.add('system')
        repoList.add('galaxy')
    } else if ( src.contains('core') && dest == 'community-x86_64' ) {
        repoList.add('galaxy')
        repoList.add('system')
    } else if ( src.contains('core') && dest == 'community-any' ) {
        repoList.add('galaxy')
        repoList.add('system')
    } else if ( src.contains('community-staging') && dest.contains('community-testing') ) {
        repoList.add('galaxy-gremlins')
        repoList.add('galaxy-goblins')
    } else if ( src.contains('community-testing') && dest.contains('community-staging') ) {
        repoList.add('galaxy-goblins')
        repoList.add('galaxy-gremlins')
    } else if ( src.contains('community-testing') && dest == 'community-x86_64' ) {
        repoList.add('galaxy')
        repoList.add('galaxy-gremlins')
    } else if ( src.contains('community-testing') && dest == 'community-any' ) {
        repoList.add('galaxy')
        repoList.add('galaxy-gremlins')
    } else if ( src == 'community-x86_64' && dest.contains('community-testing') ) {
        repoList.add('galaxy-gremlins')
        repoList.add('galaxy')
    } else if ( src == 'community-any' && dest.contains('community-testing') ) {
        repoList.add('galaxy-gremlins')
        repoList.add('galaxy')
    } else if ( src.contains('multilib-staging') && dest.contains('multilib-testing') ) {
        repoList.add('lib32-gremlins')
        repoList.add('lib32-goblins')
    } else if ( src.contains('multilib-testing') && dest.contains('multilib-staging') ) {
        repoList.add('lib32-goblins')
        repoList.add('lib32-gremlins')
    } else if ( src.contains('multilib-testing') && dest.contains('multilib-x86_64') ) {
        repoList.add('lib32')
        repoList.add('lib32-gremlins')
    } else if ( src.contains('multilib-x86_64') && dest.contains('multilib-testing') ) {
        repoList.add('lib32-gremlins')
        repoList.add('lib32')
    }
    return repoList
}

pipeline {
    agent any
    options {
        skipDefaultCheckout()
        timestamps()
    }
    stages {
        stage('Checkout') {
            steps {
                script {
                    checkout scm

                    String currentCommit = sh(returnStdout: true, script: 'git rev-parse @').trim()
                    List<String> changeSet = sh(returnStdout: true, script: "git show --pretty=format: --name-status ${currentCommit}").tokenize('\n')

                    List<String> pkgPathState = []

                    for ( int i = 0; i < changeSet.size(); i++ ) {
                        List<String> entry = changeSet[i].split()
                        String fileStatus = entry[0]
                        for ( int j = 1; j < entry.size(); j++ ) {
                            if ( entry[j].contains('/PKGBUILD') && entry[j].contains('/repos') ){
                                pkgPathState.add("${fileStatus} " + entry[j].minus('/PKGBUILD'))
                            }
                        }
                    }

                    byte pkgCount = pkgPathState.size()

                    echo "currentCommit: ${currentCommit}"
                    echo "pkgPathState: ${pkgPathState}"

                    if ( pkgCount > 0 ) {
                        List<String> pkgPath = []
                        List<String> pkgState = []
                        String srcRepo = ''
                        String destRepo = ''
                        String repoAdd = ''
                        String repoRemove = ''
                        if ( pkgCount == 1 ) {
                            pkgPath.add(pkgPathState[0].split()[1])
                            pkgState.add(pkgPathState[0].split()[0])
                            srcRepo = pkgPath[0].tokenize('/')[2]

                            if ( pkgState[0] == 'A' || pkgState[0] == 'M' ) {
                                IS_BUILD = true
                                repoAdd = getRepo(srcRepo)
                                BUILD_ARGS = "-r ${repoAdd}"
                            } else if ( pkgState[0] == 'D' ) {
                                IS_REMOVE = true
                                repoRemove = getRepo(srcRepo)
                            }
                            PKG_PATH = pkgPath[0]
                            PKG_TRUNK = pkgPath[0].tokenize('/')[0] + '/trunk'
                        } else if ( pkgCount == 2 ) {
                            pkgPath.add(pkgPathState[0].split()[1])
                            pkgPath.add(pkgPathState[1].split()[1])
                            pkgState.add(pkgPathState[0].split()[0])
                            pkgState.add(pkgPathState[1].split()[0])
                            srcRepo = pkgPath[0].tokenize('/')[2]
                            destRepo = pkgPath[1].tokenize('/')[2]

                            if ( pkgState[0] == 'M' ) {
                                IS_ADD = true
                                repoAdd = getRepo(srcRepo)
                                PKG_PATH = pkgPath[1]
                            } else if ( pkgState[1] == 'M' ) {
                                IS_ADD = true
                                repoAdd = getRepo(destRepo)
                                PKG_PATH = pkgPath[0]
                            }

                            if ( pkgState[0] == 'D' ) {
                                IS_REMOVE = true
                                repoRemove = getRepo(srcRepo)
                                PKG_PATH = pkgPath[1]
                            } else if ( pkgState[1] == 'D' ) {
                                IS_REMOVE = true
                                repoRemove = getRepo(destRepo)
                                PKG_PATH = pkgPath[0]
                            }

                            if ( pkgState[0].contains('R') && pkgState[1].contains('R') )  {
                                IS_ADD = true
                                IS_REMOVE = true
                                repoAdd = getRepos(srcRepo, destRepo)[0]
                                repoRemove = getRepos(srcRepo, destRepo)[1]
                                PKG_PATH = pkgPath[1]
                            }
                            PKG_TRUNK = pkgPath[0].tokenize('/')[0] + '/trunk'
                        }
                        ADD_ARGS = "-a -d ${repoAdd}"
                        RM_ARGS = "-r -d ${repoRemove}"
                        echo "PKG_PATH: ${PKG_PATH}"
                    }
                }
            }
        }
        stage('Build') {
            when {
                expression { return IS_BUILD }
            }
            steps {
                dir("${PKG_PATH}") {
                    sh "buildpkg ${BUILD_ARGS}"
                }
            }
            post {
                failure {
                    emailext body: "<b>Failure</b><br><br>Project: ${env.JOB_NAME}/${PKG_PATH} <br>Build Number: ${env.BUILD_NUMBER} <br> URL of build: ${env.BUILD_URL}", subject: "FAILURE CI: ${env.JOB_NAME}/${PKG_PATH}", to: "devs@artixlinux.org"
                }
                success {
                    script {
                        IS_BUILD_SUCCESS = true
                        ADD_ARGS += ' -s'
                    }
                }
            }
        }
        stage('Add') {
            environment {
                BUILDBOT_GPGP = credentials('BUILDBOT_GPGP')
            }
            when {
                anyOf {
                    expression { return IS_ADD }
                    expression { return IS_BUILD_SUCCESS }
                }
            }
            steps {
                script {
                    if ( fileExists(PKG_PATH + '/PKGBUILD') ) {
                        dir(PKG_PATH) {
                            sh "deploypkg ${ADD_ARGS}"
                        }
                    } else {
                        dir(PKG_TRUNK) {
                            sh "deploypkg ${ADD_ARGS}"
                        }
                    }
                }
            }
        }
        stage('Remove') {
            when {
                expression { return IS_REMOVE }
            }
            steps {
                script {
                    if ( fileExists(PKG_PATH + '/PKGBUILD') ) {
                        dir(PKG_PATH) {
                            sh "deploypkg ${RM_ARGS}"
                        }
                    } else {
                        dir(PKG_TRUNK) {
                            sh "deploypkg ${RM_ARGS}"
                        }
                    }
                }
            }
        }
    }
}
