# **DEPRECATED**


# Command Line Proxy Installer
If you are looking for information how to install Spacewalk Proxy see [[HowToInstallProxy]] page

## Overview


Historically when Satellite and RHN Hosted was one team and releases of code for both happened at the same time, we choose to implement the Proxy installation process based on code changes that had to be implemented for each Proxy release within both Satellite and RHN Hosted. This issue continues into the Spacewalk release. This Proxy installer code is also written and maintained within our perl stack. We have a continued task to move perl pages to java, or retire the need for those perl pages. 

## Requirement
 

Since we can not commit to hosted, nor does this code make a huge amount of sense for the future of Spacewalk, then future changes in hosted code of Proxy installer will be harder and harder. Therefore we should aim to have standalone installer that is not dependent on on server side code for the Proxt installation process to complete. 
* Legacy RHN Proxy's will not move to this new solution and continue to use the web based installer. 
 * We plan to release a Technology Preview of this solution with the Satellite 5.2 release, also in part in Spacewalk 0.1 release. 
 * We plan to have full release within Spacewalk 0.3 code
## Specification

Below is outline to show how we plan to implement a solution. 


Code currently grow in /proxy/installer/ and goal is to have Command Line (CLI) installer, with this process:

 # yum install spacewalk-proxy

 # configure-proxy.sh
### Spec More details

 * OS requirements?

 * What assumptions are made for code in channels? 
 * What do we have so far - packaging?
 * packages/code in channels - how will this work for Proxy in Spacewalk 
  * wiki doc outline how to get Proxy in Spacewalk built, pushed into channels and usable?
 * Outstanding issues?
