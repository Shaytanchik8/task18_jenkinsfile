pipeline{
    agent any
    stages{
        stage('Login'){
            steps {
                withCredentials([usernamePassword(credentialsId: 'Cred_for_Dockerhub', usernameVariable : 'USERNAME', passwordVariable : 'PASSWORD')]) {
                    sh 'docker login -u $USERNAME -p $PASSWORD'
            }
        }
    }

    stage('Docker build container nginx'){
        steps {
            sh """
                docker build -t shaytanchik/nginx-jenkins:1.${env.BUILD_NUMBER} .
                docker push shaytanchik/nginx-jenkins:1.${env.BUILD_NUMBER}
                docker rmi -f shaytanchik/nginx-jenkins:1.${env.BUILD_NUMBER}
            """
       }
    }

    stage("Docker build container apache"){
        steps {
            dir ('apache') {
                sh """
                    docker build -t shaytanchik/apache-jenkins:1.${env.BUILD_NUMBER} .
                    docker push shaytanchik/apache-jenkins:1.${env.BUILD_NUMBER}
                    docker rmi -f shaytanchik/apache-jenkins:1.${env.BUILD_NUMBER}
                """
            }
        }
    }
    
    stage('deploy to ec2_server used ansible'){
            steps {
                ansiblePlaybook becomeUser: 'jenkins', credentialsId: 'ec2_ubuntu_server', inventory: '/home/jenkins/ansible/inventory', playbook: '/home/jenkins/ansible/playbook.yml', vaultTmpPath: '', extraVars: [BUILD_NUMBER: env.BUILD_NUMBER]
            }
        }
    }
}