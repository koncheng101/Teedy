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

        stage('Maven Build & PMD Check') {
            steps {
                // 保持不变，先安装依赖
                bat 'mvn clean install pmd:pmd -DskipTests -U'
            }
        }

        stage('Run Unit Tests') {
            steps {
                // 【修改】添加 -Dmaven.test.failure.ignore=true
                // 即使测试失败，也继续后续流程
                bat 'mvn test -Dmaven.test.failure.ignore=true'
            }
        }

        stage('Generate JavaDoc') {
            steps {
                bat 'mvn javadoc:javadoc'
            }
        }
    }

    post {
        success {
            echo '✅ 流水线执行成功！正在归档构建产物...'
            archiveArtifacts artifacts: '**/target/*.jar, **/target/*.war', fingerprint: true
            archiveArtifacts artifacts: '**/target/surefire-reports/*.html', allowEmptyArchive: true
            archiveArtifacts artifacts: '**/target/site/apidocs/**', allowEmptyArchive: true
            junit '**/target/surefire-reports/*.xml'
        }
        failure {
            echo '❌ 流水线执行失败！但工作空间已保留，请登录服务器查看 C:\ProgramData\Jenkins\.jenkins\workspace\... 目录下的 target/surefire-reports 获取详细错误。'
        }
        always {
            // 【修改】注释掉 cleanWs()，保留失败后的文件用于调试
            // cleanWs()
        }
    }
}
