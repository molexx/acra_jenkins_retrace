acra_jenkins_retrace
====================

bash script to de-obfuscate stacktraces using the Acralyzer URL and access to Jenkins build dirs


##Requirements

####commandline tools
curl - a http downloader, you probably already have it  
jq - a JSON parser - http://stedolan.github.io/jq/ - available in Fedora repo
  
  
  
####Build environment
Jenkins should be set to archive the mapping.txt file, this can be done in the 'Post-build Actions' part, add an 'Archive the artifacts' and include 

      bin/proguard/mapping.txt
     	
in the comma-separated list.


The ACRA report needs to include Jenkins' build number somewhere, such as the app's version string.  
  
  
  
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
  
  
  
####Script execution

Currently the mapping.txt is read from the local filesystem so the script must be run on a machine with filesystem access to Jenkins' jobs directory. It could be enhanced to download this via HTTP using curl.

ANDROID_HOME should be set so it can locate retrace.sh which is included in the SDK.

Ensure the file is executable (chmod ugo+x acra-retrace) then call it with one parameter - the URL of your report in Acralyzer e.g.:

      ./acra-retrace http://acme.yourhost.com/acralyzer/_design/acralyzer/index.html#/report-details/myapp/ee4553-cc7e-52d9-c528-a2ce74a62f7c
  
  
  
####Future
*   How do we trigger this nicely when looking at the Acralyzer web interface?
*   Pass authentication details to the ACRA report site
*   Get mapping.txt from Jenkins over HTTP, possibly with authentication
*   Automatically update all the stacktraces in the database, I assume couch allows updates via HTTP POSTs so a bit more curl, some looping and maybe cron could automate the entire thing and have the de-obfuscated trace available in Acralyzer’s UI.
