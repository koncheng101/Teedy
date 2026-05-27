pipeline {
    agent any
    
    tools {
        jdk 'jdk21'   // 确保这是你Jenkins里配置的JDK名称
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
                // 修改点1: 使用 bat 代替 sh
                bat 'mvn clean compile pmd:pmd'
            }
        }

        stage('Run Unit Tests') {
            steps {
                // 修改点2: 使用 bat 代替 sh
                bat 'mvn test'
            }
        }

        stage('Generate JavaDoc & Package') {
            steps {
                // 修改点3: 使用 bat 代替 sh
                bat 'mvn javadoc:javadoc package -DskipTests'
            }
        }
    }

    post {
        success {
            echo '✅ 流水线执行成功！'
            // 注意：Windows下路径分隔符是反斜杠，但Jenkins归档通常用正斜杠或通配符也能识别
            archiveArtifacts artifacts: '**\\target\\*.jar', allowEmptyArchive: true
            junit '**\\target\\surefire-reports\\*.xml'
        }
        failure {
            echo '❌ 流水线执行失败！'
        }
        always {
            cleanWs()
        }
    }
}
