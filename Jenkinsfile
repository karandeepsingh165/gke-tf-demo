pipeline {
    agent any
    tools {
        "org.jenkinsci.plugins.terraform.TerraformInstallation" "terraform"
    }

    environment {
        TF_HOME = tool('terraform')
        TF_LOG = "WARN"
        PATH = "$TF_HOME:$PATH"
        environments = "${environments}"
        directory = "${directory}"
        region = "${region}"
    }


    stages {
        stage('Terraform-Init') {
            steps {
                dir("${region}/${environments}/${directory}") {
                    sh "terraform init -input=false"
                    sh "echo \$PWD"
                    sh "whoami"
                }
            }
        }


        stage('Terraform-Format') {
            steps {
                dir("${region}/${environments}/${directory}") {
                    sh "terraform fmt -list=true -write=false -diff=true -check=true"
                }
            }
        }


        stage('Terraform-Validate') {
            steps {
                dir("${region}/${environments}/${directory}") {
                    sh "terraform validate"
                }
            }
        }


        stage('Terraform-Plan') {
            steps {
                dir("${region}/${environments}/${directory}") {
                    sh 'terraform plan -out tfplan'
                }
            }
        }
        
       
     stage('Terraform-Approval') {
      steps {
        script {
          def userInput = input(id: 'Confirm', message: 'Do You Want To Apply The Terraform Changes?', parameters: [ [$class: 'BooleanParameterDefinition', defaultValue: false, description: 'Apply Terraform Changes', name: 'confirm'] ])
        }
      }
    }


    stage('Terraform-Apply') {
      steps {
          dir("${region}/${environments}/${directory}") {
          sh 'terraform apply -input=false tfplan' 
       }
     }
   }
 }
}
