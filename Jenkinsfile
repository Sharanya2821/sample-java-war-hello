pipeline {
    agent any
    parameters {
        string(name: 'BRANCH_NAME', defaultValue: 'fe/dev', description: 'Specify the branch name to deploy')
    }
    stages {
        stage("GIT checkout") {
            steps {
                script {
                    // Checkout the specified branch
                    def branchName = params.BRANCH_NAME
                    checkout([$class: 'GitSCM', branches: [[name: branchName]], userRemoteConfigs: [[url: 'https://github.com/Sharanya2821/sample-java-war-hello.git']]])
                }
            }
         }
        stage("Testing") {
            steps {
                echo "Test Runs"
            
            }
        }
        stage("Build") {
            steps {
                sh "mvn clean package"
                sh "mv target/hello-1.0.war target/sample-java-war-hello.war"
            }
        }
        stage("Deploy to Dev") {
            when {
                expression { params.BRANCH_NAME == 'fe/dev' }
            }
            steps {
                deployToTomcat('13.49.78.43', 'tomcat', 'tomcat', 'http://13.49.78.43:8080/manager/text', '/sample-java-war-hello', 'Dev')
            }
        }
        stage("Deploy to QA") {
            when {
                expression { params.BRANCH_NAME == 'fe/qa' }
            }
            steps {
                deployToTomcat('13.61.14.58', 'tomcat', 'tomcat', 'http://13.61.14.58:8080/manager/text', '/sample-java-war-hello', 'QA')
            }
        }
        stage("Deploy to Prod") {
            when {
                expression { params.BRANCH_NAME == 'master' }
            }
            steps {
                input(message: "Do you want to proceed to PROD?", ok: "Proceed") // Approval step

                // Deploy to PROD server after approval
                deployToTomcat('13.60.89.219', 'tomcat', 'tomcat', 'http://13.60.89.219:8080/manager/text', '/sample-java-war-hello', 'Prod')
                
                
            }
        }
    }
}

def deployToTomcat(tomcatIP, username, password, tomcatURL, contextPath, environment) {
    def warFileName = 'target/sample-java-war-hello.war'

    // Deploy the WAR file using curl
    sh """
        curl -v -u ${username}:${password} --upload-file ${warFileName} ${tomcatURL}/deploy?path=${contextPath}&update=true
    """
    echo "Deployment to ${environment} server completed."
}