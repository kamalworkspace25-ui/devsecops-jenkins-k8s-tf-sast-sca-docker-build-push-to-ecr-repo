pipeline {
  agent any
  tools { 
        maven 'Maven_3_8_4'  
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
                 app =  docker.build("dsoecrrepo")
                 }
               }
            }
    }

	stage('Push') {
            steps {
                script{
                    docker.withRegistry('https://074832285117.dkr.ecr.us-west-2.amazonaws.com', 'ecr:us-west-2:aws-credentials') {
                    app.push("latest")
                    }
                }
            }
    	}
	    
  }
}
