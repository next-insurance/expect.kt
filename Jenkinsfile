@Library('jenkins.pipeline') _

env.LOG_LEVEL = "INFO"

def final DEVOPS_BRANCH = "master"

def yamlContent

node("master") {
    github.getFile("devops", DEVOPS_BRANCH, "jenkins/jenkins_files/jvm_pipelines/slaves-versions/${constants.DEFAULT_JNLP_YAML}", "slave.yaml")
    yamlContent = readFile(file: "slave.yaml")
            .replaceAll("#MEM#", "8Gi")
            .replaceAll("#CPU#", "4")
            .replaceAll('#ECR#', constants.OPS_SHARED_SERVICES_ACCOUNT)
}

pipeline {

    agent {
        kubernetes {
            inheritFrom "expect-kt-agent"
            yaml yamlContent
        }
    }

    options {
        timeout(time: 45, unit: 'MINUTES')
        timestamps()
        skipDefaultCheckout(true)
        buildDiscarder(logRotator(numToKeepStr: '30', artifactNumToKeepStr: '30'))
    }

    parameters {
        string(defaultValue: 'master', description: '', name: 'branch', trim: false)
    }

    stages {
        stage('Init') {
            steps {
                script {
                    github.repositoryCheckout(params.branch, constants.GIT_CREDENTIALS_ID, 'expect.kt')
                    env.PATH = "/usr/lib/jvm/default-jvm/bin:${env.PATH}"
                    shell.withoutOutput("echo '\norg.gradle.java.home=/usr/lib/jvm/default-jvm' >> gradle.properties")
                }
            }
        }
    }
}
