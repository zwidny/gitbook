# Jenkins pipeline参数支持multi-choice

```groovy

// pipeline_test_zhao流水线Zhao测试
pipeline {
    agent any
    parameters {
        // 多选支持
        extendedChoice( 
        defaultValue: '', 
        description: '需要切换版本的环境', 
        multiSelectDelimiter: ',', 
        name: 'ENV_CHOICE', 
        quoteValue: false, 
        saveJSONParameterToFile: false, 
        type: 'PT_CHECKBOX', 
        value:'Resource,Release,Online', 
        visibleItemCount: 3)
    
    }
    stages {
        stage('初始化') {
            steps {
                script {
                    PLATFORM_INFO = [:]
                }
            }
        }
        stage('美术资源打包') {
            matrix {
                agent {
                    node {
                        label 'mv_ingame'
                        customWorkspace "workspace/${PLATFORM}"
                    }
                }
                axes {
                    axis {
                        name 'PLATFORM'
                        values 'ios', 'android'
                    }
                }
                environment {
                    GIT_PERSISTENT_BRANCH = "mv-ingame-${PLATFORM}-${params.git_branch_name}"
                }
                stages {
                    stage('配置初始化') {
                        steps {
                            script {
                                PLATFORM_INFO[PLATFORM] = [:]
                                PLATFORM_INFO[PLATFORM].update = false
                                echo "GIT_PERSISTENT_BRANCH: ${GIT_PERSISTENT_BRANCH}"
                            }
                        }
                    }
                    stage("回显配置") {
                        steps {
                            script {
                                echo "$PLATFORM ${PLATFORM_INFO[PLATFORM]}"

                            }
                        }
                    }
                    stage('Get Current Build Info') {
                        steps {
                            echo "Current build info: ${currentBuild.currentResult}"
                        }
                    }
                }
            }
        }

    }
    post {
        always {
            script {
                dir("pyframe-pipeline") {
                    // 使用build-user-vars-plugin插件提供的步骤来获取构建用户
                    wrap([$class: 'BuildUser']) {
                        env.CURRENT_RESULT = currentBuild.currentResult
                        env.BUILD_USER = "${BUILD_USER}"
                        env.BUILD_ID = "${BUILD_ID}"
                        env.DURATION = currentBuild.duration
                        env.DISPLAY_NAME = currentBuild.displayName
                        // 定时触发时，获取不到构建用户的邮箱, 远程构建触发时, 也获取不到构建邮箱
                        try {
                            env.BUILD_USER_EMAIL = "${BUILD_USER_EMAIL}"
                        } catch (exc) {
                            echo "获取不到构建的用户邮箱"
                            env.BUILD_USER_EMAIL = ""
                        }
                    }
                    println currentBuild.fullDisplayName
                    def out = bat (script: """python -c "import os; print(os.environ)" """, returnStdout: true)
                    println out
                }
            }
        }
    }
}
```