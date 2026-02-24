pipeline {
    agent any
    environment {
        PHP_PATH = 'C:\\\\Users\\\\MB540WS\\\\.config\\\\herd-lite\\\\bin\\\\php.exe'
        COMPOSER_PATH = 'C:\\\\Users\\\\MB540WS\\\\.config\\\\herd-lite\\\\bin\\\\composer.bat'
        LOCAL_DEPLOYPATH = 'C:\\\\Vedant\\\\Git\\\\laravel_project'
    }
    stages {
        stage('Checkout') {
            steps {
                git branch: 'main', 
                    credentialsId: 'github-vedant',
                    url: 'https://github.com/vedantpatelp2006-del/my-laravel-app'
            }
        }

        stage('Dependencies') {
            steps {
			 bat 'dir composer.json'  // Verify in workspace
        bat 'dir vendor || echo NO VENDOR YET'
                bat "${COMPOSER_PATH} install --no-progress --no-interaction"
				bat 'dir vendor\\autoload.php'  // ✅ Exists
            }
        }
        stage('Environment') {
            steps {
                bat 'copy .env.example .env'
                bat "${PHP_PATH} artisan key:generate --force"
            }
        }		
		
        stage('Test DB') {
            steps {
                bat """
                if exist database\\database.sqlite del database\\database.sqlite
                ${PHP_PATH} artisan migrate:fresh --seed
                """
            }
        }
        stage('Test') {
            steps {
                bat "${PHP_PATH} artisan test"
            }
        }
        stage('Deploy Local') {
            steps {
                bat """
                xcopy /E /I /Y /H . "%LOCAL_DEPLOYPATH%" /exclude:exclude.txt
                """
            }
        }
    post {
        always {
            junit 'storage/logs/*.xml'
            cleanWs()
        }
    }
}
