node {
   stage('Build') {
         git 'https://github.com/subaan/shopping-cart.git/'
         sh 'mvn package'
         archive 'target/*.war'
   }

stage("publish to s3") {
    step([
        $class: 'S3BucketPublisher',
        entries: [[
            sourceFile: '/var/lib/jenkins/workspace/shopping-cart/target/shopping-cart.war',
            bucket: 'appranix-demo',
            selectedRegion: 'us-east-1',
            noUploadOnFailure: true,
            managedArtifacts: true,
            flatten: true,
            showDirectlyInBrowser: true,
            keepForever: true,
        ]],
        profileName: 'appranix-demo',
        dontWaitForConcurrentBuildCompletion: false, 
    ])
}

   stage('Deploy to Prana') {
      withCredentials([[$class: 'UsernamePasswordMultiBinding', credentialsId: 'customer-demo',
                            usernameVariable: 'USERNAME', passwordVariable: 'PASSWORD']]) {
                //available as an env variable, but will be masked if you try to print it out any which way
               sh 'prana auth logout'
               sh 'echo successfully logged out'

               sh "prana auth set_token s_BWUxnh_W8qEbdLS7pM"
               sh 'echo successfully logged in'

               sh "prana config set organization=devorg -g"
               sh 'echo organization is set as devorg'

               sh "prana config set assembly=demo-shopping-cart -g"
               sh 'echo assembly is set as demo-shopping-cart'

               sh "prana design load design.yml"
               sh 'echo design is uploaded'

               sh "prana design variable update -a demo-shopping-cart --platform=tomcat appVersion=${env.BUILD_ID}"

               sh "prana design commit init-commit"
               sh 'echo new design in committed with message init-commit'

               sh "prana transition pull -e prod"
               sh 'echo design pull to prod appspace'

	       sh 'sleep 20'

               sh "prana transition commit init-commit -e prod"

               sh "prana transition deployment create -e prod"
               sh 'echo deployement is started'
      }
   }
}
