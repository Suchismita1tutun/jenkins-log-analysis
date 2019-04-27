pipeline{
    agent{
        label "IndiaLinuxJenkinsVM"
    }

    environment {
    MattermostEndpoint = credentials("MATTERMOST_URL_EPI")
    MattermostChannel = "epi"
  }
  
    stages{
        
        stage ('Get Usernames of Committers') {
        steps {
            script {
                def authors = currentBuild.changeSets.collect({ it.items.collect { it.author } })[0]
                echo "authors ${authors}"
                if (authors != null) {
                    def usernames = ""
                    def uniqueAuthors = authors.unique()
                    echo "uniqueAuthors ${uniqueAuthors}"
                    for (int i = 0; i < uniqueAuthors.size(); i++) {
                    def id = uniqueAuthors[i].getId()
                    usernames += "@" + id + " "
                    }
                    env.Authors = usernames
                } else {
                    env.Authors = "" 
                }
            }
          }
        }
        
        stage("Delete workspace"){
              sh ' rm -rf jiraconnector '
        }
        stage("Run build"){
           sh ' mvn clean install -P jenkinsEnv '
        }
    }
    post{
        always{
            echo "========always========"
        }
        success{
           sh 'POM_VERSION=`echo "$(cat pom.xml | grep "<version>" | awk -F"[><]" "{print $3}" | head -n 1)"`'
           sh 'echo $POM_VERSION '  
        }
        failure{
            echo "========pipeline execution failed========"
        }
    }
}