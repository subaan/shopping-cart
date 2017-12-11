node {
   stage('Build') {
         git 'https://github.com/subaan/shopping-cart.git/'
         sh 'mvn package'
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
sh 'which ruby'
        //available as an env variable, but will be masked if you try to print it out any which way
       sh 'su /home/appranix/.rvm/gems/ruby-2.2.4/bin/prana auth logout'
       sh 'echo successfully logged out'

       sh "su /home/appranix/.rvm/gems/ruby-2.2.4/bin/prana auth set_token eyhteLMyshgc1vHy69e5"
       sh 'echo successfully logged in'

       sh "/home/appranix/.rvm/gems/ruby-2.2.4/bin/prana config set organization=testorg-test2 -g"
       sh 'echo organization is set as devorg'

       sh "/home/appranix/.rvm/gems/ruby-2.2.4/bin/prana config set assembly=shopping-cart -g"
       sh 'echo assembly is set as shopping-cart'

       sh "/home/appranix/.rvm/gems/ruby-2.2.4/bin/prana configure variable update -a shopping-cart -e prod --platform=tomcat appVersion=${env.BUILD_ID}"
       sh 'echo Transition variable update build ver'

       sh "/home/appranix/.rvm/gems/ruby-2.2.4/bin/prana configure variable update -a shopping-cart -e prod --platform=tomcat jenkinsBuildUrl=http://localhost:8080/job/shopping-cart/${env.BUILD_ID}"
       sh 'echo Transition variable update build ver'

       sh "/home/appranix/.rvm/gems/ruby-2.2.4/bin/prana transition commit init-commit -e prod"

       sh "/home/appranix/.rvm/gems/ruby-2.2.4/bin/prana transition deployment create -e prod"
       sh 'echo deployement is started'
      
   }
}
