pipeline{
    agent any
    stages{
        stage('Checkout'){
            steps{

                checkout scm
            }
        }
        stage('Build'){
            steps{
                sh './gradlew build'
            }
        }
        stage('Dependency Check'){
            steps{
                script{
                dependencyCheckAnalyzer datadir: 'dependency-check-data', isFailOnErrorDisabled: true, hintsFile: '', includeCsvReports: false, includeHtmlReports: false, includeJsonReports: false, isAutoupdateDisabled: false, outdir: '', scanpath: '', skipOnScmChange: false, skipOnUpstreamChange: false, suppressionFile: '', zipExtensions: ''

                dependencyCheckPublisher canComputeNew: false, defaultEncoding: '', healthy: '', pattern: '', unHealthy: ''

                archiveArtifacts allowEmptyArchive: true, artifacts: '**/dependency-check-report.xml', onlyIfSuccessful: true
                }
            }

        }

        stage('SonarQube Scan'){
            environment {
                scannerHome = tool 'SonarQube'
            }
            steps {
                script{
                    try {
                        withSonarQubeEnv('SonarQubeDocker') {
                            sh "${scannerHome}/bin/sonar-scanner -Dsonar.login=7670abce644c280edbfe4be5e7f4d29906e384b9 -Dsonar.projectKey=SpringBoot -Dsonar.java.binaries=./build/libs/"
                        }
                        sleep(60)
                        timeout(time: 10, unit: 'MINUTES') {
                            waitForQualityGate abortPipeline: true
                        }
                        currentBuild.result = 'SUCCESS'
                    } catch (Exception e) {
                        currentBuild.result = 'FAILURE'
                    }
                }


            }

        }
    }
}
