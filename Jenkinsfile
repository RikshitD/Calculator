pipeline {
    agent any 
    tools {
        maven 'm7'
    }
    environment {
      //  GIT_CRED = '40389056-cc9c-4ed5-be98-9be7ad12cc5b'
        ADMIN = 'admin'
        MAIN_BRANCH = 'main'
        PUB_BRANCH = 'pub'
    //    GIT_REPO = credentials('GIT_URL')
    }
    stages {
        stage("Determine Branch") {
            steps {
                script {
                    if (env.BUILD_USER == ADMIN) {
                        env.BRANCH_TO_BUILD = "$MAIN_BRANCH"
                    } else {
                        env.BRANCH_TO_BUILD = "$PUB_BRANCH"
                    }
                }
            }
        }
        //stage("Fetching") {
          //  steps {
            //    script {
                    //withCredentials([string(credentialsId: 'GIT_URL', variable: 'GIT_REPO')]) {
              //          git branch: "${env.BRANCH_TO_BUILD}", url: "${GIT_REPO}", credentialsId: "${GIT_CRED}"     
                    //}
                //}
            //}
        //}
        stage("Validate") {
            steps {
                script {
                    sh 'mvn validate'
                }
            }
        }
        stage("Compile") {
            steps {
                script{
                     sh 'mvn compile'
                }
            }
        }
        stage("Test") {
            steps {
                script {
                    sh 'mvn test'
                }
            }
        }
        stage("Package") {
            steps {
                script {
                    sh 'mvn package'
                }
            }
        }
        
                //stage("Continue?") {
          //  steps {
            //        input message: 'Do you want to continue with deployment?', ok: 'Yes, continue'
            //}
        //}
        
        stage("Deploy") {
            steps {
                input message: 'Do you want to continue with deployment?', ok: 'Yes, continue'
                script {
                    sshagent(credentials : ['LOCAL_VM']) {
                        withCredentials([string(credentialsId: 'LOCAL_IP', variable: 'LOCAL_IP')]) {
                            sh 'scp -o StrictHostKeyChecking=no target/*.jar rikshit@${LOCAL_IP}:~/'
                        }                      
                    }
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}

