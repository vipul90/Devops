pipeline{
	agent any
	
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
	
}

 post {
        always 
		{
			echo "*********** Executing post tasks like Email notifications *****************"
        }
    }
}
