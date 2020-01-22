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
	stage ('checkout')
    {
		steps
		{
			echo  " ********** Clone starts ******************"
		    checkout scm	 
		}
    }
    stage ('nuget')
    {
		steps
		{
			sh "dotnet restore"	 
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
