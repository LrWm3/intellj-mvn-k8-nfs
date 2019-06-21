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
5. I get the deploy-file plugin for maven if it isn't built in by default (it might be)
6. I get a sample jar project from the fabric8-maven plugin repo
   1. need a sample with a dockerfile, jar and yaml
7. I build the project
8. I get a sample pom.xml from the fabric8-maven plugin repo
9. I run the fabric8 job to create a deployment in kubernetes
10. I see the kubernetes deployment exists in intellj

At this point I have the basics working and most of the risk is eliminated; however, I will want to be deploying my
jar to an nfs mount to match what I'm attempting at work.

### Throw NFS into the mix

This is one direction of the complete pipeline

1. I add in an scp-copy to the deployment
   1. I target double-dutch.ca/public as my test scp copy, somehow
   2. `ssh admin@double-dutch.ca` will do the trick
   3. it deploys to a parent dir based on my username, and then uses a pom.xml property for the next dir name
2. I adjust the kubernetes deployment to mount the jar as an nfs mount and run it using the jar command or whatever
3. I see the kubernetes deployment exists in intellj

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
