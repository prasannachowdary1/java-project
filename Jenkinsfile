pipeline{
  agent none
  options{
    buildDiscarder(logRotator(numToKeepStr: '2' , artifactNumToKeepStr: '1'))
  }
   stages{
      stage('Unit Tests') {
      agent{

        label 'apache'
      }
      steps{
         sh 'ant -f test.xml -v'
         junit 'reports/result.xml'
      }
    }
    stage('build'){
      agent{

        label 'apache'
      }
    steps {

    sh 'ant -f build.xml -v'
    }
    post{
      success{
        archiveArtifacts artifacts: 'dist/*.jar' , fingerprint:true
      }
    }
    }
    stage('deploy'){
      agent{

        label 'apache'
      }
    steps {
        sh "mkdir /var/www/html/rectangle/all/${env.BRANCH_NAME}"
        sh "cp dist/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangle/all/${env.BRANCH_NAME}"
    }
    }
    stage('Running on CentOs'){
      agent{
         label 'apache'
      }

      when{
        branch 'master'
      }
      steps{
       sh "wget http://192.168.33.88/rectangle/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
        sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 2 3"
      }
    }
    stage('testing on Debian'){
       agent{

            docker 'openjdk:8u121-jre'
       }
       steps{
         sh "wget http://192.168.33.88/rectangle/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar"
         sh "java -jar rectangle_${env.BUILD_NUMBER}.jar 4 5"       }

    }

    stage('Promoting to Green after successful functional Testing'){
      agent{
         label 'apache'
      }
        steps{

            sh "cp /var/www/html/rectangle/all/${env.BRANCH_NAME}/rectangle_${env.BUILD_NUMBER}.jar /var/www/html/rectangle/green/rectangle_${env.BUILD_NUMBER}.jar"

        }
     }

    stage('Promote Development branch to Master'){
      agent{
        label 'apache'
      }
      when {
        branch 'development'
      }
      steps{
        echo "Stashing Any local changes"
        sh 'git stash'
        echo "Checkout into Dev Branch"
        sh 'git checkout development'
        echo "Checkout into master"
        sh 'git checkout master'
        echo 'Merging Development into Master'
        sh 'git merge development'
        echo 'Pushing into origin master'
        sh 'git pusg origin master'
      }
    }

}

}
