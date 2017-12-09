node {
   stage('Build') {
         git 'https://github.com/subaan/shopping-cart.git/'
         sh 'mvn package'
         archive 'target/*.war'
         sh 'echo Build id: ${env.BUILD_ID}'
   }

stage("publish to s3") {
    step([
        $class: 'S3BucketPublisher',
        entries: [[
            sourceFile: '**/*.war',
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
        //available as an env variable, but will be masked if you try to print it out any which way
       sh 'prana auth logout'
       sh 'echo successfully logged out'

       sh "prana auth set_token s_BWUxnh_W8qEbdLS7pM"
       sh 'echo successfully logged in'

       sh "prana config set organization=devorg-veeresh -g"
       sh 'echo organization is set as devorg'

       sh "prana config set assembly=demo-shopping-cart -g"
       sh 'echo assembly is set as demo-shopping-cart'

       sh "prana configure variable update -a demo-shopping-cart -e prod --platform=tomcat appVersion=${env.BUILD_ID}"
       sh 'echo Transition variable update build ver'

       sh "prana configure variable update -a demo-shopping-cart -e prod --platform=tomcat jenkinsBuildUrl=http://localhost:8080/job/shopping-cart/${env.BUILD_ID}"
       sh 'echo Transition variable update build ver'

       sh "prana transition commit init-commit -e prod"

       sh "prana transition deployment create -e prod"
       sh 'echo deployement is started'
      
   }
}
