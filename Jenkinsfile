pipeline {
    agent any

    stages {
        stage('Build') {
            steps {
                echo "Branch Name: ${env.BRANCH_NAME}"
                echo 'Building..'
            }
        }
        stage('Test') {
            steps {
                echo 'Testing..'
            }
        }
        stage('lest') {
            steps {
                echo 'lesting..'
            }
        }
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
      
    }
}
