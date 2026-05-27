pipeline {
    agent any
    
    // 指定构建所需的工具（别名必须与 Jenkins 全局工具配置中的名称完全一致）
    tools {
        jdk 'jdk21'   // 对应你的 JDK 21 环境
        maven 'maven3' // 确保你在 Jenkins 里也配置了名为 maven3 的 Maven
    }

    stages {
        stage('Checkout SCM') {
            steps {
                // 拉取 GitHub 仓库的代码
                checkout scm
            }
        }

        stage('Maven Build & PMD Check') {
            steps {
                // 执行清理、编译，并运行 PMD 静态代码检查
                sh 'mvn clean compile pmd:pmd'
            }
        }

        stage('Run Unit Tests') {
            steps {
                // 执行单元测试
                sh 'mvn test'
            }
        }

        stage('Generate JavaDoc & Package') {
            steps {
                // 生成 JavaDoc 文档，并进行打包 (生成 jar/war)
                sh 'mvn javadoc:javadoc package -DskipTests'
            }
        }
    }

    // 构建后的处理动作
    post {
        success {
            echo '✅ 流水线执行成功！正在归档构建产物...'
            // 归档可执行的 jar/war 包
            archiveArtifacts artifacts: '**/target/*.jar, **/target/*.war', fingerprint: true
            
            // 归档 HTML 格式的测试报告
            archiveArtifacts artifacts: '**/target/surefire-reports/*.html', allowEmptyArchive: true
            
            // 归档生成的 JavaDoc 文档
            archiveArtifacts artifacts: '**/target/site/apidocs/**', allowEmptyArchive: true
            
            // 让 Jenkins 原生解析并展示 XML 格式的测试结果趋势图
            junit '**/target/surefire-reports/*.xml'
        }
        failure {
            echo '❌ 流水线执行失败！请查看控制台输出排查错误。'
        }
        always {
            // 无论成功或失败，最后都清理工作空间，释放磁盘空间
            cleanWs()
        }
    }
}
