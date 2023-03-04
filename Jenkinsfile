pipeline{
    agent any
    stages{
        stage("Pull Latest Image"){
                steps{
                    sh "docker pull vijayendra1/saucedemo"
                }
        }
        stage("Start Grid"){
                steps{

                    //timeout(time: 5, unit: 'MINUTES') {
                        script{
                            waitUntil {
                                  try {
                                            def r = sh script:   "docker-compose up -d --scale chrome=0 --scale firefox=0 --scale edge=0", returnStatus: true
                                            return true
                                        }
                                        catch (exception) {
                                             error("Start Grid failed .....")
                                             return false
                                        }
                            }
                        }
                    //}
                }
        }
        stage("Run Test"){
                steps{
                    script{
                            waitUntil {
                                        try {
                                            def r = sh script:   "docker-compose up --scale chrome=2 --scale firefox=0 --scale edge=0 --abort-on-container-exit", returnStatus: true
                                            return true
                                        }
                                        catch (exception) {
                                             error("Test Run failed .....")
                                             return false
                                        }
                            }
                    }
                }
        }
        stage('Open Report') {
            steps {
                script {
                        allure([
                                includeProperties: false,
                                jdk: '',
                                properties: [],
                                reportBuildPolicy: 'ALWAYS',
                                results: [[path: 'test_report']]
                        ])
                }
            }
        }
    }
	post{
	    always{
	        sh "pwd"
	    	archiveArtifacts artifacts: 'test_report/**'
	    	sh "docker-compose down"
		    //sh "open ."
	    }
	}
}