pipeline {
  agent {
    node {
      label 'maven'
    }
  }
  
  stages {    
    
    stage('Checkout') {
      steps {
        git url: "${SOURCE_REPO}", branch: "${SOURCE_REF}", credentialsId: "build-github"
      }
    } 
    
    stage('Build') {
      steps {
        sh "mvn -B -DskipTests package"
      }
    }
    
    stage('Test') {
      parallel {
        stage('Unit Test') {
          steps {
            sh "mvn -B test"   
          }
        }
        stage('SonarQube') {
          steps {
            sh "mvn org.sonarsource.scanner.maven:sonar-maven-plugin:sonar -Dsonar.host.url=http://sonarqube:9000"
          }
        }
      }
    }
    
    stage('Build Image') {
      steps {
        script {
          openshift.withCluster() {
            openshift.selector("bc", "${NAME}").startBuild("--from-dir=target/ --build-loglevel=5").logs("-f")
            
            // Extract abbreviated git commit id
            def props = readProperties file: "target/classes/git.properties"
            def abbrev = props['git.commit.id.abbrev']
            def src_tag = """${NAME}:latest"""
            def dst_tag = """${NAME}:$abbrev"""
            
            echo """Tagging latest build git commit id: $dst_tag"""
            openshift.tag(src_tag, dst_tag)            
            
          }
        }
      }
      
    }
  }
}