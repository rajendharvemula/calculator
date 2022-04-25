pipeline {
     agent any
     triggers {
          pollSCM('* * * * *')
     }
     stages {
          stage("Compile") {
               steps {
                    sh "./gradlew compileJava"
               }
          }
          stage("Unit test") {
               steps {
                    sh "./gradlew test"
               }
          }
          stage("Code coverage") {
               steps {
                    sh "./gradlew jacocoTestReport"
                    // HTML Publisher plugin required to run this
                    publishHTML (target: [
                         reportDir: 'build/reports/jacoco/test/html',
                         reportFiles: 'index.html',
                         reportName: "JaCoCo Report"
                    ])
                    sh "./gradlew jacocoTestCoverageVerification"
               }
          }
          stage("Static code analysis") {
               steps {
                    sh "./gradlew checkstyleMain"
                    publishHTML (target: [
                         reportDir: 'build/reports/checkstyle/',
                         reportFiles: 'main.html',
                         reportName: "Checkstyle Report"
                    ])
               }
          }
          stage("Package"){
                steps   {
                    sh "./gradlew build"
                }
          }
          stage ("Docker build") {
                steps {
                    sh "docker build -t rajvemula/calculator ."
                }
          }
          stage ("Docker login") {
                steps {
                    withCredentials([usernamePassword(credentialsId: 'ea6de769-ddb5-4461-a378-77c260b74286', passwordVariable: 'docker-password', usernameVariable: 'docker-user')]) {
                    {
                        sh "docker login --username $docker-user --password $docker-password"
                    }
                }
          }

          stage ("Docker push") {
                steps {
                       sh "docker push rajvemula/calculator"
                }
          }
     }
}