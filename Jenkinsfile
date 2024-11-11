 pipeline
{
  agent none
 
  stages
  {
    stage('CLONE GIT REPOSITORY')
    {
      agent
      {
        label 'ubuntu-APPserver'
      }
      steps
      {
        checkout scm
      }
    }
 
    stage('SCA-SAST-SNYK-TEST')
    {
      agent
      {
        label 'ubuntu-APPserver'
      }
      steps
      {
        snykSecurity(
            snykInstallation: 'Synk',
            snykTokenId: 'snykid',
            severity: 'critical'
        )
      }
    }
    
    // from here down to next comment 
    stage('SonarQube Analysis') {
        agent {
            label 'ubuntu-APPserver'
        }
        steps {
            script {
                def scannerHome = tool 'SonarQubeScanner'
                withSonarQubeEnv('sonarqube') {
                    sh "${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=ChatApp \
                        -Dsonar.sources=."
                }
            }
        }
    }
    // this is what we edited
    
    stage('BUILD-AND-TAG')
    {
      agent
      {
        label 'ubuntu-APPserver'
      }
      steps
      {
         script
         {
            def app = docker.build("zimmate222/snakegame")
            app.tag("latest")
         }
      }
    }
 
    stage('POST-TO-DOCKERHUB')
    {
      agent
      {
        label 'ubuntu-APPserver'
      }
      steps
      {
         script
         {
            docker.withRegistry("https://registry.hub.docker.com", "zimmate")
            {
                def app = docker.image("zimmate222/snakegame")
                app.push("latest")
 
            }
           
         }
      }
    }
 
    stage('DEPLOYMENT')
    {
      agent
      {
        label 'ubuntu-APPserver'
      }
      steps
      {
        sh "docker-compose down"
        sh "docker-compose up -d"
      }
    }
 
   
   
  }
 
} 
