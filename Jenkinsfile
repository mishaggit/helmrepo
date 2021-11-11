pipeline {
    agent {
        label 'slave'
    }
    options {ansiColor('xterm') }
    parameters {
        choice(name: 'CHOICES', choices: ['terraform plan', 'terraform apply'], description: 'Choose terraform command')
    }
    environment {
      OWNER_NAME   = "Misha"
    }
    stages {
        stage('1-Test') {
            steps {
                echo "Testing..................................."
                //echo "Owner is ${OWNER_NAME}"
                sh "echo $PATH"
                echo "End of Stage Build........................"
            }
        }
        stage('2- Find all fodlers from given folder') {
            steps {
                script {
                    folders = sh(returnStdout: true, script: "find -path './[^.]*' -prune -type d").trim()
                    echo "$folders"
                    folderstf = "$folders".split()
                    echo "=================================list"
                    echo "$folderstf"
                    echo "=================================list"
                    folderstf.each {
                        //val -> println "$val"
                        val -> dir("$val") {script {sh "terraform init"}}
                    }
                }
            }
        }
        stage ("Terraform Command") {
            steps {
                echo "Choice is ${params.CHOICES}"
                script {
                    for (value in folderstf) {
                        dir("$value"){
                            sh "echo $PATH"
                            sh "echo test"
                            sh "terraform init"
                            sh "terraform validate"
                            sh "terraform plan"
                        }
                    }
                } 
            }
        }
        stage('TF Apply') {
            when { anyOf {branch "main";branch "master" } }
            steps {
                ansiColor('xterm') {
                    script {
                        for (value in folderstf){
                            dir("$value") {
                                sh "echo test apply update"
                                input(message: 'Do you want TF Apply', ok: 'Proceed')
                                sh "terraform apply -input=false -auto-approve"
                                //sh "${params.CHOICES} -input=false -auto-approve"
                            }
                        }
                    }
                }
            }
        }
    }
    post {
        always {
            cleanWs()
        }
    }
}