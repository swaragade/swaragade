---
description: >-
  This script can be imported into a pipeline job and methods can be called.
  Works like a shared kind of library.
---

# Importing script in pipeline

```groovy
//importing script
def administration = evaluate readFile("bin/jenkins_administration.groovy");
administration.processAutoBaseline("${SOURCE_VERSION}", "${TARGET_VERSION}", "${RECON_NUM}",params.TRIGGER_BASELINE_JOBS);
```

```groovy
import jenkins.*;
import jenkins.model.*;
import hudson.*;
import hudson.model.*;

//disableJobs(".*-SNAPSHOT");
//disableJobs(".*-BASELINE");
def disableJobs(String type){
	def projects = Jenkins.instance.items.findAll{job -> job.displayName.matches(type)};
	for (p in  projects) {
		p.disable();
		println(p.name +" : [DISABLED]")
	}
	println("[DISABLED] total="+projects.size())
}

//enableJobs(".*-SNAPSHOT");
//enableJobs(".*-BASELINE");
def enableJobs(String type){
	def projects = Jenkins.instance.items.findAll{job -> job.displayName.matches(type)} ;
	for (p in  projects) {
		p.enable();
		println(p.name +" : [ENABLED]")
	}
	println("[ENABLED] total="+projects.size())
}

// triggers all active baseline jobs
def triggerActiveBaselineJobs(){
    println "Triggering all active baseline jobs : Started";
	jobs = jenkins.model.Jenkins.instance.getItems(hudson.maven.MavenModuleSet.class);
	jobs.each{job -> 
		if (job.displayName.contains(".*-BASELINE")){
				job.scheduleBuild();
				println "[Success] "+job.displayName + " triggered !!";
				
		}
	}
    println "Triggering all active baseline jobs : Complete";
}

//getVRP("branch_rel_21_50");
def getVRP(String branch){
    def cmd = ['/bin/sh',  '-c',  'git archive --remote=ssh://git@stash.rel.corp.telenet.be:7999/tina/release.git ' + branch + ' custom/versions-release.properties | tar -xO custom/versions-release.properties > versions-release.properties'];
    cmd.execute().with{
        def output = new StringWriter();
        def error = new StringWriter();
        it.waitForProcessOutput(output, error);

        if(it.exitValue() != 0){
          	println "$error";
            throw new Exception("Some error occurred while getting VRP");
        }else{
           println "Got VRP from "+branch;
        }
    }
}

def isVRPexists(String fileName){
    File file = new File(fileName);
    return file.exists();
}

//changeAndBuild(arr, 21.50, 028, true? trigger the job once configured : dont trigger (manual trigger is available))
def changeAndBuild(String[] modulesInRecon, String trgVerison, boolean trigger){
    //def vrp = "versions-release.properties";
  	//def vrpLines = null;
    
    //if(isVRPexists(vrp)){
    //    vrpLines = new File(vrp).readLines()
    //}
    modulesInRecon.each{
        reconModule -> 
            //if(vrpLines != null){
                String[] reconModuleArr = reconModule.split(":");
                String moduleName = reconModuleArr[0];
                String jobName = moduleName+"_"+trgVerison+"-BASELINE";
                Job job = jenkins.model.Jenkins.instance.getItem(jobName);
                job.enable();
                println "[INFO] ${jobName} enabled"
                //def vrpModule = "ver-telenet-"+moduleName+"=";
                //def result = vrpLines.findAll { it.contains(vrpModule) };
                //def vrpLine = result[0].toString();
                //if(result != null && result.contains("=")){
                //    String[] versionArr = result.split("=");
                //    String r_tag = "R_"+versionArr[1].replace(".","_");
                String r_tag = reconModuleArr[1];
                   changeBranch(job,r_tag);
                    if(trigger){
                        job.scheduleBuild();
                    }
                //}else{
                //    throw new Exception("unable to find module in vrp : "+ vrpModule);
                //}
            //}
        
    }
}

def changeBranch(Job job, String branchName){
    try{
        if(!job.scm.branches.contains(branchName)){
            job.scm.branches[0].name=branchName;
            job.save();
            if(job.scm.branches[0].name.contains(branchName)){
                println  "[INFO] job config changed for ${job.displayName} | ${branchName}";
            }else{
                println "[Error] could not configure job : ${job.displayName} | ${branchName}";
            }
         }else{
             println "[INFO] job already configured : ${job.displayName} | ${branchName}";
         }
    } catch(Exception e){
      	println "[Exception] cannot configure job : ${job.displayName} | ${e.getMessage()}";
    }
}

// rest call to RMC
def getModulesInRecon(String reconNumber){
    def postmanGet = new URL('http://tele523.corp.telenet.be:8082/pmc/resource/pmc/restv1/pmcServices/getReconModuleList/'+reconNumber);
    def getConnection = postmanGet.openConnection();
    getConnection.requestMethod = 'GET';
    def respCode = getConnection.responseCode;
    if(respCode < 300){
        String[] moduleList = getConnection.getInputStream().getText().split();
        println "moduleList ${moduleList}"
        return moduleList;
    }else{
        throw new Exception("Response code ${respCode} received")
    }
}

//processAutoBaseline("21.40", "21.50", "028")
def processAutoBaseline(String srcVersion, String trgVerison , String reconNumber, boolean trigger ){
    String reconFullName = srcVersion+"."+reconNumber;
    String[] reconModules = getModulesInRecon(reconFullName);
    println "[INFO] ${reconModules.size()} modules found in RMC";
    //String suf = srcVersion.replace(".","_");
    //String branchName = "branch_rel_"+suf;
    //getVRP(branchName);
    changeAndBuild(reconModules, trgVerison, trigger);
}

return [
    getVRP: this.&getVRP,
    changeAndBuild: this.&changeAndBuild,
    processAutoBaseline: this.&processAutoBaseline,
    getModulesInRecon: this.&getModulesInRecon,
    changeBranch: this.&changeBranch,
    isVRPexists: this.&isVRPexists,
    triggerActiveBaselineJobs: this.&triggerActiveBaselineJobs,
    enableJobs: this.&enableJobs,
    disableJobs: this.&disableJobs
]
```

