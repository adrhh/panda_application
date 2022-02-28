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

    stages 
    {
        stage('Clear running apps')
        {
            steps 
            {
                // Clear previous instances of app built
                sh "docker rm -f pandaapp || true"
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
        stage('Build Docker image')
        {
            steps
            {
                sh "mvn package -Pdocker"
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
        post 
        {
            // If Maven was able to run the tests, even if some of the test
            // failed, record the test results and archive the jar file.
            success 
            {
                junit '**/target/surefire-reports/TEST-*.xml'
                archiveArtifacts 'target/*.jar'
            }
        }
    }
}
