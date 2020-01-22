pipeline{
	agent any

environment
{
    scannerHome = tool name: 'sonar_scanner_dotnet', type: 'hudson.plugins.sonar.MsBuildSQRunnerInstallation'   
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
				sh '''dotnet ${scannerHome}/SonarScanner.MSBuild.dll begin /k:$JOB_NAME /n:$JOB_NAME /v:1.0'''   
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
				sh '''dotnet ${scannerHome}//SonarScanner.MSBuild.dll end'''
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
	
}

 post {
        always 
		{
			echo "*********** Executing post tasks like Email notifications *****************"
        }
    }
}
