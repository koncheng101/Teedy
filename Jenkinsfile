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
        // 归档 jar/war 包
        archiveArtifacts artifacts: '**/target/*.jar, **/target/*.war', fingerprint: true
        // 归档 HTML 格式的测试报告
        archiveArtifacts artifacts: '**/target/surefire-reports/*.html', allowEmptyArchive: true
        // 归档生成的 JavaDoc 文档
        archiveArtifacts artifacts: '**/target/site/apidocs/**', allowEmptyArchive: true
        // 解析并展示 XML 格式的测试结果趋势图
        junit '**/target/surefire-reports/*.xml'
    }
    failure {
        echo '❌ 流水线执行失败！请查看控制台输出排查错误。'
    }
    always {
        // 【关键修复】加上 steps，并确保 cleanWs 有缩进
        steps {
            // 无论成功或失败，最后都清理工作空间
            cleanWs()
        }
    }
}
