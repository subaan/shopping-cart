node {

    environment {
        PATH = '$PATH:/home/appranix/.rvm/gems/ruby-2.2.4/bin/prana'
    } 
   stage('Build') {
         git 'https://github.com/subaan/shopping-cart.git/'
         sh 'mvn package'
   }

stage("publish to s3") {
    step([
        $class: 'S3BucketPublisher',
        entries: [[
            sourceFile: '**/jobs/shopping-cart/*.war',
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

stage('Initiate prana deployment') {


       sh 'echo successfully logged in prana'

       sh 'echo organization is set as devorg'

       sh 'echo assembly is set as shopping-cart'

       sh 'echo Transition variable update build ver'

       sh 'echo Transition variable update build ver'

       sh 'echo deployement is started'
      
   }
}
