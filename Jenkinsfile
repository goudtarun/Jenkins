pipeline {
    agent { 
        label "agent-two"
    }
    stages {
        stage ("code"){
            steps {
                dir ("ansible"){
                    echo "Cloning the code from github"
                    git url: "https://github.com/goudtarun/ansible.git", branch: "main"
                    sh "rsync -av /home/ubuntu/workspace/First-pipeline/ansible/ /home/ubuntu/ansible"
                    echo "The repo is cloned succesfully"
                }
            }
        }
        stage ("test"){
            steps {
                sh "cd /home/ubuntu/ansible/"
                sh "sudo chmod 600 /home/ubuntu/ansible/tarun.pem"
                sh "ansible test -m ping"
            }
        }
        stage ("build"){
            steps{
                sh "ansible-playbook firstplaybook.yml"
                sh "echo playbook execution successful"
            }
        }
        stage ("docker-pull"){
            steps{
                dir("docker"){
                    git url: "https://github.com/goudtarun/docker.git", branch: "main"
                    sh "rsync -av /home/ubuntu/workspace/First-pipeline/docker/beginner/ /home/ubuntu/docker"
                    sh "pwd"
                }
            }
        }
        stage ("docker-build"){
            steps{
                sh "cd /home/ubuntu/docker/"
                sh "docker build -t dockci:v1 /home/ubuntu/docker/"
            }
        }
        stage ("Docker-deploy"){
            steps{
                sh "docker container run -itd -p 8081:80 --name firstcidoc dockci:v1"
            }
        }
        
        stage ("Docker-push"){
            steps{
                withCredentials ([usernamePassword(credentialsId : 'dockercred', usernameVariable: 'Dockerhubuser', passwordVariable: 'Dockerhubpassword')]){
                    sh "docker login -u ${env.Dockerhubuser} -p ${env.Dockerhubpassword}"
                    sh "docker image tag dockci:v1 ${env.Dockerhubuser}/dockci:v1"
                    sh "docker push  ${env.Dockerhubuser}/dockci:v1"
                }
            }
        }
    }
}
