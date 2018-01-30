def REPO = ''
def PACKAGE = ''
def SOURCE_PATH = ''
def IS_MOVE = 'false'
def IS_DELETE = 'false'
def IS_BUILD = 'false'
def IS_ADD = 'false'
def MOVE_TO_REPO = ''
def BUILD_ARGS = ''

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
                    echo "currentCommit: ${currentCommit}"

                    def changedFilesStatus = sh(returnStdout: true, script: "git show --pretty=format: --name-status ${currentCommit}").tokenize('\n')
                    echo "changedFilesStatus: " + changedFilesStatus

                    for ( int i = 0; i < changedFilesStatus.size(); i++ ) {
                        def entry = changedFilesStatus[i].split()
                        def status = entry[0]
                        def filePath = []
                        for ( int j = 1; j < entry.size(); j++ ) {
                            if ( entry[j].contains('PKGBUILD') ){
                                filePath << entry[j].minus('/PKGBUILD')
                            }
                        }
                        def pathSize = filePath.size()
                        if ( pathSize > 1 ){
                            if ( status.contains('R') ) {
                                IS_MOVE = 'true'
                                if ( filePath[0].contains('community-staging') ) {
                                    SOURCE_PATH = filePath[1]
                                    REPO = 'galaxy-goblins'
                                    MOVE_TO_REPO = 'galaxy-gremlins'
                                } else if ( filePath[0].contains('community-testing') ) {
                                    SOURCE_PATH = filePath[1]
                                    if ( SOURCE_PATH.contains('community-staging') ) {
                                        REPO = 'galaxy-gremlins'
                                        MOVE_TO_REPO = 'galaxy-goblins'
                                    } else if ( SOURCE_PATH.contains('community-x86_64') || SOURCE_PATH.contains('community-any') ) {
                                        REPO = 'galaxy-gremlins'
                                        MOVE_TO_REPO = 'galaxy'
                                    }
                                } else if ( filePath[0].contains('multilib-staging') ) {
                                    SOURCE_PATH = filePath[1]
                                    REPO = 'lib32-goblins'
                                    MOVE_TO_REPO = 'lib32-gremlins'
                                } else if ( filePath[0].contains('multilib-testing') ) {
                                    SOURCE_PATH = filePath[1]
                                    if ( SOURCE_PATH.contains('multilib-staging') ) {
                                        REPO = 'lib32-gremlins'
                                        MOVE_TO_REPO = 'lib32-goblins'
                                    } else if ( SOURCE_PATH.contains('multilib-x86_64') ) {
                                        REPO = 'lib32-gremlins'
                                        MOVE_TO_REPO = 'lib32'
                                    }
                                }
                            }
                        } else {
                            if ( filePath[0] != null ) {
                                SOURCE_PATH = filePath[0]
                                def buildInfo = SOURCE_PATH.tokenize('/')
                                PACKAGE = buildInfo[0]
                                def repoNode = buildInfo[1]
                                REPO = buildInfo[2]
                                if ( repoNode == 'repos' ) {
                                
                                    if ( status == 'A' ) {
                                        IS_BUILD = 'true'
                                        if ( REPO.contains('community-staging') ) {
                                            REPO = 'galaxy-goblins'
                                        } else if ( REPO.contains('community-testing') ) {
                                            REPO = 'galaxy-gremlins'
                                        } else if ( REPO.contains('community-x86_64') || REPO.contains('community-any') ) {
                                            REPO = 'galaxy'
                                        } else if ( REPO.contains('multilib-staging') ) {
                                            BUILD_ARGS = '-m'
                                            REPO = 'lib32-goblins'
                                        } else if ( REPO.contains('multilib-testing') ) {
                                            BUILD_ARGS = '-m'
                                            REPO = 'lib32-gremlins'
                                        } else if ( REPO.contains('multilib-x86_64') ) {
                                            BUILD_ARGS = '-m'
                                            REPO = 'lib32'
                                        }
                                    }
                                    
                                    if ( status == 'M' ) {
                                        if ( REPO.contains('community-staging') ) {
                                            IS_BUILD = 'true'
                                            REPO = 'galaxy-goblins'
                                        } else if ( REPO.contains('community-testing') ) {
                                            IS_BUILD = 'true'
                                            REPO = 'galaxy-gremlins'
                                        } else if ( REPO.contains('community-x86_64') || REPO.contains('community-any') ) {
                                            IS_ADD = 'true'
                                            REPO = 'galaxy'
                                        } else if ( REPO.contains('multilib-staging') ) {
                                            IS_BUILD = 'true'
                                            BUILD_ARGS = '-m'
                                            REPO = 'lib32-goblins'
                                        } else if ( REPO.contains('multilib-testing') ) {
                                            IS_BUILD = 'true'
                                            BUILD_ARGS = '-m'
                                            REPO = 'lib32-gremlins'
                                        } else if ( REPO.contains('multilib-x86_64') ) {
                                            IS_ADD = 'true'
                                            BUILD_ARGS = '-m'
                                            REPO = 'lib32'
                                        }
                                    }
                                    
                                    if ( status == 'D' ) {
                                        IS_DELETE = 'true'
                                        if ( REPO.contains('community-staging') ) {
                                            REPO = 'galaxy-goblins'
                                        } else if ( REPO.contains('community-testing') ) {
                                            REPO = 'galaxy-gremlins'
                                        } else if ( REPO.contains('community-x86_64') || REPO.contains('community-any') ) {
                                            REPO = 'galaxy'
                                        } else if ( REPO.contains('multilib-staging') ) {
                                            REPO = 'lib32-goblins'
                                        } else if ( REPO.contains('multilib-testing') ) {
                                            REPO = 'lib32-gremlins'
                                        } else if ( REPO.contains('multilib-x86_64') ) {
                                            REPO = 'lib32'
                                        } 
                                        SOURCE_PATH = "${PACKAGE}/trunk"
                                    }
                                }
                            }
                        }
                        echo "PACKAGE: ${PACKAGE}"
                        echo "SOURCE_PATH: ${SOURCE_PATH}"
                        echo "REPO: ${REPO}"
                        echo "IS_BUILD: ${IS_BUILD}"
                        echo "IS_ADD: ${IS_ADD}"
                        echo "IS_MOVE: ${IS_MOVE} MOVE_TO_REPO: ${MOVE_TO_REPO}"
                        echo "IS_DELETE: ${IS_DELETE}"
                    }
                }
            }
        }
        stage('Build') {
            environment {
                BUILDBOT_GPGP = credentials('BUILDBOT_GPGP')
            }
            when {
                expression { return  IS_BUILD == 'true' }
                expression { return  IS_ADD == 'false' }
                expression { return  IS_MOVE == 'false' }
                expression { return  IS_DELETE == 'false' }
            }
            steps {
                script {
                    dir("${SOURCE_PATH}") {
                        if ( PACKAGE != '' ) {
                            echo "buildpkg2 -r ${REPO} ${BUILD_ARGS}"
                        }
                    }
                }
            }
            post {
                success {
                    script {
                        dir("${SOURCE_PATH}") {
                            if ( PACKAGE != '' ) {
                                echo "deploypkg2 -a -d ${REPO}"
                            }
                        }
                    }
                }
            }
        }
        stage('Move') {
            when {
                expression { return  IS_BUILD == 'false' }
                expression { return  IS_ADD == 'false' }
                expression { return  IS_MOVE == 'true' }
                expression { return  IS_DELETE == 'false' }
            }
            steps {
                script {
                    dir("${SOURCE_PATH}") {
                        echo "deploypkg2 -m -d ${MOVE_TO_REPO} -s ${REPO}"
                    }
                }
            }
        }
        stage('Add') {
            when {
                expression { return  IS_BUILD == 'false' }
                expression { return  IS_ADD == 'true' }
                expression { return  IS_MOVE == 'false' }
                expression { return  IS_DELETE == 'false' }
            }
            steps {
                script {
                    dir("${SOURCE_PATH}") {
                        echo "deploypkg2 -a -d ${REPO}"
                    }
                }
            }
        }
        stage('Remove') {
            when {
                expression { return  IS_BUILD == 'false' }
                expression { return  IS_ADD == 'false' }
                expression { return  IS_MOVE == 'false' }
                expression { return  IS_DELETE == 'true' }
            }
            steps {
                script {
                    dir("${SOURCE_PATH}") {
                        echo "deploypkg2 -r -d ${REPO}"
                    }
                }
            }
        }
    }
}
