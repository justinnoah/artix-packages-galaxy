def PKG_TRUNK = ''
def PKG_PATH = ''
def ADD_ARGS = ''
def RM_ARGS = ''
def BUILD_ARGS = ''
def IS_ADD = 'false'
def IS_REMOVE = 'false'
def IS_BUILD = 'false'
def IS_BUILD_SUCCESS = 'false'

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

                    def currentCommit = sh(returnStdout: true, script: 'git rev-parse @').trim()
                    def changedFilesStatus = sh(returnStdout: true, script: "git show --pretty=format: --name-status ${currentCommit}").tokenize('\n')
                    def changedFileState = []
                    int entryCount = 0
                    def repoAdd = ''
                    def repoRemove = ''

                    for ( int i = 0; i < changedFilesStatus.size(); i++ ) {
                        def entry = changedFilesStatus[i].split()
                        def fileStatus = entry[0]
                        entryCount = entry.size()
                        for ( int j = 1; j < entry.size(); j++ ) {
                            if ( entry[j].contains('/PKGBUILD') && entry[j].contains('/repos') ){
                                changedFileState << "${fileStatus} " + entry[j].minus('/PKGBUILD')
                            }
                        }
                    }

                    int pkgCount = changedFileState.size()

                    echo "currentCommit: ${currentCommit}"
                    echo "changedFileState: ${changedFileState}"

                    if ( pkgCount > 0 ) {
                        // Usually a move from testing to core/extra, the dest repo folder does exist, ie rm and add
                        if ( entryCount == 2 && pkgCount == 2 ) {
                            def srcPkg = changedFileState[0].split()
                            def destPkg = changedFileState[1].split()
                            def filePath = [srcPkg[1], destPkg[1]]
                            def fileState = [srcPkg[0], destPkg[0]]
                            def srcRepo = filePath[0].tokenize('/')[2]
                            def destRepo = filePath[1].tokenize('/')[2]

                            if ( fileState[0] == 'M' ) {
                                IS_ADD = 'true'
                                if ( srcRepo == 'staging-x86_64' || srcRepo == 'staging-any' ) {
                                    repoAdd = 'goblins'
                                } else if ( srcRepo == 'testing-x86_64' || srcRepo == 'testing-any' ) {
                                    repoAdd = 'gremlins'
                                } else if ( srcRepo.contains('core') ) {
                                    repoAdd = 'system'
                                } else if ( srcRepo.contains('extra') ) {
                                    repoAdd = 'world'
                                }

                                if ( srcRepo.contains('community-staging') ) {
                                    repoAdd = 'galaxy-goblins'
                                } else if ( srcRepo.contains('community-testing') ) {
                                    repoAdd = 'galaxy-gremlins'
                                } else if ( srcRepo == 'community-x86_64' || srcRepo == 'community-any' ) {
                                    repoAdd = 'galaxy'
                                }

                                if ( srcRepo.contains('multilib-staging') ) {
                                    repoAdd = 'lib32-goblins'
                                } else if ( srcRepo.contains('multilib-testing') ) {
                                    repoAdd = 'lib32-gremlins'
                                } else if ( srcRepo.contains('multilib-x86_64') ) {
                                    repoAdd = 'lib32'
                                }
                                PKG_PATH = filePath[1]
                            } else if ( fileState[1] == 'M' ) {
                                IS_ADD = 'true'
                                if ( destRepo == 'staging-x86_64' || destRepo == 'staging-any' ) {
                                    repoAdd = 'goblins'
                                } else if ( destRepo == 'testing-x86_64' || destRepo == 'testing-any' ) {
                                    repoAdd = 'gremlins'
                                } else if ( destRepo.contains('core') ) {
                                    repoAdd = 'system'
                                } else if ( destRepo.contains('extra') ) {
                                    repoAdd = 'world'
                                }

                                if ( destRepo.contains('community-staging') ) {
                                    repoAdd = 'galaxy-goblins'
                                } else if ( destRepo.contains('community-testing') ) {
                                    repoAdd = 'galaxy-gremlins'
                                } else if ( destRepo == 'community-x86_64' || destRepo == 'community-any' ) {
                                    repoAdd = 'galaxy'
                                }

                                if ( destRepo.contains('multilib-staging') ) {
                                    repoAdd = 'lib32-goblins'
                                } else if ( destRepo.contains('multilib-testing') ) {
                                    repoAdd = 'lib32-gremlins'
                                } else if ( destRepo.contains('multilib-x86_64') ) {
                                    repoAdd = 'lib32'
                                }
                                PKG_PATH = filePath[0]
                            }

                            if ( fileState[0] == 'D' ) {
                                IS_REMOVE = 'true'
                                if ( srcRepo == 'staging-x86_64' || srcRepo == 'staging-any' ) {
                                    repoRemove = 'goblins'
                                } else if ( srcRepo == 'testing-x86_64' || srcRepo == 'testing-any' ) {
                                    repoRemove = 'gremlins'
                                } else if ( srcRepo.contains('core') ) {
                                    repoRemove = 'system'
                                } else if ( srcRepo.contains('extra') ) {
                                    repoRemove = 'world'
                                }

                                if ( srcRepo.contains('community-staging') ) {
                                    repoRemove = 'galaxy-goblins'
                                } else if ( srcRepo.contains('community-testing') ) {
                                    repoRemove = 'galaxy-gremlins'
                                } else if ( srcRepo == 'community-x86_64' || srcRepo == 'community-any' ) {
                                    repoRemove = 'galaxy'
                                }

                                if ( srcRepo.contains('multilib-staging') ) {
                                    repoRemove = 'lib32-goblins'
                                } else if ( srcRepo.contains('multilib-testing') ) {
                                    repoRemove = 'lib32-gremlins'
                                } else if ( srcRepo.contains('multilib-x86_64') ) {
                                    repoRemove = 'lib32'
                                }
                                PKG_PATH = filePath[1]
                            } else if ( fileState[1] == 'D' ) {
                                IS_REMOVE = 'true'
                                if ( destRepo == 'staging-x86_64' || destRepo == 'staging-any' ) {
                                    repoRemove = 'goblins'
                                } else if ( destRepo == 'testing-x86_64' || destRepo == 'testing-any' ) {
                                    repoRemove = 'gremlins'
                                } else if ( destRepo.contains('core') ) {
                                    repoRemove = 'system'
                                } else if ( destRepo.contains('extra') ) {
                                    repoRemove = 'world'
                                }

                                if ( destRepo.contains('community-staging') ) {
                                    repoRemove = 'galaxy-goblins'
                                } else if ( destRepo.contains('community-testing') ) {
                                    repoRemove = 'galaxy-gremlins'
                                } else if ( destRepo == 'community-x86_64' || destRepo == 'community-any' ) {
                                    repoRemove = 'galaxy'
                                }

                                if ( destRepo.contains('multilib-staging') ) {
                                    repoRemove = 'lib32-goblins'
                                } else if ( destRepo.contains('multilib-testing') ) {
                                    repoRemove = 'lib32-gremlins'
                                } else if ( destRepo.contains('multilib-x86_64') ) {
                                    repoRemove = 'lib32'
                                }
                                PKG_PATH = filePath[0]
                            }

                            ADD_ARGS = "-a -d ${repoAdd}"
                            RM_ARGS = "-r -d ${repoRemove}"
                            PKG_TRUNK = filePath[0].tokenize('/')[0] + '/trunk'
                            echo "srcRepo: ${srcRepo}"
                            echo "destRepo: ${destRepo}"
                            echo "repoAdd: ${repoAdd}"
                            echo "repoRemove: ${repoRemove}"
                        }

                        if ( entryCount == 3 && pkgCount == 2 ) {
                            // Usually a move from staging to testing, the dest repo doesn't exist, ie rename of folder
                            def srcPkg = changedFileState[0].split()
                            def destPkg = changedFileState[1].split()
                            def filePath = [srcPkg[1], destPkg[1]]
                            def fileState = [srcPkg[0], destPkg[0]]
                            def srcRepo = filePath[0].tokenize('/')[2]
                            def destRepo = filePath[1].tokenize('/')[2]

                            if ( fileState[0].contains('R') && fileState[1].contains('R') )  {
                                IS_ADD = 'true'
                                IS_REMOVE = 'true'

                                if ( srcRepo == 'staging-x86_64' && destRepo == 'testing-x86_64' ) {
                                    repoAdd = 'gremlins'
                                    repoRemove = 'goblins'
                                } else if ( srcRepo == 'staging-any' && destRepo == 'testing-any' ) {
                                    repoAdd = 'gremlins'
                                    repoRemove = 'goblins'
                                } else if ( srcRepo == 'testing-x86_64' && destRepo == 'staging-x86_64' ) {
                                    repoAdd = 'goblins'
                                    repoRemove = 'gremlins'
                                } else if ( srcRepo == 'testing-any' && destRepo == 'staging-any' ) {
                                    repoAdd = 'goblins'
                                    repoRemove = 'gremlins'
                                }

                                if ( srcRepo.contains('core') && destRepo == 'testing-x86_64' ) {
                                    repoAdd = 'gremlins'
                                    repoRemove = 'system'
                                } else if ( srcRepo.contains('core') && destRepo == 'testing-any' ) {
                                    repoAdd = 'gremlins'
                                    repoRemove = 'system'
                                } else if ( srcRepo == 'testing-x86_64' && destRepo.contains('core') ) {
                                    repoAdd = 'system'
                                    repoRemove = 'gremlins'
                                } else if ( srcRepo == 'testing-any' && destRepo.contains('core') ) {
                                    repoAdd = 'system'
                                    repoRemove = 'gremlins'
                                }

                                if ( srcRepo.contains('extra') && destRepo == 'testing-x86_64' ) {
                                    repoAdd = 'gremlins'
                                    repoRemove = 'world'
                                } else if ( srcRepo.contains('extra') && destRepo == 'testing-any' ) {
                                    repoAdd = 'gremlins'
                                    repoRemove = 'world'
                                } else if ( srcRepo == 'testing-x86_64' && destRepo.contains('extra') ) {
                                    repoAdd = 'world'
                                    repoRemove = 'gremlins'
                                } else if ( srcRepo == 'testing-any' && destRepo.contains('extra') ) {
                                    repoAdd = 'world'
                                    repoRemove = 'gremlins'
                                }

                                if ( srcRepo.contains('core') && destRepo.contains('extra') ) {
                                    repoAdd = 'world'
                                    repoRemove = 'system'
                                } else if ( srcRepo.contains('extra') && destRepo.contains('core') ) {
                                    repoAdd = 'system'
                                    repoRemove = 'world'
                                }

                                if ( srcRepo == 'community-x86_64' && destRepo.contains('extra') ) {
                                    repoAdd = 'world'
                                    repoRemove = 'galaxy'
                                } else if ( srcRepo == 'community-any' && destRepo.contains('extra') ) {
                                    repoAdd = 'world'
                                    repoRemove = 'galaxy'
                                } else if ( srcRepo.contains('extra') && destRepo == 'community-x86_64' ) {
                                    repoAdd = 'galaxy'
                                    repoRemove = 'world'
                                } else if ( srcRepo.contains('extra') && destRepo == 'community-any' ) {
                                    repoAdd = 'galaxy'
                                    repoRemove = 'world'
                                }

                                if ( srcRepo == 'community-x86_64' && destRepo.contains('core') ) {
                                    repoAdd = 'system'
                                    repoRemove = 'galaxy'
                                } else if ( srcRepo == 'community-any' && destRepo.contains('core') ) {
                                    repoAdd = 'system'
                                    repoRemove = 'galaxy'
                                } else if ( srcRepo.contains('core') && destRepo == 'community-x86_64' ) {
                                    repoAdd = 'galaxy'
                                    repoRemove = 'system'
                                } else if ( srcRepo.contains('core') && destRepo == 'community-any' ) {
                                    repoAdd = 'galaxy'
                                    repoRemove = 'system'
                                }

                                if ( srcRepo.contains('community-staging') && destRepo.contains('community-testing') ) {
                                    repoAdd = 'galaxy-gremlins'
                                    repoRemove = 'galaxy-goblins'
                                } else if ( srcRepo.contains('community-testing') && destRepo.contains('community-staging') ) {
                                    repoAdd = 'galaxy-goblins'
                                    repoRemove = 'galaxy-gremlins'
                                } else if ( srcRepo.contains('community-testing') && destRepo == 'community-x86_64' ) {
                                    repoAdd = 'galaxy'
                                    repoRemove = 'galaxy-gremlins'
                                } else if ( srcRepo.contains('community-testing') && destRepo == 'community-any' ) {
                                    repoAdd = 'galaxy'
                                    repoRemove = 'galaxy-gremlins'
                                } else if ( srcRepo == 'community-x86_64' && destRepo.contains('community-testing') ) {
                                    repoAdd = 'galaxy-gremlins'
                                    repoRemove = 'galaxy'
                                } else if ( srcRepo == 'community-any' && destRepo.contains('community-testing') ) {
                                    repoAdd = 'galaxy-gremlins'
                                    repoRemove = 'galaxy'
                                }

                                if ( srcRepo.contains('multilib-staging') && destRepo.contains('multilib-testing') ) {
                                    repoAdd = 'lib32-gremlins'
                                    repoRemove = 'lib32-goblins'
                                } else if ( srcRepo.contains('multilib-testing') && destRepo.contains('multilib-staging') ) {
                                    repoAdd = 'lib32-goblins'
                                    repoRemove = 'lib32-gremlins'
                                } else if ( srcRepo.contains('multilib-testing') && destRepo.contains('multilib-x86_64') ) {
                                    repoAdd = 'lib32'
                                    repoRemove = 'lib32-gremlins'
                                } else if ( srcRepo.contains('multilib-x86_64') && destRepo.contains('multilib-testing') ) {
                                    repoAdd = 'lib32-gremlins'
                                    repoRemove = 'lib32'
                                }
                            }
                            ADD_ARGS = "-a -d ${repoAdd}"
                            RM_ARGS = "-r -d ${repoRemove}"
                            PKG_TRUNK = filePath[0].tokenize('/')[0] + '/trunk'
                            PKG_PATH = filePath[1]
                            echo "srcRepo: ${srcRepo}"
                            echo "destRepo: ${destRepo}"
                            echo "repoAdd: ${repoAdd}"
                            echo "repoRemove: ${repoRemove}"
                        }

                        if ( pkgCount == 1 ) {
                            // Usually a build
                            def srcPkg = changedFileState[0].split()
                            def fileState = srcPkg[0]
                            def filePath = srcPkg[1]
                            def srcRepo = filePath.tokenize('/')[2]

                            if ( srcRepo == 'staging-x86_64' || srcRepo == 'staging-any' ) {
                                if ( fileState == 'A' || fileState == 'M' ) {
                                    IS_BUILD = 'true'
                                }
                                if ( fileState == 'D' ) {
                                    IS_REMOVE = 'true'
                                }
                                repoAdd = 'goblins'
                                repoRemove = repoAdd
                            } else if ( srcRepo == 'testing-x86_64' || srcRepo == 'testing-any' ) {
                                if ( fileState == 'A' || fileState == 'M' ) {
                                    IS_BUILD = 'true'
                                }
                                if ( fileState == 'D' ) {
                                    IS_REMOVE = 'true'
                                }
                                repoAdd = 'gremlins'
                                repoRemove = repoAdd
                            } else if ( srcRepo.contains('core') ) {
                                if ( fileState == 'A' || fileState == 'M' ) {
                                    IS_BUILD = 'true'
                                }
                                if ( fileState == 'D' ) {
                                    IS_REMOVE = 'true'
                                }
                                repoAdd = 'system'
                                repoRemove = repoAdd
                            } else if ( srcRepo.contains('extra') ) {
                                if ( fileState == 'A' || fileState == 'M' ) {
                                    IS_BUILD = 'true'
                                }
                                if ( fileState == 'D' ) {
                                    IS_REMOVE = 'true'
                                }
                                repoAdd = 'world'
                                repoRemove = repoAdd
                            }

                            if ( srcRepo.contains('community-staging') ) {
                                if ( fileState == 'A' || fileState == 'M' ) {
                                    IS_BUILD = 'true'
                                }
                                if ( fileState == 'D' ) {
                                    IS_REMOVE = 'true'
                                }
                                repoAdd = 'galaxy-goblins'
                                repoRemove = repoAdd
                            } else if ( srcRepo.contains('community-testing') ) {
                                if ( fileState == 'A' || fileState == 'M' ) {
                                    IS_BUILD = 'true'
                                }
                                if ( fileState == 'D' ) {
                                    IS_REMOVE = 'true'
                                }
                                repoAdd = 'galaxy-gremlins'
                                repoRemove = repoAdd
                            } else if ( srcRepo == 'community-x86_64' || srcRepo == 'community-any' ) {
                                if ( fileState == 'A' || fileState == 'M' ) {
                                    IS_BUILD = 'true'
                                }
                                if ( fileState == 'D' ) {
                                    IS_REMOVE = 'true'
                                }
                                repoAdd = 'galaxy'
                                repoRemove = repoAdd
                            }

                            if ( srcRepo.contains('multilib-staging') ) {
                                if ( fileState == 'A' || fileState == 'M' ) {
                                    IS_BUILD = 'true'
                                }
                                if ( fileState == 'D' ) {
                                    IS_REMOVE = 'true'
                                }
                                repoAdd = 'lib32-goblins'
                                repoRemove = repoAdd
                            } else if ( srcRepo.contains('multilib-testing') ) {
                                if ( fileState == 'A' || fileState == 'M' ) {
                                    IS_BUILD = 'true'
                                }
                                if ( fileState == 'D' ) {
                                    IS_REMOVE = 'true'
                                }
                                repoAdd = 'lib32-gremlins'
                                repoRemove = repoAdd
                            } else if ( srcRepo.contains('multilib-x86_64') ) {
                                if ( fileState == 'A' || fileState == 'M' ) {
                                    IS_BUILD = 'true'
                                }
                                if ( fileState == 'D' ) {
                                    IS_REMOVE = 'true'
                                }
                                repoAdd = 'lib32'
                                repoRemove = repoAdd
                            }
                            ADD_ARGS = "-a -d ${repoAdd}"
                            RM_ARGS = "-r -d ${repoRemove}"
                            BUILD_ARGS = "-r ${repoAdd}"

                            PKG_PATH = filePath
                            PKG_TRUNK = filePath.tokenize('/')[0] + '/trunk'
                            echo "srcRepo: ${srcRepo}"
                            echo "repoAdd: ${repoAdd}"
                            echo "repoRemove: ${repoRemove}"
                        }
                    }
                    echo "PKG_PATH: ${PKG_PATH}"
                }
            }
        }
        stage('Build') {
            when {
                expression { return IS_BUILD == 'true' }
            }
            steps {
                dir("${PKG_PATH}") {
                    sh "buildpkg ${BUILD_ARGS}"
                }
            }
            post {
                failure {
                    emailext body: "<b>Failure</b><br><br>Project: ${env.JOB_NAME} ${PKG_PATH} <br>Build Number: ${env.BUILD_NUMBER} <br> URL of build: ${env.BUILD_URL}", subject: "FAILURE CI: ${env.JOB_NAME} ${PKG_PATH}", to: "devs@artixlinux.org"
                }
                success {
                    script {
                        IS_BUILD_SUCCESS = 'true'
                        ADD_ARGS = ADD_ARGS + ' -s'
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
                    expression { return IS_ADD == 'true' }
                    expression { return IS_BUILD_SUCCESS == 'true' }
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
                expression { return IS_REMOVE == 'true' }
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
