//START-OF-SCRIPT
node {
    def SONARQUBE_HOSTNAME = 'sonarqube'
    
    // Nhớ cài gradle-4.10.2 trong Manage Jenkins --> Global Tool Configuration --> Gradle --> Add Gradle: name: gradle-4.10.2, Version: Gradle 4.10.2
    // Xem version của gradle: docker exec jenkins /var/jenkins_home/tools/hudson.plugins.gradle.GradleInstallation/gradle-4.10.2/bin/gradle --version
    def GRADLE_HOME = tool name: 'gradle-4.10.2', type: 'hudson.plugins.gradle.GradleInstallation'
    sh "${GRADLE_HOME}/bin/gradle tasks"

    // Lấy từ repo tui fork của người ta :D
    stage('prep') {
        git url: 'https://github.com/thaygiaoth/devops-webapp.git'                
    }

    stage('build') {
        sh "${GRADLE_HOME}/bin/gradle build"
    }

    stage('sonar-scanner') {
      
      def sonarqubeScannerHome = tool name: 'sonar', type: 'hudson.plugins.sonar.SonarRunnerInstallation'
      // Manage Jenkins --> Credentials --> global --> Add credentials --> Kind 
      // tạo 1 cái Secret text: ID tên sonar, Secret là token sinh ra từ Sonarqube
      // Sonarqube http://ip:9000 admin/admin --> chữ A bên phải trên cùng --> My Account --> Security --> Generate Tokens đặt tên tùy ý thí chủ
      withCredentials([string(credentialsId: 'sonar', variable: 'sonarLogin')]) {
        // Manage Jenkins --> Global Tool Configuration --> SonarQube Scanner --> Add SonarQube Scanner --> name: sonar, Version: mới nhất
        sh "${sonarqubeScannerHome}/bin/sonar-scanner -e -Dsonar.host.url=http://${SONARQUBE_HOSTNAME}:9000 -Dsonar.login=${sonarLogin} -Dsonar.projectName=WebApp -Dsonar.projectVersion=${env.BUILD_NUMBER} -Dsonar.projectKey=GS -Dsonar.sources=src/main/ -Dsonar.tests=src/test/ -Dsonar.java.binaries=build/**/* -Dsonar.language=java"
      }
    }

}
//END-OF-SCRIPT
