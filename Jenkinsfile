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
                    withCredentials([usernamePassword(credentialsId: 'd9acda90-d0de-433f-91f1-65624abe9aef', passwordVariable: 'dockerPassword', usernameVariable: 'dockerUser')]) {
                        sh "docker login --username $dockerUser --password $dockerPassword"
                    }
                }
          }
          stage ("Docker push") {
                steps {
                       sh "docker push rajvemula/calculator"
                }
          }
         stage ("Deploy to staging") {
                steps {
                    sh "docker run -d -p 8765:8080 --name calculator rajvemula/calculator"
                }
         }
         stage ("Acceptence stage") {
                steps {
                    sleep 60
                   // sh "./acceptance_test.sh"
                }
         }
         post {
              always {
                    // cleanup the calculator application
                   sh "docker stop calculator"
              }
         }
     }
}
