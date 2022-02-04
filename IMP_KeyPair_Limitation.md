https://binx.io/blog/2017/10/25/deploying-private-key-pairs-with-aws-cloudformation/

In AWS CloudFormation there is no way to generate a private key pair. As a result, you always have manual work. You need to generate a ssh key, import it into AWS and finally pass the name to your CloudFormation template. This is clumsy, manual work which prevents us from fully automating the deployment of our infrastructure.

Fork me on GitHubWith Custom CloudFormation Resource we put an end to that. RSA keys are generated as a CloudFormation Resource and the private key is stored in the EC2 parameter store, while the public key can be imported as a EC2 key pair. This means that we fully automated the deployment of our infrastructure, while at the same time we get to control access to the private key.

Resources:
PrivateKey:
Type: Custom::RSAKey
Properties:
Name: /demo/private-key
ServiceToken: !Sub 'arn:aws:lambda:${AWS::Region}:${AWS::AccountId}:function:binxio-cfn-secret-provider'
KeyPair:
Type: Custom::KeyPair
DependsOn: PrivateKey
Properties:
Name: CustomKeyPair
PublicKeyMaterial: !GetAtt 'PrivateKey.PublicKey'
ServiceToken: !Sub 'arn:aws:lambda:${AWS::Region}:${AWS::AccountId}:function:binxio-cfn-secret-provider'

Installation
To install these Custom Resources, type:

git clone https://github.com/binxio/cfn-secret-provider.git
cd cfn-secret-provider

aws cloudformation create-stack \
 --capabilities CAPABILITY_IAM \
 --stack-name cfn-secret-provider \
 --template-body \
 file://cloudformation/cfn-custom-resource-provider.yaml

aws cloudformation wait stack-create-complete \
 --stack-name cfn-secret-provider
