node {
  checkout scm
  env.PATH = "${tool 'Maven3'}/bin:${env.PATH}"
  stage('Package') {
    dir('webapp') {
      sh 'mvn clean package -DskipTests'
    }
  }

  stage('Create Docker Image') {
    dir('webapp') {
      docker.build("asvinwin123/docker-jenkins-pipeline:${env.BUILD_NUMBER}")
    }
  }

  stage ('Run Application') {
    try {
      // Start database container here
      // sh 'docker run -d --name db -p 8091-8093:8091-8093 -p 11210:11210 arungupta/oreilly-couchbase:latest'

      // Run application using Docker image
      sh "DB=`docker inspect --format='{{range .NetworkSettings.Networks}}{{.IPAddress}}{{end}}' db`"
      sh "docker run -e DB_URI=$DB asvinwin123/docker-jenkins-pipeline:${env.BUILD_NUMBER}"

      // Run tests using Maven
      //dir ('webapp') {
      //  sh 'mvn exec:java -DskipTests'
      //}
    } catch (error) {
    } finally {
      // Stop and remove database container here
      //sh 'docker-compose stop db'
      //sh 'docker-compose rm db'
    }
  }

  stage('Run Tests') {
    try {
      dir('webapp') {
        sh "mvn test"
        //docker.build("asvinwin123/docker-jenkins-pipeline:${env.BUILD_NUMBER}").push()
        script {
          def appimage = docker.build registry + ":$BUILD_NUMBER"
          docker.withRegistry( '', 'b00de5ae-26b1-4813-8145-0118a47e7e78' ) {
              appimage.push()
              appimage.push('latest')
          } 
        }
      }
    } catch (error) {
      
    } finally {
      //junit '**/target/surefire-reports/*.xml'
    }
  }
}
