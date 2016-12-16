# ami-bake-pipeline
This templates deploys Codepipeline + Lambda + StepFunctions to Automate Custom AMI creation using CloudFormation.

### General Setup 
* Create an s3 bucket in the region you want to deploy, upload these 2 files from this repo (Take note of the bucket you created.). 
"{thisrepo}/sfn_functions/execute_states_machine.zip"
"{thisrepo}/sfn_functions/create_states_machine.zip"
( THIS STEP IS NEEDED BECAUSE AT THE POINT WHEN THIS DOCUMENT IS MADE LAMBDA DOES NOT SUPPORT BOTOCORE 1.4.2 )


* Create a CloudFormation stack using template "{thisrepo}/pipeline_templates/ami-baking-pipeline.yaml".  
When asked for "StepFunctionsCodeBucket" parameter enter the name of the bucket you previously created.
Take note of the stack name you created (This Deploys all the functions and pipelines). 
We will refer this stack as {PipelineStack} going forward.

* Create the second CloudFormation stack using template "{thisrepo}/pipeline_templates/ami-baking-loggroup.yaml". 
(When asked for 'PipelineStackName' ( aka {PipelineStack} ) enter the name of the stack you created in the previous step).
We will refer this as {LogGroupStack} going forward.

* Open CodeCommit Console, and locate the 2 repositories created named :
{PipelineStack}-coderepo and {PipelineStack}-userconfig

* Clone these 2 repositories locally. (follow below instructions)
http://docs.aws.amazon.com/codecommit/latest/userguide/how-to-connect.html

### Use Guide
* modify "{thisrepo}/[linux_builder|windows_builder]/bootstrap-metadata.json with the desired CloudFormation Init config to build the AMI. (refer to below guide for details)
http://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-init.html

* modify "{thisrepo}/[linux_builder|windows_builder]/build-stack-configuration.json the correct parameter values.

[build-stack-configuration.json]
```sh
"InstanceType": "r4.4xlarge" 			--> This will be the Instance type you want to use to bake the image
"BaseAMI": "ami-24e64944", 				--> This will be the base public AMI ID in the region you want to use.
"Keypair": "us-west-2", 				--> This will be the keypair to assign to the Instance.
"PipelineStack": "WIN2012-bake-pipe" 	--> This will need to be the {PipelineStack} name you created previously. 
											(NEEDED FOR SENDING LOGS TO CLOUDWATCH)
```

[test-stack-configuration.json]
```sh
"InstanceType": "r4.4xlarge" 			--> This will be the Instance type you want to use to launch the test instance
"BaseAMI": "******", 					--> This will be updated by the pipeline, you can leave it blank
"Keypair": "us-west-2", 				--> This will be the keypair to assign to the Instance.
"PipelineStack": "WIN2012-bake-pipe" 	--> This will need to be the {PipelineStack} name you created previously.
											(NEEDED FOR SENDING LOGS TO CLOUDWATCH)
```

### Building Linux AMI   
To deploy "{thisrepo}/linux_builder/*" consists of all the json files you need to commit to the "{PipelineStack}-coderepo" and "{PipelineStack}-userconfig" 
Before commiting, please refer to the "Use Guide" above ensure the parameters are adjusted accordingly to your use.

* Download and Commit below files into the designated repositories created by the pipeline stack above.
```sh
"{thisrepo}/linux_builder/test-stack-configuration.json" ---> "{PipelineStack}-coderepo" 
"{thisrepo}/linux_builder/build-stack-configuration.json" ---> "{PipelineStack}-coderepo"
"{thisrepo}/linux_builder/test_ami.json" ---> "{PipelineStack}-coderepo"
"{thisrepo}/linux_builder/build_ami.json" ---> "{PipelineStack}-coderepo"
"{thisrepo}/linux_builder/bootstrap-metadata.json" ---> "{PipelineStack}-userconfig"
```
### Building Windows2012 AMI
To deploy "{thisrepo}/win2012_builder/*" consists of all the json files you need to commit to the "{PipelineStack}-coderepo" and "{PipelineStack}-userconfig" 
Before commiting, please refer to the "Use Guide" above ensure the parameters are adjusted accordingly to your use.

* Download and Commit below files into the designated repositories created by the pipeline stack above.
```sh
"{thisrepo}/win2012_builder/test-stack-configuration.json" ---> "{PipelineStack}-coderepo" 
"{thisrepo}/win2012_builder/build-stack-configuration.json" ---> "{PipelineStack}-coderepo"
"{thisrepo}/win2012_builder/test_ami.json" ---> "{PipelineStack}-coderepo"
"{thisrepo}/win2012_builder/build_ami.json" ---> "{PipelineStack}-coderepo"
"{thisrepo}/win2012_builder/bootstrap-metadata.json" ---> "{PipelineStack}-userconfig"
```

