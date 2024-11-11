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
            snykInstallation: 'Snyk',
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
                def scannerHome = tool 'SonarqubeScanner'
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
            def app = docker.build("MaxQuist/hw5_realest")
            app.tag("latest")
         }
      }
    }
 
    stage('POST-TO-DOCKERHUB')
    {
      agent
      {
        label 'ubuntu-Appserver-3120'
      }
      steps
      {
         script
         {
            docker.withRegistry("https://registry.hub.docker.com", "maxq")
            {
                def app = docker.image("xamq/hw5_realest")
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
