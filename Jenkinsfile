pipeline {
    agent any

    tools {
        jdk 'jdk21'
        maven 'maven3'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Install System Dependencies') {
            steps {
                script {
                    if (isUnix()) {
                        sh '''
                            echo "Installing system dependencies..."
                            sudo apt-get update
                            sudo apt-get install -y \
                                tesseract-ocr \
                                libtesseract-dev \
                                poppler-utils \
                                ffmpeg \
                                mediainfo \
                                imagemagick
                        '''
                    }
                }
            }
        }

        stage('Maven Build & Test') {
            steps {
                // 注意：这里使用 sh 而不是 bat (除非你确定是 Windows 环境)
                sh 'mvn clean package -U -Dmaven.test.failure.ignore=true'
            }
        }
    }

    post {
        success {
            echo '✅ 流水线执行成功！'
            archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            // 修复点1: 参数名改为 allowEmptyResults
            // 修复点2: 确保路径包含 target
            junit testResults: '**/target/surefire-reports/*.xml', allowEmptyResults: true
        }
        failure {
            echo '❌ 流水线执行失败！'
        }
        always {
            echo 'ℹ️ 构建结束。'
            // cleanWs() // 如果想保留工作区排查问题，可以注释掉这行
        }
    }
}
