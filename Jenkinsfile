pipeline {
    agent any

    tools {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "Maven3"
    }

      //  S3_BUCKET_NAME = sh "date '+%Y%m%d%H%M'.zip" 스크립트에서 변수 선언은 제한되어 있다 https://kwonnam.pe.kr/wiki/ci/jenkins/pipeline


    stages {

        stage('prepare') {
            steps {
                echo '${env.JOB_NAME}'
                 sh 'rm -rf deploy'
                 echo 'prepare'
                 git credentialsId: "GIT_ACCOUNT", url: 'https://github.com/zombie828/targetA'
                 sh  'ls -al'
            }
        }


        stage('Build') {
            steps {

                // Run Maven on a Unix agent.
                sh "mvn package -Dmaven.test.skip=true"

                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }

            post {
                // If Maven was able to run the tests, even if some of the test
                // failed, record the test results and archive the jar file.
                success {

                    sh "echo success"
                    // junit '**/target/surefire-reports/TEST-*.xml'
                    // archiveArtifacts 'target/*.jar'
                }
            }
        }

        stage('deploy') {
            steps {
                sh 'mkdir deploy'
                sh 'mv target/*.war deploy'
                sh 'mv appspec.yml deploy'

                dir('deploy') {
                    sh 'ls -al'
                    sh 'zip "ttt.zip" ./*'
                   // sh 'aws s3 cp ./*.zip s3://code-deploy-test3212'
                    withAWS(credentials: 'DH_AWS', region: 'ap-northeast-2') {
s3Upload(file:'ttt.zip', bucket:'solnae-test')
}
                }
                // sh 'aws deploy create-deployment --application-name deploy-test --deployment-config-name test-config --deployment-group-name code-deploy-group --s3-location bucket=code-deploy-test3212,key=warbuildfile.zip,bundleType=zip'

            }
            post {
                success {
                    echo 'deploy success'
                }

                failure {
                    echo 'deploy build failed'
                }
            }
        }

        stage('delete') {
            steps {
                sh 'rm -rf deploy'

            }
        }

    }
}
