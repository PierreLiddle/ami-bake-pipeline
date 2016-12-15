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

### Linux AMI Bake 
* Download below files into the local github directory.

### Windows2012 AMI Bake

