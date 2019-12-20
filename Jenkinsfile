// 34 - Introduction to the Java web project
// Jenkins pipeline to Checkout and build
pipeline
{
    agent {label 'linux'}
    {
        docker 
        {
            // image 'maven:3.6-ibmjava-alpine' // REM out this as Maven docker image may not have scp (needed for deployment)
            label 'linux'       // we have to run on the node which is the only Jenkins instance with Docker at the mo
            args '-v $HOME/.m2:/var/maven/.m2:z -e MAVEN_CONFIG=/var/maven/.m2 -e MAVEN_OPTS="-Duser.home=/var/maven"'
            // Try https://stackoverflow.com/a/55415674 to not run as root but still avoid "Could not create local repository at /.m2/repository" - this worked!
        }
    }
    stages
    {
        stage('Checkout')   // checkout from the Github repo
        {
            steps
            {
                git 'https://github.com/spring-projects/spring-petclinic'  // NB no sh needed    
            }
        }
        stage('Build')
        {
            agent {label 'linux'}   // a different agent specified for build - maven docker image is good for building (just not for deploying i.e. no scp)
            {
                docker 
                {
                    image 'maven:3.6-ibmjava-alpine'
                    label 'linux'       // we have to run on the node which is the only Jenkins instance with Docker at the mo
                    args '-v $HOME/.m2:/var/maven/.m2:z -e MAVEN_CONFIG=/var/maven/.m2 -e MAVEN_OPTS="-Duser.home=/var/maven"'
                    // Try https://stackoverflow.com/a/55415674 to not run as root but still avoid "Could not create local repository at /.m2/repository" - this worked!
                }
            }

            steps
            {
                sh 'mvn -e -X clean package'                               // use -e to troubleshoot errors (e.g. .m2 folder)
                junit '**/target/surefire-reports/TEST-*.xml'           // test & present results ready for Jenkins reporting
                archiveArtifacts artifacts: 'target/*.jar', fingerprint: true       // 35 archive & fingerprinting 
            }
        }
        stage('Deploy')         // 36 Deploying our Java web project - with human input
        {
            steps
            {
                input 'Do you approve the deployment?'
                // echo 'Deploying ...'
                sh 'scp target/*.jar jenkins@192.168.50.10:/opt/pet/'
                //35 Copy project artifact from Jenkins machine to target machine (i) 6:00
                sh "ssh jenkins@192.168.50.10 'nohup java -jar /opt/pet/spring-petclinic-2.2.0.jar &'"
                //35 Start the application (from artifact) on the target machine (ii) 
                // version number in real pipeline would need to be from environment variable not hard coded
                // NB if the target machine does not have scp installed, an error will ensue at (i)
            }
        }
    }
}