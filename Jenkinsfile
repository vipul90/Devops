pipeline{
	agent any

environment
{
    scannerHome = 'C:/Program Files (x86)/Jenkins/tools/hudson.plugins.sonar.MsBuildSQRunnerInstallation'   
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
			sh "dotnet clean"	 
		}
    }
	stage ('Starting Sonarqube analysis')
	{
		steps
		{
			withSonarQubeEnv('Test_Sonar')
			{
				sh "dotnet ${scannerHome}/SonarScanner.MSBuild.dll begin /key:$JOB_NAME /name:$JOB_NAME /version:1.0"
			}
		}
	}
	stage ('Building Code')
	{
		steps
		{
			sh "dotnet build -c Release -o DevopsApp/app/build"
		}	
	}
	stage ('Ending SonarQube Analysis')
	{	
		steps
		{
		    withSonarQubeEnv('Test_Sonar')
			{
				sh "dotnet ${scannerHome}/SonarScanner.MSBuild.dll end"
			}
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
                ContainerID=$(docker ps | grep 5400 | cut -d " " -f 1)
                if [  $ContainerID ]
                then
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
