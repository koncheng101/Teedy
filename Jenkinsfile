pipeline {
    agent any

    tools {
        jdk 'jdk11' // 对应你配置的JDK别名
        maven 'maven3' // 对应你配置的Maven别名
    }

    stages {
        stage('Checkout') {
            steps {
                checkout scm
                sh 'git status'
            }
        }

        stage('Maven Build') {
            steps {
                // 清理并构建，跳过测试（实验环境常见做法）
                sh 'mvn clean install -DskipTests'
            }
        }

        stage('PMD Code Analysis') {
            steps {
                // 使用Maven PMD插件进行代码检查
                sh 'mvn pmd:pmd'
            }
        }

        stage('Run Tests') {
            steps {
                // 运行单元测试
                sh 'mvn test'
            }
            post {
                always {
                    // 收集测试结果
                    junit '**/target/surefire-reports/*.xml'
                }
            }
        }

        stage('Generate JavaDoc') {
            steps {
                // 生成Java文档
                sh 'mvn javadoc:javadoc'
            }
        }

        stage('Archive Artifacts') {
            steps {
                // 归档构建产物
                archiveArtifacts artifacts: 'docs-web/target/*.war', fingerprint: true
                archiveArtifacts artifacts: 'docs-core/target/*.jar', fingerprint: true
                // 归档报告
                archiveArtifacts artifacts: '**/target/site/javadoc/**', fingerprint: true, allowEmptyArchive: true
                archiveArtifacts artifacts: '**/target/pmd.xml', fingerprint: true, allowEmptyArchive: true
            }
        }
    }

    post {
        success {
            echo '🎉 构建成功！流水线执行完毕。'
        }
        failure {
            echo ' 构建失败！请检查日志。'
        }
    }
}
