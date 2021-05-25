---
description: Consolidated groovy functions for Jenkins administration
---

# Groovy for Administration

```groovy
//runAllJobs(); // to run all the jobs available
//clearQueue(); // clear all the queue items
//countQueueItems(); // to show the count of queue items

//runFailedJobs(); // this will look for failed jobs and the jobs which were never executed
//showFailedJobs(); // prints the failed and never executed jobs 
//countFailedJobs(); // count of failed/never executed jobs

//showSuccessfulJobs(); // prints the successful jobs
//countSuccessJobs(); // count of successful jobs
//showAll()
//change()
//=([0-9][0-9]\.[0-9][0-9]\.[0-9][0-9][0-9])$
//disableJobs(".*-SNAPSHOT");
//enableJobs(".*-SNAPSHOT");
//disableJobs(".*-BASELINE");
//enableJobs(".*-BASELINE");

def disableJobs(String type){
	def projects = Jenkins.instance.items.findAll{job -> job.displayName.matches(type)};
	for (p in  projects) {
		p.disable();
		println(p.name +" : [DISABLED]")
	}
	println("[DISABLED] total="+projects.size())
}

def enableJobs(String type){
	def projects = Jenkins.instance.items.findAll{job -> job.displayName.matches(type)} ;
	for (p in  projects) {
		p.enable();
		println(p.name +" : [ENABLED]")
	}
	println("[ENABLED] total="+projects.size())
}

def disableCrumb(){
	Jenkins.instance.setCrumbIssuer(null);
// curl --silent --user <username>:<pass> http://tele523:12131/crumbIssuer/api/xml?xpath=concat\(//crumbRequestField,%22":"%22,//crumb\)
}

def runAllJobs(){
    jobs = hudson.model.Hudson.instance.items.findAll{job -> job.displayName.matches(".*-SNAPSHOT")} 
    println("Triggering "+jobs.size()+" jobs");
    jobs.each{run -> run.scheduleBuild()};
}
def runFailedJobs(){
    showFailedJobs();
    jobs.reverse().each{run -> run.scheduleBuild()};
}

def clearQueue(){
    countQueueItems();
    Hudson.instance.queue.clear();
}

def countQueueItems(){
    def queue = Hudson.instance.queue
    println "Queue contains ${queue.items.length} items"
}

def showSuccessfulJobs(){
    countSuccessJobs();
    jobs.each{job -> println(job.displayName)};
}
def showFailedJobs(){
    countFailedJobs();
    jobs.each {job -> println(job.displayName)};
}
def countSuccessJobs(){
  jobs = hudson.model.Hudson.instance.items.findAll{
  job -> job.displayName.matches(".*-SNAPSHOT") &&
      (job.getLastBuild() == job.getLastSuccessfulBuild() && job.getLastBuild() != null)} 
  println("Successful jobs: "+jobs.size());
}

def countFailedJobs(){
  jobs = hudson.model.Hudson.instance.items.findAll{
      job -> job.displayName.matches(".*-SNAPSHOT") &&
       (job.getLastBuild() != job.getLastSuccessfulBuild() || job.getLastBuild() == null)} 
  println("Failed jobs: "+jobs.size());
}

def change(){

  jobs = hudson.model.Hudson.instance.getItems(hudson.maven.MavenModuleSet.class);
  jobs.each{job -> 
   if(job.displayName.matches(".*-SNAPSHOT")){
    //job.mavenOpts = "-Dfile.encoding=Cp1252";
	//job.mavenName = "Maven3.6";
    job.goals = "-Dver-telenet-version=20.60-SNAPSHOT -Dmaven.test.skip=true -Dsonar.perform.qualitycheck=false clean install -X";
    job.save();
    //println(job.displayName);
   }
  }
}

def change2(){
 def list = ["srv.service.service","srv.service.servicemgmt"];
  jobs = hudson.model.Hudson.instance.getItems(hudson.maven.MavenModuleSet.class);
  jobs.each{job -> 
	  idx = job.displayName.indexOf("-");
	  moduleName= job.displayName.subString(idx as Integer);
	  print moduleName
	  print(list.any { it =~ ".*"+job.displayName+"*" } )  
	}
}


def changeJunit(){
	def list = ["srv.service.service","srv.service.servicemgmt"];
	jobs = hudson.model.Hudson.instance.getItems(hudson.maven.MavenModuleSet.class);
	jobs.each{job -> 
		release= job.displayName.substring(job.displayName.indexOf("_").toInteger());
		list.each{module ->
			if (job.displayName.contains(module+release)){
				if(!job.goals.contains("junit-cqa")){
					job.goals = "be.telenet:maven2.plugin:junit-cqa "+job.goals;
					job.save();
					println "[Success] "+job.displayName + " configured !!";
				}else{
					println "[Skipped] "+job.displayName + " already configured !!";
				}
			}
		}
	}
}

def replaceJunit(){
	def list = ["srv.service.service","srv.service.servicemgmt"];
	jobs = hudson.model.Hudson.instance.getItems(hudson.maven.MavenModuleSet.class);
	jobs.each{job -> 
		release= job.displayName.substring(job.displayName.indexOf("_").toInteger());
		list.each{module ->
			if (job.displayName.contains(module+release)){
				if(job.goals.contains("junit-cqa")){
                    def tmp = job.goals.replace("be.telenet:maven2.plugin:21.40-SNAPSHOT:junit-cqa","be.telenet:maven2.plugin:21.30-SNAPSHOT:junit-cqa");
					job.goals = tmp;
					job.save();
					println "[Success] "+job.displayName + " configured !!";
				}else{
					println "[Skipped] "+job.displayName + " already configured !!";
				}
			}
		}
	}
}


def triggerList(){
	def list = ["srv.service.service"];
	jobs = hudson.model.Hudson.instance.getItems(hudson.maven.MavenModuleSet.class);
	jobs.each{job -> 
		moduleExt= job.displayName.substring(job.displayName.indexOf("_").toInteger());
		list.each{module ->
			if (job.displayName.contains(module+moduleExt)){
				job.scheduleBuild();
				println "[Success] "+job.displayName + " triggered !!";
				
			}
		}
	}
}

def showAll(){

    jobs = hudson.model.Hudson.instance.items.findAll{job -> job.displayName.matches(".*-SNAPSHOT")} 
    println("All "+jobs.size()+" jobs");
    jobs.each{job -> 
		if(job.displayName.matches(".*-SNAPSHOT")){
		println(job.displayName);
   }
  }
}

def durationHudson() {
  jobs = hudson.model.Hudson.instance.items.findAll {
    job -> job.getLastBuild() != null;
  };
  
  jobs.each {
    job -> 
      def build = job.getLastBuild();
	  def name = job.getName();
      def duration = job.getEstimatedDuration() / 60000;
      println "Build: ${build } | Duration: ${duration} mins ";
    
  };
}

def duration() {
  jobs = hudson.model.Hudson.instance.items.findAll {
    job -> job.getLastBuild() != null;
  };
  
  jobs.each {
    job -> 
      def build = job.getLastBuild();
	  def name = job.getName();
      def time = build.getDuration();
      def duration = time / 60000;
      println "${name},${duration}";
    
  };
}

def allWithCause(){
	Jenkins.instance.getAllItems(Job).each{
		def jobBuilds=it.getBuilds()
		jobBuilds.each { build ->
			def runningSince = groovy.time.TimeCategory.minus( new Date(), build.getTime() )
			def currentStatus = build.buildStatusSummary.message
			def cause = build.getCauses()[0] //we keep the first cause
			def user = cause instanceof Cause.UserIdCause? cause.getUserId():""
			println "Build: ${build} | Since: ${runningSince} | Status: ${currentStatus} | Cause: ${cause} | User: ${user}"
			def parameters = build.getAction(ParametersAction)?.parameters
			parameters.each {
				println "Type: ${it.class} Name: ${it.name}, Value: ${it.dump()}" 
			}
		}
	}//failed
}

def failed(){
	Jenkins.instance.getAllItems(Job).each{
		def jobBuilds=it.getBuilds()
		jobBuilds.each { build ->
			//def runningSince = groovy.time.TimeCategory.minus( new Date(), build.getTime() )
			def currentStatus = build.buildStatusSummary.message
			def cause = build.getCauses()[0] //we keep the first cause
			
			//def reason = cause instanceof Cause.LegacyCodeCause ? cause:""
			
			println "Build: ${build} | Status: ${currentStatus} | Cause: ${cause} "
			def parameters = build.getAction(ParametersAction)?.parameters
			parameters.each {
				println "Type: ${it.class} Name: ${it.name}, Value: ${it.dump()}" 
			}
		}
	}//failed
}

```

