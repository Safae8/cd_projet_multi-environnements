pipeline {
    agent any

    environment {
        PATH = "C:\\Users\\pc\\AppData\\Local\\Programs\\Python\\Python313;C:\\Users\\pc\\AppData\\Local\\Programs\\Python\\Python313\\Scripts;${env.PATH}"
        NEXUS_URL      = 'http://localhost:8081/repository/python-release/Release.zip'
        NEXUS_USER     = 'admin'
        NEXUS_PASSWORD = 'safae123'
    }

    stages {
        stage('Nettoyage du Workspace') {
            steps {
                deleteDir()
            }
        }

        stage('Téléchargement de la Release') {
            steps {
                bat '''
                curl -f -u %NEXUS_USER%:%NEXUS_PASSWORD% ^
                -o Release.zip ^
                %NEXUS_URL%
                '''
            }
        }

        stage('Vérification du téléchargement') {
            steps {
                bat '''
                if not exist Release.zip (
                    echo ERREUR : Release.zip introuvable
                    exit /b 1
                )
                dir
                '''
            }
        }

        stage('Décompression de la Release') {
            steps {
                bat '''
                powershell -Command "Expand-Archive -Path Release.zip -DestinationPath Release -Force"
                '''
            }
        }

        stage('Vérification de la Release') {
            steps {
                bat '''
                if not exist Release (
                    echo ERREUR : dossier Release introuvable
                    exit /b 1
                )
                dir Release
                '''
            }
        }

        stage('Déploiement DEV') {
            steps {
                bat '''
                if not exist C:\\DEV mkdir C:\\DEV
                xcopy Release C:\\DEV /E /I /Y
                '''
            }
        }

        stage('Vérification DEV') {
            steps {
                bat '''
                echo ===== CONTENU DEV =====
                dir C:\\DEV
                if exist C:\\DEV\\calculator.py (
                    python C:\\DEV\\calculator.py
                )
                '''
            }
        }

        stage('Déploiement TEST') {
            steps {
                bat '''
                if not exist C:\\TEST mkdir C:\\TEST
                xcopy Release C:\\TEST /E /I /Y
                '''
            }
        }

        stage('Tests fonctionnels') {
            steps {
                bat '''
                if exist C:\\TEST\\test_calculator.py (
                    cd /d C:\\TEST
                    python -m pytest -v -p no:cacheprovider
                ) else (
                    echo Aucun test fonctionnel trouve.
                )
                '''
            }
        }

        stage('Déploiement UAT') {
            steps {
                bat '''
                if not exist C:\\UAT mkdir C:\\UAT
                xcopy Release C:\\UAT /E /I /Y
                '''
            }
        }

        stage('Déploiement STAGING') {
            steps {
                bat '''
                if not exist C:\\STAGING mkdir C:\\STAGING
                xcopy Release C:\\STAGING /E /I /Y
                '''
            }
        }

        stage('Déploiement PRODUCTION') {
            steps {
                bat '''
                if not exist C:\\PRODUCTION mkdir C:\\PRODUCTION
                xcopy Release C:\\PRODUCTION /E /I /Y
                '''
            }
        }
    }

    post {
        success {
            echo '===================================='
            echo 'Déploiement terminé avec succès.'
            echo '===================================='
        }
        failure {
            echo '===================================='
            echo 'Le pipeline a échoué.'
            echo 'Vérifier les logs Jenkins.'
            echo '===================================='
        }
        always {
            archiveArtifacts artifacts: 'Release.zip', fingerprint: true
        }
    }
}