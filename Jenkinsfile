pipeline{
    tools{
        jdk 'Java_Home'
        maven 'myMaven'
    }
	agent any
      stages{
           stage('Checkout'){
	    
               steps{
		 echo 'cloning..'
                 git 'https://github.com/timjar3/Pro_2_CICD_GCP.git'
              }
          }
          stage('Compile'){
             
              steps{
                  echo 'compiling..'
                  sh 'mvn compile'
	      }
          }
          stage('CodeReview'){
		  
              steps{
		    
		  echo 'codeReview'
                  sh 'mvn pmd:pmd'
              }
          }
           stage('UnitTest'){
		  
              steps{
	         
                  sh 'mvn test'
              }
               post {
               success {
                   junit 'target/surefire-reports/*.xml'
               }
           }	
          }
           stage('MetricCheck'){
              
              steps{
                  sh 'mvn cobertura:cobertura -Dcobertura.report.format=xml'
              }
               post {
               success {
	           cobertura autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: 'target/site/cobertura/coverage.xml', conditionalCoverageTargets: '70, 0, 0', failUnhealthy: false, failUnstable: false, lineCoverageTargets: '80, 0, 0', maxNumberOfBuilds: 0, methodCoverageTargets: '80, 0, 0', onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false                  
               }
           }		
          }
          stage('Package'){
		  
              steps{
		  
                  sh 'mvn package'
              }
          }
	   stage('docker build'){
        steps{
            sh "sudo docker build -t timjar3/pubrepo1:$BUILD_NUMBER ."
        }
    }
    
          stage('dockerhub_push'){
		 
                 steps{
            sh "sudo docker push timjar3/pubrepo1:$BUILD_NUMBER"  
          
	    }
        }
	      
	  stage('Deploy to Kubernetes Cluster on GKE'){
		      kubernetesDeploy(
                  configs: 'Deploy_Serv_Vol.yml',
                  kubeconfigID: 'KUBERNETES_CLUSTER_CONFIG',
                  enableConfigSubstitution: true
              )
	    }
	  
    }
}
