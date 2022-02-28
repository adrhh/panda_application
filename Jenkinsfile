pipeline
{
    agent
    {   
        label 'TestSlaveNodeLabel'
	}

    tools 
    {
        // Install the Maven version configured as "M3" and add it to the path.
        maven "auto_maven"
    }
    environment 
    {
        // IMAGE = readMavenPom().getArtifactId()
        // VERSION = readMavenPom().getVersion()
        IMAGE  = sh script: 'mvn help:evaluate -Dexpression=project.artifactId -q -DforceStdout', returnStdout: true
        VERSION = sh script: 'mvn help:evaluate -Dexpression=project.version -q -DforceStdout', returnStdout: true
        APPNAME = "pandaapp"
    }

    stages 
    {
        stage('Clear')
        {
            steps 
            {
                // Clear previous instances of app built
                sh "docker rm -f ${APPNAME} || true"
            }
        }
        // stage('Clone')
        // {
        //     steps
        //     {
        //          // Get some code from a GitHub repository
        //         git branch: 'final_test_fix', url: 'https://github.com/adrhh/panda_application'
        //     }
        // }
        stage('Build') 
        {
            steps 
            {
                // Run Maven on a Unix agent.
                sh "mvn -Dmaven.test.failure.ignore=true clean install"
                // To run Maven on a Windows agent, use
                // bat "mvn -Dmaven.test.failure.ignore=true clean package"
            }
        }
        stage('Docker image')
        {
            steps
            {
                sh "mvn package -Pdocker"
            }
        }
        stage('Run Docker app') 
        {
            steps 
            {
                sh "docker run -d -p 0.0.0.0:8080:8080 --name ${APPNAME} ${IMAGE}:${VERSION}"
            }
        }
        stage('Test')
        {
            steps 
            {
                sh "mvn test -Pselenium"
                sh "mvn --version"
            }
        }
        stage('Deploy') 
        {
            steps 
            {
                withMaven(globalMavenSettingsConfig: 'null', jdk: 'null', maven: 'auto_maven', mavenSettingsConfig: 'MyMaven') 
                {
                    sh "mvn deploy"
                }
            }
        }
        post 
        {
            success
            { 
                deleteDir()
            }
            failure
            {
                sh "echo post failure"
            }
        }
        // post 
        // {
        //     // If Maven was able to run the tests, even if some of the test
        //     // failed, record the test results and archive the jar file.
        //     success 
        //     {
        //         junit '**/target/surefire-reports/TEST-*.xml'
        //         archiveArtifacts 'target/*.jar'
        //     }
        // }
    }
}
