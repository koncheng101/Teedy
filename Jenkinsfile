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
                // 【关键修改】将 compile 改为 install，确保多模块的 test-jar 被正确安装到本地仓库
                // -DskipTests 跳过测试执行以加快首次打包速度，-U 保持强制更新依赖
                bat 'mvn clean install pmd:pmd -DskipTests -U'
            }
        }

        stage('Run Unit Tests') {
            steps {
                // 代码已在上一阶段编译打包完成，这里只需专注运行单元测试即可
                bat 'mvn test'
            }
        }

        stage('Generate JavaDoc') {
            steps {
                // 移除 package (已在第一阶段完成) 和 -DskipTests (test阶段已跑过)，只保留生成文档
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
            // 无论成功或失败，最后都清理工作空间
            cleanWs()
        }
    }
}
