pipeline {
    agent any

    environment {
        // Variables d'environnement nécessaires
        ID_DOCKER = "${ID_DOCKER_PARAMS}"
        IMAGE_NAME = "jk-flask"
        IMAGE_TAG = "latest"
        DOCKERHUB_PASSWORD = "${DOCKERHUB_PASSWORD_PSW}"
        RENDER_API_TOKEN = credentials('RENDER_API_TOKEN')
        RENDER_SERVICE_ID = "srv-cockhsa1hbls73csl2o0"
        RENDER_DEPLOY_HOOK_URL_TP2 = credentials('RENDER_DEPLOY_HOOK_URL_TP2')
    }

    triggers {
        // Vérifie le dépôt pour des changements toutes les 2 minutes
        pollSCM('H/2 * * * *')
    }

    stages {
        stage('Build image') {
            steps {
                script {
                    sh 'docker build -t ${ID_DOCKER}/${IMAGE_NAME}:${IMAGE_TAG} .'
                }
            }
        }

        stage('Test image') {
            steps {
                script {
                    echo "Exécution des tests"
                }
            }
        }

        stage('Login and Push Image on docker hub') {
            steps {
                script {
                    sh '''
                        echo ${DOCKERHUB_PASSWORD} | docker login -u ${ID_DOCKER} --password-stdin
                        docker push ${ID_DOCKER}/${IMAGE_NAME}:${IMAGE_TAG}
                    '''
                }
            }
        }

        // stage('Trigger Deploy to Render') {
        //     steps {
        //         // Utilisez withCredentials pour accéder à l'URL du webhook de manière sécurisée
        //         withCredentials([string(credentialsId: 'RENDER_DEPLOY_HOOK_URL_TP2', variable: 'DEPLOY_HOOK_URL')]) {
        //             script {
        //                 // Envoi d'une requête POST au webhook de déploiement
        //                 sh "curl -X POST ${DEPLOY_HOOK_URL}"
        //             }
        //         }
        //     }
        // }




    }

    post {
        success {
            mail to: 'killian.lecornet@ynov.com',
                 subject: "Succès du Pipeline ${env.JOB_NAME} ${env.BUILD_NUMBER}",
                 body: "Le pipeline a réussi. L'application a été déployée sur Render."
        }
        failure {
            mail to: 'killian.lecornet@ynov.com',
                 subject: "Échec du Pipeline ${env.JOB_NAME} ${env.BUILD_NUMBER}",
                 body: "Le pipeline a échoué. Veuillez vérifier Jenkins pour plus de détails."
        }
    }
}
