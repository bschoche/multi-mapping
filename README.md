# Multiple SAM Projects sharing an API Gateway Custom Domain #

This demo project is to help teams understand how to separate dependencies for SAM projects
so that Custom Domain Names can be shared across the projects.  This can help alleviate
any dependencies between the development teams of the projects while maintaining a simple
approach to Custom Domains and Base Path Mappings.

### Target Solution ###

  workgroup-dev.domain.com/api1/ ----> apigateway1.region.amazonaws.com/Prod/
  workgroup-dev.domain.com/api2/ ----> apigateway2.region.amazonaws.com/Prod/

### Problem ###

Normally, the developers would put the Custom Domain Name and Base Path Mappings into their SAM template and deploy the solution via 'sam deploy'.  The first project to deploy would then own the Custom Domain Name and a single Base Path Mapping.  The second project would then just use a Base Path Mapping.  If something occurs that requires the first project to remove the Custom Domain Name, it will get an error and be blocked from rollback due to the second Base Path Mapping.

### Pre-Requisites ###

This demo was built with the following already completed:
1. You have a valid AWS account
2. A domain is purchased and has a working DNS provider
3. A certificate is available for the domain via Amazon Certificate Manager.  For this demo the certificate needs to be located in the same region you are deploying into, as the API Custom Domain is setup to be REGIONAL optimized.  You can change the deployment type to be EDGE, which would require the certificate to be located within the us-east-1 region and you will need the RegionalHostedZoneId

### Deployment via SAM ###
[SAM documentation can be found here](https://docs.aws.amazon.com/serverless-application-model/latest/developerguide/serverless-sam-cli-command-reference.html).

You can deploy these templates by using sam build, sam package, and sam deploy.  I used:
1. sam build
2. sam package --s3-bucket <yourbucket> --output-template-file <packagednamedtemplate.yaml>
3. sam deploy --template-file <packagednamedtemplate.yaml> --stackname <nameofproject> --capabilities CAPABILITY_IAM

### Deployment Order ###

Use this deployment order to be able to stand up the example:

1. toplevel
2. api1
3. api2
4. mapping1
5. mapping2

You can then modify api1 or api2 to return a different value or perform some other action and redeploy the applications without worry of dependencies.  However, if you take an action that will delete and replace the API Gateway instance, you will need to delete and re-run mapping[api#], as the existing mapping will be invalid.

### Cleanup ###

Using the CloudFormation console, delete the projects in order:

1. mapping2
2. mapping1
3. api2
4. api1
5. toplevel
