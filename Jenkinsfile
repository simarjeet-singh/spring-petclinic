    node {
      stage 'Checkout'
      checkout([$class: 'GitSCM', 
      branches: [[name: '*/master']], 
      doGenerateSubmoduleConfigurations: false, 
      extensions: [[$class: 'CleanCheckout']], 
      submoduleCfg: [], 
      userRemoteConfigs: [[credentialsId: 'cc60faf2-b4fd-4348-90be-f9eb93de3be3', url: 'https://github.com/simarjeet-singh/spring-petclinic.git']]
    ])
}
   node {
    stage 'SonarQube Anaylsis'
    //def scannerHome = tool 'sonar-scanner';
      sh '''
       #   /home/tomcat/.jenkins/tools/hudson.plugins.sonar.SonarRunnerInstallation/sonar-scanner/bin/sonar-scanner -Dsonar.projectKey=spring-petclinic -Dsonar.projectName=spring-petclinic -Dsonar.projectVersion=1.0 -Dsonar.sources=. -Dsonar.java.binaries=./src
            echo "Check reports on URL: http://10.207.146.151:9000/dashboard?id=spring-petclinic"
         '''
        }
        node {
          stage("Build") {
                sh '''
                    mvn package -DskipTests=true
                   '''
          }
        }
        node {
          stage("Test Cases") {
                sh '''
                    mvn test
                   '''
          }
        }
        node {
          stage("Publish to Nexus") {
            nexusArtifactUploader artifacts: [[artifactId: 'spring-petclinic-snapshot', classifier: '', file: '/home/tomcat/.jenkins/workspace/Build Pipeline/target/devops-petclinic-2.1.0.BUILD-SNAPSHOT.jar', type: 'jar']], credentialsId: 'Nexus-Creds', groupId: 'nexus', nexusUrl: '10.207.146.152:8081', nexusVersion: 'nexus3', protocol: 'http', repository: 'spring-petclinic-snapshot', version: '1.0'
          }
        }
        
        node {
            stage("Dev Push") {
                build job: 'Dev'
			    }
        }
        
        node {
            stage("Approval for Stage") {
             timeout(time: 5, unit: 'DAYS')
             input message: 'Proceed to deploy on Stage', submitter: 'test'
				  }
        }
        node {
          stage("Stage Push") {
           build job: 'Stage'
				  }
        }
        node {
          stage("Approval for Prod") {
           timeout(time: 5, unit: 'DAYS')
           input message: 'Proceed to deploy on Stage', submitter: 'test1'
				  }
        }
        node {
          stage("Prod Push") {
           build job: 'Prod'
				  }
        }
        
