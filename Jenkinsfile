pipeline {

agent any

options {

    skipDefaultCheckout()
    disableConcurrentBuilds()
    disableResume()
    }
    
 environment {
    imagename = "chetancc023/magento"
    dockerImage = ''
    
  }   
parameters {

    string(name: 'imagename', defaultValue: '1.0', description: 'tagname')
    string(name: 'version', defaultValue: '1.0', description: 'tagname')
}



stages{

    // checkout stage //
    stage('GIT Pull'){
        steps{
            checkout scm
        }
    }
    
    stage('Enabling maintainence mode'){
        steps{
            sh 'cd /var/www/html/magento'
            sh 'php ./bin/magento maintenance:enable || true'
        }
    }

    stage('Checkout Source Code from Master Branch'){
        steps{
            checkout scm
        }
    }

    stage("composer install"){

        steps{
           // sh 'apt get update'
            sh 'cd /var/www/html/magento'
            sh 'composer install'
        }
    } 

    stage(' Setup Upgrade'){

        steps{
            sh 'cd /var/www/html/magento'
            sh 'composer update'
            sh 'php /var/www/html/magento setup:upgrade'
        }
    }

    stage(' Di Compile'){

        steps{
           
            sh 'php /var/www/html/magento setup:di:compile'
            
        }
    }

    stage(' Static Content Deploy'){

        steps{
            
            sh 'php /var/www/html/magento setup:static-content:deploy'
            
        }
    }

    stage(' Cache Flush'){

        steps{
            
            sh 'php /var/www/html/magento clean:flush'
            
        }
    }

    stage(' Disabiling maintenance mode'){

        steps{
            
            sh 'php /var/www/html/magento maintenance:disable'
            
        }
    }
    


    // Docker build and push to docker-hub //

    stage(' Docker Build and Push To Docker-Hub '){
        steps{
            echo 'Building Started'
            script{
                dockerImage = docker.build imagename
                docker.withRegistry('', 'docker-creds') {
                dockerImage.push("latest")
                dockerImage.push('latest')    

                }


            }


        }
    }
    // Pulling docker images from hub and deploying using docker-compose method//
   stage('Deploying Image to Container'){
       
       steps{
                    sh """
                        cd /home/ubuntu
                        docker-compose -f docker-compose.yml pull
                        docker-compose -f docker-compose.yml up -d
                    """
             
       }
   }
    
    
}
}
