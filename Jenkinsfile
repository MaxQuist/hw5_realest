node('ubuntu-Appserver-3120')
{
    def app
    stage('Cloning Git')
    {
    /* Let's make sure we have the repository cloned to our workspace */
    checkout scm
    }
 
      stage('SCA-SAST-SNYK-TEST') 
      {
       agent 
       {
         label 'ubuntu-Appserver-3120'
       }
         snykSecurity(
            snykInstallation: 'Snyk',
            snykTokenId: 'snykid',
            severity: 'critical'
         )
       }
        stage('SonarQube Analysis')
    {
        agent {
            label 'ubuntu-Appserver-3120'
        }
        steps {
            script {
                def scannerHome = tool 'SonarQubeScanner'
                withSonarQubeEnv('sonarqube') {
                    sh "${scannerHome}/bin/sonar-scanner \
                    -Dsonar.projectKey=gameapp \
                    -Dsonar.sources=."
                }
            }
        }
    }
    stage('Build-and-Tag')
    {
        agent {
            label 'ubuntu-Appserver-3120'
        }
        steps {
            script {
                def app = docker.build("MaxQuist/hw5_realest")
                app.tag("latest")
            }
        }
    }
    stage('Post-to-dockerhub')
    {
        docker.withRegistry('https://registry.hub.docker.com', 'maxq')
        {
         app.push("latest")
        }
    }
 
    stage('Pull-image-server')
    {
        sh "docker-compose down"
        sh "docker-compose up -d"
    }
 
}

