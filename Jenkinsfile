pipeline {
    agent {
        docker {
            image 'maven:3.3.3'
            args '-v //C/Users/tzs91/.m2:/root/.m2'
        }
    }
    stages {
        stage('Compile') {
            steps {
                sh 'mvn -B clean compile'
            }
        }
        stage('Test') {
            steps {
                sh 'mvn test'
            }
            post {
                always {
                    junit 'target/surefire-reports/*.xml'
                }
            }
        }
        stage('Site') {
            steps {
                sh 'mvn site'
            }
            post {
                always {
                    cobertura autoUpdateHealth: false, autoUpdateStability: false, coberturaReportFile: 'target/site/cobertura/coverage.xml', conditionalCoverageTargets: '70, 0, 0', failUnhealthy: false, failUnstable: false, lineCoverageTargets: '80, 0, 0', maxNumberOfBuilds: 0, methodCoverageTargets: '80, 0, 0', onlyStable: false, sourceEncoding: 'ASCII', zoomCoverageChart: false
                    publishHTML([allowMissing: false, alwaysLinkToLastBuild: false, keepAll: false, reportDir: 'target/site', reportFiles: 'index.html', reportName: 'HTML Report', reportTitles: 'site report'])
                    findbugs('**/findbugsXml.xml', false) {
                        healthLimits(3, 20)
                        thresholdLimit('high')
                        defaultEncoding('UTF-8')
                        canRunOnFailed(true)
                        useStableBuildAsReference(true)
                        useDeltaValues(true)
                        computeNew(true)
                        shouldDetectModules(true)
                        thresholds(
                                unstableTotal: [all: 1, high: 2, normal: 3, low: 4],
                                failedTotal: [all: 5, high: 6, normal: 7, low: 8],
                                unstableNew: [all: 9, high: 10, normal: 11, low: 12],
                                failedNew: [all: 13, high: 14, normal: 15, low: 16]
                        )
                        }
                }
            }
        }
        stage('tzs check') {
            steps {
                input "Does the staging environment look ok?"
            }
        }
        stage('Deliver') {
            steps {
                sh './jenkins/scripts/deliver.sh'
            }
        }
    }
}