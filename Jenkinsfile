pipeline{
	agent any


environment
{
    scannerHome = 'D:/SonarQube'   
}
	
options
   {
      timeout(time: 1, unit: 'HOURS')
      
      // Discard old builds after 5 days or 5 builds count.
      buildDiscarder(logRotator(daysToKeepStr: '5', numToKeepStr: '5'))
	  
	  //To avoid concurrent builds to avoid multiple checkouts
	  disableConcurrentBuilds()
   }
   
  
     
stages
{
	stage ('Checkout Branch')
    {
		steps
		{
		    checkout scm	 
		}
    }

	 stage ('Restoring Nuget')
    {
		steps
		{
			sh "dotnet restore"	 
		}
    }
	stage ('Clean Code')
    {
		steps
		{
			echo "${env.JAVA_HOME}"
			sh "dotnet clean"	 
		}
    }
	stage ('Building Code')
	{
		steps
		{
			sh "dotnet build -c Release -o DevopsApp/app/build"
		}	
	}
	stage ('Release Artifacts')
	{
	    steps
	    {
	        sh "dotnet publish -c Release -o DevopsApp/app/publish"
	    }
	}
	
	stage ('Docker Image')
	{
		steps
		{
		    sh returnStdout: true, script: 'docker build --no-cache -t devopsapp_vipulchohan:${BUILD_NUMBER} .'
		}
	}
	
	stage ('Stop Running container')
	{
	    steps
	    {
	        sh '''
				ContainerIDByName=$(docker ps | grep devopsAppRun | cut -d " " -f 1)
                if [  $ContainerIDByName ]
                then
					echo "Inside ContainerIDByName"
                    docker stop $ContainerIDByName
                    docker rm -f $ContainerIDByName
                fi
			
                ContainerID=$(docker ps | grep 5400 | cut -d " " -f 1)
                if [  $ContainerID ]
                then
					echo "Inside ContainerID"
                    docker stop $ContainerID
                    docker rm -f $ContainerID
                fi
				
				
            '''
	    }
	}
	stage ('Docker deployment')
	{
	    steps
	    {
	       sh 'docker run --name devopsAppRun -d -p 5400:80 devopsapp_vipulchohan:${BUILD_NUMBER}'
	    }
	}
	
}

 post {
        always 
		{
			echo "*********** Executing post tasks like Email notifications *****************"
        }
    }
}
