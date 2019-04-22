pipeline {
  agent any
  stages {
    stage('Build') {
      steps {
        echo 'Running build automation'
        sh './gradlew build --no-daemon'
        archiveArtifacts 'dist/trainSchedule.zip'
      }
    }
    stage('DeployToStaging') {
      when {
        branch 'master'
      }
      steps {
        withCredentials(bindings: [usernamePassword(credentialsId: '  webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
          sshPublisher(failOnError: true, publishers: [
                                        sshPublisherDesc(
                                              configName: 'alpha',
                                              sshCredentials: [
                                                    username: "$USERNAME",
                                                    encryptedPassphrase: "$USERPASS"
                                                ], 
                                                transfers: [
                                                      sshTransfer(
                                                            sourceFiles: 'dist/trainSchedule.zip',
                                                            removePrefix: 'dist/',
                                                            remoteDirectory: '/tmp'
                                                        )
                                                    ]
                                                )
                                            ])
                  }

                }
              }
              stage('DeployToProduction') {
                when {
                  branch 'master'
                }
                steps {
                  input 'Does the staging environment look OK?'
                  milestone 1
                  withCredentials(bindings: [usernamePassword(credentialsId: '	webserver_login', usernameVariable: 'USERNAME', passwordVariable: 'USERPASS')]) {
                    sshPublisher(failOnError: true, publishers: [
                                                  sshPublisherDesc(
                                                        configName: 'beta',
                                                        sshCredentials: [
                                                              username: "$USERNAME",
                                                              encryptedPassphrase: "$USERPASS"
                                                          ], 
                                                          transfers: [
                                                                sshTransfer(
                                                                      sourceFiles: 'dist/trainSchedule.zip',
                                                                      removePrefix: 'dist/',
                                                                      remoteDirectory: '/tmp'
                                                                  )
                                                              ]
                                                          )
                                                      ])
                            }

                          }
                        }
                      }
                    }