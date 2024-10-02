## Prerequisites:
- Java installed
- Maven installed
    ```sudo apt install maven```
- CodeArtifact Domain and Repository exists
- AWS CLI access to CodeArtifact
- **User cannot be part of "op_base_permissions(_console)" group**

## Instructions:
(examples from opllc aws account)
- Account: 088575927460
- CodeArtifact Domain: common-op-module-domain
- CodeArtifact Repostitory: common-op-module-repo

#### Pull and build stripes-jakarta
```
git clone git@github.com:onepackius/stripes-jakarta.git
cd stripes-jakarta
mvn clean install
```
#### Pull and package stripes-spring-boot
```
cd ~
git clone git@github.com:onepackius/stripes-spring-boot.git
cd stripes-spring-boot
mvn clean package
```
#### Create mvn settings file
```
mkdir -p ~/.m2
touch ~/.m2/settings.xml
```
#### Add below to ~/.m2/settings.xml
```
sudo vi ~/.m2/settings.xml

#############################
<settings>
  <servers>
    <server>
      <id>common-op-module-domain-common-op-module-repo</id>
      <username>aws</username>
      <password>${env.CODEARTIFACT_AUTH_TOKEN}</password>
    </server>
  </servers>
</settings>
#############################
```
#### Add below to ~/stripes-spring-boot/pom.xml
```
vi ~/stripes-spring-boot/pom.xml
#############################
    <distributionManagement>
      <repository>
        <id>common-op-module-domain-common-op-module-repo</id>
        <name>common-op-module-domain-common-op-module-repo</name>
        <url>https://common-op-module-domain-088575927460.d.codeartifact.us-east-1.amazonaws.com/maven/common-op-module-repo/</url>
      </repository>
    </distributionManagement>
#############################
```
#### Export Auth token to env vars:
```
export CODEARTIFACT_AUTH_TOKEN=`aws codeartifact get-authorization-token --domain common-op-module-domain --domain-owner 088575927460 --region us-east-1 --query authorizationToken --output text`
```
#### Publish to CodeArtifact
```
cd ~/stripes-spring-boot

mvn deploy -DaltDeploymentRepository=common-op-module-domain-common-op-module-repo::default::https://common-op-module-domain-088575927460.d.codeartifact.us-east-1.amazonaws.com/maven/common-op-module-repo/
```


