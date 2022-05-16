The root stack is where all stack actions are performed such as update and delete of the stack. This is where you import the nested stack. When you create the root stack, the nested stack will be created (as its template URL has ben defined in the stack resource)

The nested stack can be uploaded to S3. Versioning should be enabled on the bucket and the template files should be publicly accessible (verify you can download the cfn template files in incognito). 
To pass parameters values to the nested stack, use THE root stack. The same parameters(defined in the nested stack) can be defined in the root stack parameters section and referenced in the root template stackresource.

To use an attribute of a resource from the nested stack, the nested stack must expose the attribute via the Output template section; then it must be referenced from the root stack using the !GetAtt function in this format: Outputs.NestedStackOutputName E.g. !GetAtt VPCStack.Outputs.VPCId

  SecurityGroupStack:
    Type: AWS::CloudFormation::Stack
    Properties:
      Parameters:
        VPCId: !GetAtt VPCStack.Outputs.VPCId
      TemplateURL: https://nestedbucket2022.s3.us-east-2.amazonaws.com/nestedstacks/13-03-NestedStack-SG_mycopy.yml # Required
      TimeoutInMinutes: 5

To update a nested stack, update the cfn template file and upload to the s3 bucket where the previous file existed(with versioning enabled). Then in CFN portal, choose Upate the stack -> Select Use current template and execute. Cloudformation will pick up the latest version from S3 and modify the nested stack.

When you delete the root stack, all nested templates will be deleted. Check the 