 pipeline {
    agent {
        docker {
            image 'digitalonus/terraform_hub:0.11.10'
        }
    }
    environment {
        DIGITALOCEAN_TOKEN= credentials('DO_TOKEN')
    }
    triggers {
         pollSCM('H/5 * * * *')
    }
    stages {
        stage('init'){
          when { expression { env.BRANCH_NAME ==~ /dev.*/ || env.BRANCH_NAME ==~ /PR.*/ || env.BRANCH_NAME ==~ /feat.*/ } }
          steps{
            sh 'cd terraform && terraform init -input=false'    
          }
        }
        stage('validate'){
          when { expression { env.BRANCH_NAME ==~ /dev.*/ || env.BRANCH_NAME ==~ /PR.*/ || env.BRANCH_NAME ==~ /feat.*/ } }
          steps{
            sh 'cd terraform && terraform validate'    
          }
        }

        stage('Generate PR'){
            when { expression{ env.BRANCH_NAME ==~ /feat.*/ } }
            steps{
                echo 'create PR'
            }
        }

        stage('plan'){
            when { expression{ env.BRANCH_NAME ==~ /dev.*/ || env.BRANCH_NAME ==~ /PR.*/ || env.BRANCH_NAME ==~ /feat.*/ } }
            steps {
                sh 'cd terraform && terraform plan -out=plan -input=false'
                input(message: "Do you want to apply this plan?", ok: "yes")
            }
        }
        stage('apply') {
            when { expression{ env.BRANCH_NAME ==~ /dev.*/ || env.BRANCH_NAME ==~ /PR.*/ || env.BRANCH_NAME ==~ /feat.*/ } }
            steps {
                sh 'cd terraform && terraform apply -input=false plan'
            }
        }
        stage('destroy') {
            when { expression{ env.BRANCH_NAME ==~ /dev.*/ || env.BRANCH_NAME ==~ /PR.*/ || env.BRANCH_NAME ==~ /feat.*/ } }
            steps {
                sh 'cd terraform && terraform destroy -force -input=false'
            }
        }
    }
    post {
      success {
          echo 'success'
        //slackSend baseUrl: readProperties.slack, channel: '#devops_training_nov', color: '#00FF00', message: "SUCCESSFUL: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})"
      }
      failure {
           echo 'FAILED'
        // script{
        //   //def commiter_user = sh "git log -1 --format='%ae'"
        //   //slackSend baseUrl: readProperties.slack, channel: '#devops_training_nov', color: '#FF0000', message: "FAILED: Job '${env.JOB_NAME} [${env.BUILD_NUMBER}]' (${env.BUILD_URL})"
        // }
      }

    }
}

