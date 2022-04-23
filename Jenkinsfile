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
                    sh "podman build -t rajvemula/calculator ."
                }
          }
          stage ("Docker login") {
                steps {
                    withCredentials([usernamePassword(credentialsId: '325beef9-6ade-425b-8632-83f0264d1264', passwordVariable: 'Docker_Password', usernameVariable: 'Docker_User')]) {
                        sh "podman login --username $Docker_User --password $Docker_Password"
                    }
                }
          }

          stage ("podman push") {
                steps {
                       sh "podman push rajvemula/calculator"
                }
          }
     }
}