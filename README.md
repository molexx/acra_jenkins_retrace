acra_jenkins_retrace
====================

bash script to de-obfuscate stacktraces using the Acralyzer URL and access to Jenkins build dirs


##Requirements

####commandline tools
curl - a http downloader, you probably already have it
jq - a jquery parser - http://stedolan.github.io/jq/ - available in Fedora repo
  
  
  
####Build environment
Jenkins should be set to archive the mapping.txt file, this can be done in the 'Post-build Actions' part, add an 'Archive the artifacts' and include 

      bin/proguard/mapping.txt
     	
in the comma-separated list.


The ACRA report needs to include Jenkins' build number somewhere, such as the app's version string. 

Currently the mapping.txt is read from the local filesystem so the script must be run on a machine with filesystem access to Jenkins' jobs directory. It could be enhanced download this via HTTP using curl.


####Configuration
You'll probably need to edit some of the vars set at the top of the file:  

      JENKINS_JOB_NAME              defaulted to 'master', can be changed here or overidden by setting an environment variable  
      JENKINS_BUILDS                filesystem path to your job's builds directory  
      EXTRACT_BUILD_NUM_FROM_FIELD  the field in the ACRA report that contains your Jenkins build number, e.g. 'APP_VERSION_NAME'
      EXTRACT_BUILD_NUM_REGEX       regex to extract the build number from that field. As an example, mine is:
                                      0.19beta-ert277_aaec7b9
                                    and the regex:  
                                      's/.*-ert(.*)_.*/\1/p'
                                    extracts the number '277' which has been set into AndroidManifest.xml from Jenkins' BUILD_NUMBER at build time.
                                    
