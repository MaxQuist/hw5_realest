 pipeline
{
  agent none
 
  stages
  {
    stage('CLONE GIT REPOSITORY')
    {
      agent
      {
        label 'ubuntu-Appserver-3120'
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
        label 'ubuntu-Appserver-3120'
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
            label 'ubuntu-Appserver-3120'
        }
        steps {
            script {
                def scannerHome = tool 'SonarQubeScanner'
                withSonarQubeEnv('sonarqube') {
                    sh "${scannerHome}/bin/sonar-scanner \
                        -Dsonar.projectKey=hw5_realest \
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
        label 'ubuntu-Appserver-3120'
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
                def app = docker.image("MaxQuist/hw5_realest")
                app.push("latest")
 
            }
           
         }
      }
    }
 
    stage('DEPLOYMENT')
    {
      agent
      {
        label 'ubuntu-Appserver-3120'
      }
      steps
      {
        sh "docker-compose down"
        sh "docker-compose up -d"
      }
    }
 
   
   
  }
 
} 
