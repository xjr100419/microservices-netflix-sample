node {
    stage('Checkout') {
        git url: 'https://github.com/xjr100419/microservices-netflix-sample.git', credentialsId: 'github-piomin', branch: 'master'

        sh DEMO_PARMS=`echo $DEMO_PARMS | sed -r 's/"//g'`

        sh 'echo $DEMO_PARMS '

         sh 'echo  ${DEMO_PARMS} '

    }
    withMaven(maven:'M3') {

            stage('Build') {
                    sh ' cd  $DEMO_PARMS '
                    sh 'mvn clean install'

                    def pom = readMavenPom file:'pom.xml'
                    print pom.version
                    env.version = pom.version


                    tag = pom.version
                        appName = "${DEMO_PARMS}"
                        registryHost = "192.168.80.180:5000/demo/"
                        imageName = "${registryHost}${appName}:${tag}"
                        env.BUILDIMG=imageName
            }

            stage('Image') {
                    sh 'cd  ${DEMO_PARMS}'
                    sh "docker login 192.168.80.180:5000 -u admin -p 123456"
                    def app = docker.build "${imageName}"
                    app.push()
                }



            stage ('Deploy') {
                    sh 'cd  ${DEMO_PARMS}'
                    sh "sed 's#192.168.80.180:5000/demo/${DEMO_PARMS}:latest#'$BUILDIMG'#' kubernetes/deployment.yml | kubectl apply -f -"

                    sh "kubectl apply -f kubernetes/svc.yml"
            }


        }
}