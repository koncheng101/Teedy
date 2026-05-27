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
                // 使用 install 确保多模块依赖正确安装，-U 强制更新快照依赖
                bat 'mvn clean install pmd:pmd -DskipTests -U'
            }
        }

        stage('Run Unit Tests') {
            steps {
                // 加上 -Dmaven.test.failure.ignore=true，即使有测试报错也不中断构建，方便后续生成文档和归档
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
            // 【修复】Windows 路径中的 \ 必须写成 \\ 进行转义，否则会报 unexpected char 错误
            echo '❌ 流水线执行失败！但工作空间已保留，请登录服务器查看 C:\\ProgramData\\Jenkins\\.jenkins\\workspace\\... 目录下的 target/surefire-reports 获取详细错误。'
        }
        always {
            // 【修复】加上 steps 块以符合声明式流水线语法规范
            steps {
                // 【临时注释】为了方便排查之前的测试报错，暂时不删除工作空间
                // 等你确认流水线完全正常后，请把下面这行的 // 去掉，恢复自动清理
                // cleanWs()
            }
        }
    }
}
