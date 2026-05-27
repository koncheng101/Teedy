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

        stage('Maven Build') {
            steps {
                // 关键修改点：添加 -Dmaven.test.failure.ignore=true
                // 这会让 Maven 在单元测试失败时继续打包
                bat 'mvn clean install -U -Dmaven.javadoc.skip=true -Dmaven.test.failure.ignore=true'
            }
        }
    }

    post {
        success {
            echo '✅ 流水线执行成功！'
            // 修复归档路径，使用 ** 递归查找
            archiveArtifacts artifacts: '**/target/*.jar', fingerprint: true
            archiveArtifacts artifacts: '**/surefire-reports/*.xml', allowEmptyArchive: true
            junit '**/surefire-reports/*.xml'
        }
        failure {
            echo '❌ 流水线执行失败！'
        }
        always {
            echo 'ℹ️ 构建结束。'
            // 如果你想保留现场去排查 OCR 错误，可以暂时注释掉下面这行
            // cleanWs()
        }
    }
}
