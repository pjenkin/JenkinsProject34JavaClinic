// 34 - Introduction to the Java web project
// Jenkins pipeline to Checkout and build
pipeline
{
    agent 
    {
        docker 
        {
            image 'maven:3.6-ibmjava-alpine'
            label 'linux'       // we have to run on the node which is the only Jenkins instance with Docker at the mo
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
            steps
            {
                sh 'mvn -e -X clean package'                               // use -e to troubleshoot errors (e.g. .m2 folder)
                junit '**/target/surefire-reports/TEST-*.xml'           // test & present results ready for Jenkins reporting
            }
        }
    }
}