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
        stage('Deploy') {
            steps {
                echo 'Deploying....'
            }
        }
	stage('Done') {
            steps {
                echo 'yes its done....'
            }
        }
    }
}
