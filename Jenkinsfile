pipeline {
    agent any

    tools {
        jdk 'jdk21' // 确保你的 Jenkins 全局工具配置里有叫 jdk21 的工具
        maven 'maven3'
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        // 【新增】环境准备阶段：安装缺失的系统库
        stage('Install System Dependencies') {
            steps {
                script {
                    // 判断操作系统，通常 Jenkins Agent 是 Linux
                    if (isUnix()) {
                        sh '''
                            echo "正在安装 Teedy 运行所需的系统依赖..."
                            sudo apt-get update
                            # 核心修复：安装 poppler-utils (PDF处理) 和 tesseract (OCR)
                            sudo apt-get install -y \
                                tesseract-ocr libtesseract-dev \
                                poppler-utils \
                                ffmpeg mediainfo \
                                imagemagick
                            echo "依赖安装完成。"
                        '''
                    } else {
                        echo "检测到非 Unix 环境，请手动确保安装了 Tesseract 和 Poppler。"
                    }
                }
            }
        }

        stage('Maven Build & Test') {
            steps {
                // 使用 sh 代替 bat (除非你确定是在 Windows 上跑)
                // 注意：这里去掉了 javadoc.skip，如果你想生成文档就不要跳过
                // 保留了 test.failure.ignore 以便即使测试挂了也能看报告
                sh 'mvn clean package -U -Dmaven.test.failure.ignore=true'
            }
        }
    }

    post {
        always {
            echo 'ℹ️ 构建结束，正在收集结果...'
            // 收集 JAR 包
            archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true

            // 【关键修复】修正测试报告的路径，必须包含 /target/
            junit testResults: '**/target/surefire-reports/*.xml', allowEmptyArchive: true

            // 如果你想看 HTML 格式的测试报告，也可以归档 HTML 目录
            // archiveArtifacts artifacts: '**/target/site/', allowEmptyArchive: true
        }
        success {
            echo '✅ 流水线执行成功！(注意检查测试报告中是否有被忽略的错误)'
        }
        failure {
            echo '❌ 流水线执行失败！(可能是编译错误或脚本语法错误)'
        }
    }
}
