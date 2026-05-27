pipeline {
    agent any
    
    tools {
        jdk 'jdk21'   // 对应你 Jenkins 中配置的 JDK 21 别名
        maven 'maven3' // 对应你 Jenkins 中配置的 Maven 别名
    }

    stages {
        stage('Checkout SCM') {
            steps {
                checkout scm
            }
        }

        stage('Maven Build & PMD Check') {
            steps {
                // 使用 bat 适配 Windows，加上 -U 强制更新依赖
                bat 'mvn clean compile pmd:pmd -U'
            }
        }

        stage('Run Unit Tests') {
            steps {
                // 使用 bat 适配 Windows，加上 -U 强制更新依赖
                bat 'mvn test -U'
            }
        }

        stage('Generate JavaDoc & Package') {
            steps {
                // 使用 bat 适配 Windows，加上 -U 强制更新依赖
                bat 'mvn javadoc:javadoc package -DskipTests -U'
            }
        }
    }

    post {
        success {
            echo '✅ 流水线执行成功！正在归档构建产物...'
            // 归档 jar/war 包 (Windows下兼容写法)
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
            // 无论成功或失败，最后都清理工作空间
            cleanWs()
        }
    }
}
