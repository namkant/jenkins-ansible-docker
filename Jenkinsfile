node {

   def registryProjet='registry.gitlab.com/namkant/wartest'
   def IMAGE="${registryProjet}:version-${env.BUILD_ID}"

    stage('Build - Clone') {
        git 'https://github.com/namkant/war-build-docker.git'
    }
    stage('Buid - Maven package') {
        sh 'mvn package'
    }
    def img = stage('Build') {
          docker.build("$IMAGE",  '.')
    }
    stage('Build - Test') {
          img.withRun("--name run-$BUILD_ID -p 8081:8080") { c ->
            sh 'docker ps'
            sh 'netstat -ntaup'
            sh 'sleep 10s'
            sh 'curl 192.168.126.128:8081'
            sh 'docker ps'
          }
    }
    stage('Push') {
          docker.withRegistry('https://registry.gitlab.com', 'reg1') {
               img.push 'latest'
               img.push()
          }
    }
        stage('Deploy-Clone') {
          git 'https://github.com/namkant/jenkins-ansible-docker.git'
    }
    stage('Deploy-End') {
      ansiblePlaybook (
          colorized: true,
          become: true,
          playbook: 'playbook.yml',
          inventory: 'hosts.yml',
          extras: "--extra-vars 'image=$IMAGE'"
        )
    }
}


