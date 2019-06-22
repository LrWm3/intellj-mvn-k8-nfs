# intellj-mvn-k8-nfs

A project in which I attempt to combine kubernetes with intellj and nfs with maven deployment jobs.

## The rough idea

Here's my rough plan of attack

### Basic intellj, k8 and fabric8-maven integration

In which I get things working together more or less

1. I get intellj
2. I get the kubernetes plugin for intellj
3. I get maven
4. I get the fabric8-maven plugin for maven
5. I get a sample jar project from the [fabric8-maven plugin repo](https://github.com/fabric8io/fabric8-maven-plugin)
   1. need a sample with a dockerfile, jar and yaml
6. I build the project
7. I get a sample pom.xml from the fabric8-maven plugin repo
8. I run the fabric8 job to create a deployment in kubernetes
9. I see the kubernetes deployment exists in intellj

At this point I have the basics working and most of the risk is eliminated; however, I will want to be deploying my
jar to an nfs mount to match what I'm attempting at work.

### Throw NFS into the mix

This is one direction of the complete pipeline

1. I get the deploy:file if its not already in maven by default
1. I add in an scp-copy to the deployment
   1. I target double-dutch.ca/public as my test scp copy, somehow
   2. `ssh admin@double-dutch.ca` will do the trick
   3. it deploys to a parent dir based on my username, and then uses a pom.xml property for the next dir name
1. I adjust the kubernetes deployment to mount the jar as an nfs mount and run it using the jar command or whatever
1. I see the kubernetes deployment exists in intellj

### Rollback is important too

Next is the ability to rollback a deployment

1. I create an 'original' deployment
2. I add a rollback / undeploy command
3. I run the deploy command
4. I see the deployment was updated to use mounted image in intellj
5. I run the undeploy command
6. I see the deployment has reverted to the original in intellj

This allows for the undeployment of things

## Targeting specific namespaces will be a requirement for usage

It would be nice to deploy to specific namespaces; not sure exactly how this will work.

1. Figure out an appropriate way to set a default namespace in the maven deployment
2. Include the namespace as part of the template pom
3. Run the deployment build pipeline as above and see the result in intellj

## Ok so are you going to actually do something to make this happen or

Yes. Give me some time to heat up dinner first.

### Installing Intellj

I download for mac; i open; i select plugins related to what I'm doing. I open the fabric8 project.

### Adding the kubernetes plugin to intellj

Turns out the kubernetes plugin is for paying users ONLY. So I'll have to inspect the results elsewhere (VSCode?)

### Install maven

brew saves me a few clicks here

```
brew install maven
```

### Install fabric8 plugin for maven

I'm not sure if I even have to do anything here. I'll try and run the job in intellj for the
[fabric8 gitrepo](https://github.com/fabric8io/fabric8-maven-plugin).

Lets see...

I did a build of the entire project and it actually worked. Pretty incredible stuff so far.

I naively try and run the plugin from within the `sample/yaml-only` dir with the following command

```
mvn fabric8:resource
```

```
[INFO] Scanning for projects...
[WARNING] The POM for io.fabric8:fabric8-maven-plugin:jar:4.1-SNAPSHOT is missing, no dependency information available
[WARNING] Failed to retrieve plugin descriptor for io.fabric8:fabric8-maven-plugin:4.1-SNAPSHOT: Plugin io.fabric8:fabric8-maven-plugin:4.1-SNAPSHOT or one of its dependencies could not be resolved: Could not find artifact io.fabric8:fabric8-maven-plugin:jar:4.1-SNAPSHOT
Downloading from central: https://repo.maven.apache.org/maven2/org/codehaus/mojo/maven-metadata.xml
Downloading from central: https://repo.maven.apache.org/maven2/org/apache/maven/plugins/maven-metadata.xml
Progress (1): 2.8/14 kB
Progress (2): 2.8/14 kB | 4.1/20 kB
Progress (2): 5.5/14 kB | 4.1/20 kB
Progress (2): 5.5/14 kB | 7.4/20 kB
Progress (2): 8.3/14 kB | 7.4/20 kB
Progress (2): 8.3/14 kB | 10/20 kB
Progress (2): 11/14 kB | 10/20 kB
Progress (2): 11/14 kB | 13/20 kB
Progress (2): 14 kB | 13/20 kB
Progress (2): 14 kB | 16/20 kB
Progress (2): 14 kB | 18/20 kB
Progress (2): 14 kB | 20 kB

Downloaded from central: https://repo.maven.apache.org/maven2/org/apache/maven/plugins/maven-metadata.xml (14 kB at 32 kB/s)
Downloaded from central: https://repo.maven.apache.org/maven2/org/codehaus/mojo/maven-metadata.xml (20 kB at 48 kB/s)
[INFO] ------------------------------------------------------------------------
[INFO] BUILD FAILURE
[INFO] ------------------------------------------------------------------------
[INFO] Total time:  0.989 s
[INFO] Finished at: 2019-06-20T21:51:34-03:00
[INFO] ------------------------------------------------------------------------
[ERROR] No plugin found for prefix 'fabric8' in the current project and in the plugin groups [org.apache.maven.plugins, org.codehaus.mojo] available from the repositories [local (/Users/wmarsman/.m2/repository), central (https://repo.maven.apache.org/maven2)] -> [Help 1]
[ERROR]
[ERROR] To see the full stack trace of the errors, re-run Maven with the -e switch.
[ERROR] Re-run Maven using the -X switch to enable full debug logging.
[ERROR]
[ERROR] For more information about the errors and possible solutions, please read the following articles:
[ERROR] [Help 1] http://cwiki.apache.org/confluence/display/MAVEN/NoPluginFoundForPrefixException
```

It seems to know what I'm talking about, but it can't find the plugin. So I'll need to install it somehow...

After reading an example by someone else, I switched the version to `4.0.0` and now it runs. So that's good.

I am doing it from the commandline right now, and I'd prefer to figure out how to run it in intellj

I found the maven sidebar in Intellj. So that's done.

Messing around with the fabric8 plugin, I found the following:

- fabric8:resource-apply does what I want in terms of templating and creating pods, basically
- fabric8:undeploy is a delete as far as I can tell, not much of an 'undeploy' if you ask me.

Wait a second...

Why the fuck

are we doing any of this

am I

genuinely

thinking this is wise?

wow.

did it really take me til 10:30 PM at night to hit a moment of clariety?

is the problem really just: we aren't allowed to build docker images on our local machines?

that's... that's a really easy problem to solve, isn't it?

I mean, sure, its annoying to have to do the IT request to get it set up but

its really not THAT bad.

Hmm...

Well...

The fabric8 plugin works REALLY well. Like, why the fuck would we be using anything else kind of well.

HMMMMMM

I suppose I am saying we should be using the fabric8 plugin, and not hacking our way around it.

In order to use the fabric8 plugin, we need to be able to build docker images on our local machines.

That is a perfectly reasonable thing to have and need.

The benefit is we get templated resource files.

The downside is these templated resource files do not match the templated resource files produced by Everis.

OK so yea this works WAY
the fuck
better

than ANYTHING WE HAVE BY A SIGNIFICANT MARGIN

I am seriously going to quit my job and become a farmer if this keeps up

### Becoming a farmer

[Can a sw eng become a farmer?](https://www.quora.com/Can-a-software-engineer-become-a-farmer)
