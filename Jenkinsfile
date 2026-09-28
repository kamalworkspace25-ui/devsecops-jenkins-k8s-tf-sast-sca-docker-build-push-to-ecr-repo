pipeline {
  agent any
  tools { 
        maven 'Maven_3_5_2'  
    }
   stages{
		// 1 - Sonar SAST Scan //	   
		stage('CompileandRunSonarAnalysis') {
      steps {
        withCredentials([string(credentialsId: 'sonarcloud-token', variable: 'SONAR_TOKEN')]) {
          sh '''
            mvn clean verify org.sonarsource.scanner.maven:sonar-maven-plugin:5.8.0.7211:sonar -Dsonar.host.url=https://sonarcloud.io -Dsonar.organization=dsobuggyapp -Dsonar.projectKey=dsobuggyapp -Dsonar.token=$SONAR_TOKEN
          '''
        }
      }
    }
		// 2 - Snyk SCA Scan //
    stage('RunSCAAnalysisUsingSnyk') {
      steps {
        withCredentials([string(credentialsId: 'SNYK_TOKEN', variable: 'SNYK_TOKEN')]) {
          sh 'mvn snyk:test -fn'
        }
      }
    }
		// 3 - Docker Image //
	stage('Build') { 
            steps { 
               withDockerRegistry([credentialsId: "dockerlogin", url: ""]) {
                 script{
                 app =  docker.build("asg")
                 }
               }
            }
    }

	stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://429128461530.dkr.ecr.us-east-1.amazonaws.com', 'ecr:us-east-1:aws-credentials') {
                    app.push("latest")
                    }
                }
            }
    	}
	    
  }
}
