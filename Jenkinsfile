pipeline {

    environment {
      
      //----------------UPDATE HERE--------------------------------------------
      // CloudHub (CH) | Mule China Onprem (ARM_C) | Mule EU Onprem (ARM_EU)
      DEPLOY_TARGET = 'CH'
      //CloudHub Parameters
      CH_REGION = 'eu-west-1'
      CH_WORKERS = '1'
      CH_WORKER_TYPE = 'MICRO'
      CH_ORG = 'KONE Corporation'
      
      //-----------------END OF UPDATE------------------------------------------
      
      
      ANYPOINT = credentials("ANYPOINT")
      //MSTEAMS = credentials("MSTEAMS")
      //CloudHub Enviroments
      CH_ENV_DEV = 'DEV'
      CH_ENV_QA = 'QA'
      CH_ENV_PROD = 'Production'
      //China Onprem Enviroments
      ARM_C_ENV_DEV = 'DEV'
      ARM_C_ENV_QA = 'QA'
      ARM_C_ENV_PROD = 'PRODUCTION'
      API_NAME = readMavenPom().getArtifactId()
      VERSION = readMavenPom().getVersion()
    }

    agent any
    options {
        office365ConnectorWebhooks([[name: 'mule-jenkins-connect', notifyBackToNormal: true, notifyFailure: true, notifyUnstable: true, url: 'https://outlook.office.com/webhook/64805a1d-9de2-48ae-b900-0adbb9f22248@22ddce65-9770-4012-94f0-da65409d3999/JenkinsCI/86a097bf52844bf785930150473aa4fb/82ed54e0-8f97-4b40-96f8-94a9a532f7d0']])
    }
    stages {
        stage('Example') {
            steps {
                echo "Hello"
            }
        }
        /*stage('Unit Test') {
            steps {
                sh 'mvn clean test'
                publishHTML([allowMissing: false, alwaysLinkToLastBuild: true, keepAll: false, reportDir: 'target\\site\\munit\\coverage', reportFiles: 'summary.html', reportName: 'Code Coverage', reportTitles: ''])
            }
        }
       stage('Deploy CloudHub - DEV[feature*]') {
               when {
                allOf { branch 'feature*'; environment name: 'DEPLOY_TARGET', value: 'CH' }
               }
               environment {
                 DEPLOY_TO_CH_ENV = "${env.CH_ENV_DEV}"
                 DEPLOY_TO_CH_REGION = "${env.CH_REGION}"
                 DEPLOY_TO_CH_WORKERS = "${env.CH_WORKERS}"
                 DEPLOY_TO_CH_WORKER_TYPE = "${env.CH_WORKER_TYPE}"
                 BUILD_NAME = "${env.API_NAME}-${env.BRANCH_NAME}"
               }
               steps {
                   sh "mvn clean package deploy -P cloudhub -DmuleDeploy -DCH_ORG=\"$CH_ORG\" -DANYPOINT_USERNAME=$ANYPOINT_USR -DANYPOINT_PASSWORD=$ANYPOINT_PSW -DCH_ENV=${env.DEPLOY_TO_CH_ENV} -DCH_RGN=${env.DEPLOY_TO_CH_REGION} -DCH_WORKERTYPE=${env.DEPLOY_TO_CH_WORKER_TYPE} -DCH_WORKERS=${env.DEPLOY_TO_CH_WORKERS} -DBUILD_NAME=${env.BUILD_NAME}"
               }
       }
       stage('Deploy CloudHub - QA[develop]') {
               when {
                allOf { branch 'develop'; environment name: 'DEPLOY_TARGET', value: 'CH' }
               }
               environment {
                 DEPLOY_TO_CH_ENV = "${env.CH_ENV_QA}"
                 DEPLOY_TO_CH_REGION = "${env.CH_REGION}"
                 DEPLOY_TO_CH_WORKERS = "${env.CH_WORKERS}"
                 DEPLOY_TO_CH_WORKER_TYPE = "${env.CH_WORKER_TYPE}"
                 BUILD_NAME = "${env.API_NAME}-${env.VERSION}"
               }
               steps {
               		 script {
                    		env.D_BUILD_NAME = "${env.BUILD_NAME}".replace('.', '-')
                		 }
                      sh "mvn clean package deploy -P cloudhub -DmuleDeploy -DCH_ORG=\"$CH_ORG\" -DANYPOINT_USERNAME=$ANYPOINT_USR -DANYPOINT_PASSWORD=$ANYPOINT_PSW -DCH_ENV=${env.DEPLOY_TO_CH_ENV} -DCH_RGN=${env.DEPLOY_TO_CH_REGION} -DCH_WORKERTYPE=${env.DEPLOY_TO_CH_WORKER_TYPE} -DCH_WORKERS=${env.DEPLOY_TO_CH_WORKERS} -DBUILD_NAME=${env.D_BUILD_NAME}"
               }
       }
       stage('Deploy CloudHub - QA[release*]') {
               when {
                allOf { branch 'release*'; environment name: 'DEPLOY_TARGET', value: 'CH' }
               }
               environment {
                 DEPLOY_TO_CH_ENV = "${env.CH_ENV_QA}"
                 DEPLOY_TO_CH_REGION = "${env.CH_REGION}"
                 DEPLOY_TO_CH_WORKERS = "${env.CH_WORKERS}"
                 DEPLOY_TO_CH_WORKER_TYPE = "${env.CH_WORKER_TYPE}"
               }
               steps {
               		  script {
               		    if ("${env.VERSION}".contains('-')) {
            					env.VERSION_NO_SS = "${env.VERSION}".substring(0, "${env.VERSION}".indexOf('-'))
            					env.BUILD_NAME = "${env.CH_ENV_QA}-${env.API_NAME}-${env.VERSION_NO_SS}"
                                env.D_BUILD_NAME = "${env.BUILD_NAME}".replace('.', '-')
          				} else {
          				        env.BUILD_NAME = "${env.CH_ENV_QA}-${env.API_NAME}-${env.VERSION}"
            					env.D_BUILD_NAME = "${env.BUILD_NAME}".replace('.', '-')
          				}             		
                		  }
                      sh "mvn clean package deploy -P cloudhub -DmuleDeploy -DCH_ORG=\"$CH_ORG\" -DANYPOINT_USERNAME=$ANYPOINT_USR -DANYPOINT_PASSWORD=$ANYPOINT_PSW -DCH_ENV=${env.DEPLOY_TO_CH_ENV} -DCH_RGN=${env.DEPLOY_TO_CH_REGION} -DCH_WORKERTYPE=${env.DEPLOY_TO_CH_WORKER_TYPE} -DCH_WORKERS=${env.DEPLOY_TO_CH_WORKERS} -DBUILD_NAME=${env.D_BUILD_NAME}"
               }
       }
       stage('Deploy CloudHub - PRODUCTION[release*]') {
              when {
                allOf { branch 'release*'; environment name: 'DEPLOY_TARGET', value: 'CH' }
              }
              environment {
                 DEPLOY_TO_CH_ENV = "${env.CH_ENV_PROD}"
                 DEPLOY_TO_CH_REGION = "${env.CH_REGION}"
                 DEPLOY_TO_CH_WORKERS = "${env.CH_WORKERS}"
                 DEPLOY_TO_CH_WORKER_TYPE = "${env.CH_WORKER_TYPE}"
               }
              steps {
                script {
                    def proceed = true
                    env.MAJ_VERSION = "${env.VERSION}".substring(0, "${env.VERSION}".indexOf('.'))
                    env.BUILD_NAME = "${env.API_NAME}-v${env.MAJ_VERSION}"
                    try {
                        timeout(time: 15, unit: 'SECONDS') {
                            input(message: 'Deploy to production?')
                        }
                    } catch (err) {
                        echo 'Skipped production deployment'
                        proceed = false
                    }
                    if(proceed) {
                        echo 'Deploying to production'
                    }
                }
                sh "mvn clean package deploy -P cloudhub -DmuleDeploy -DCH_ORG=\"$CH_ORG\" -DANYPOINT_USERNAME=$ANYPOINT_USR -DANYPOINT_PASSWORD=$ANYPOINT_PSW -DCH_ENV=${env.DEPLOY_TO_CH_ENV} -DCH_RGN=${env.DEPLOY_TO_CH_REGION} -DCH_WORKERTYPE=${env.DEPLOY_TO_CH_WORKER_TYPE} -DCH_WORKERS=${env.DEPLOY_TO_CH_WORKERS} -DBUILD_NAME=${env.BUILD_NAME}"
              }
        }*/
        /*stage('Deploy ARM') {
            steps {
                sh "mvn deploy -P arm -DANYPOINT_USERNAME=$ANYPOINT_USR -DANYPOINT_PASSWORD=$ANYPOINT_PSW -DARM_ENV=$DEPLOY_TO -DARM_TARGET=vm-mule -DARM_TARGET_TYPE=server"
            }
        }
        stage('Deploy Standalone') {
            steps {
                sh "mvn deploy -P standalone -Dmule.home=/home/sab/Downloads/opt/mule3"
            }
        }
        stage('Artifact') {
            when {
                branch 'master'
            }
            steps {
                    echo 'Push to Artifactory'
            }
        }*/
    }
}