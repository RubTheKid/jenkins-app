pipeline {
    agent any

    stages {
        stage('withoutDocker') {
            // steps {
            //     sh '''
            //      echo "Without Docker"
            //      ls -la 
            //      touch container-no.txt
            //     '''
            // }
            steps {
                sh '''
                echo "testing jenkins with github"
                ls -la
                touch github-test.txt
                '''
            }
        }
        // stage('withDocker') {
        //     agent {
        //         docker {
        //             image 'node:18-alpine'
        //             reuseNode true
        //         }
        //     }
        //     steps {
        //         sh '''
        //         echo "With docker"
        //         ls -la 
        //         touch container-yes.txt
        //         '''
        //     }
        // }
    }
}
