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
            dir ('nginx') {
                sh """
                    docker build -t shaytanchik/nginx-jenkins-d:1.${env.BUILD_NUMBER} . 
                    docker push shaytanchik/nginx-jenkins-d:1.${env.BUILD_NUMBER}
                    docker rmi -f shaytanchik/nginx-jenkins-d:1.${env.BUILD_NUMBER}
                """
           }
        }
    }
    stage("Docker build container apache"){
        steps {
            dir ('apache') {
                sh """
                    docker build -t shaytanchik/apache-jenkins-d:1.${env.BUILD_NUMBER} .
                    docker push shaytanchik/apache-jenkins-d:1.${env.BUILD_NUMBER}
                    docker rmi -f shaytanchik/apache-jenkins-d:1.${env.BUILD_NUMBER}
                """
            }
        }
    }
    
    stage('deploy to ec2_server used ansible'){
            steps {
                ansiblePlaybook becomeUser: 'ubuntu', credentialsId: 'ec2_ubuntu_server', inventory: '/home/jenkins/ansible/hosts.txt', playbook: '/home/jenkins/ansible/playbook.yml', vaultTmpPath: '', extraVars: [BUILD_NUMBER: env.BUILD_NUMBER]
            }
        }
    }
}
