pipeline {
  agent {
    docker { image 'liquibase/liquibase:4.17' }
  }
  environment {
    TEST_URL='jdbc:postgresql://database-1.cpuc6bgspxr2.eu-central-1.rds.amazonaws.com:5432/postgres'
    QA_URL='jdbc:postgresql://prod.cpuc6bgspxr2.eu-central-1.rds.amazonaws.com:5432/postgres'
    changeLogFile='changelog_version.xml'
    driver='org.postgresql.Driver'
  }
  stages {
    stage('Status') {
      steps {
        sh 'liquibase status --url="$env:TEST_URL" --changeLogFile=$env:changeLogFile. --driver='org.postgresql.Driver' --username=postgres --password=fellaini'
        sh 'liquibase status --url="$env:QA_URL" --changeLogFile=$env:changeLogFile --driver='org.postgresql.Driver' --username=postgres --password=fellaini'
      }
    }
    stage('test') {
       steps {
        sh 'mvn clean package -Dmaven.test.skip=true'
        sh 'mvn liquibase:update --url=$env:TEST_URL --changeLogFile=$env:changeLogFile --username=postgres --password=fellaini'
        sh 'mvn liquibase:status -PTEST'
        sh 'mvn spring-boot:run'
      }
    }
    stage('QA') {
      steps {
        sh 'mvn clean package'
        sh 'mvn liquibase:update --url=$env:QA_URL --changeLogFile=$env:changeLogFile --username=postgres --password=fellaini'
        sh 'mvn liquibase:status -PQA'
        sh 'mvn Spring-boot:run'
       }
      }
    stage('Update') {
      steps {
        sh 'liquibase update --url="$QA_URL" --changeLogFile=$changeLogFile --username=postgres --password=fellaini'
      }
    }
  }
  post {
    always {
      cleanWs()
    }
  }
}