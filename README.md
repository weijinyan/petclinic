# petclinic
This is a sample project for pipeline


Add parameter "AutomationTestPath" in the pipeline job
Add Parameter "DeployScriptFolder" in the pipeline job

1.Config Jenkins

Manage Jenkins->Manage Plugins->Avaliable->Search and install plugins
Pipeline
Github
Sonar
Jacoco
Blue Ocean(optional)

Manage Jenkins->Configure System->Add SonarQube Server

Manage Jenkins->Configure System->Jenkins Location

Manage Jenkins->Global Tool Configuration ->Bind jdk8


Manage Jenkins->Global Tool Configuration ->Bind M3

2.Update Maven settings

```bash
<profiles>
	<profile>
		<id>sonar</id>
		<activation>
			<activeByDefault>true</activeByDefault>
		</activation>
		<properties>
			<sonar.host.url>http://localhost:9000</sonar.host.url>
		</properties>
	</profile>
</profiles>
```

3. Configure SonarQube
SonarQube ->Login with admin role->Administration->Configuration->General Settings->Webhooks->Create 
URL: http://localhost:8080/sonarqube-webhook/

Configuration->General Settings->SCM->Disable SCM Sensor


4. Tomcat for Jacoco
Open Catalina.sh, find JAVA_OPTS="JAVA_OPTS -Djava

Add '-javaagent:pathofJacocoagentjar=output=tcpserver,address=docker container ip,port=6300'


