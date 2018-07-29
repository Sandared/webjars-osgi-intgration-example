# webjars-osgi-intgration-example
A example how to include OSGi relevant meta data in a webjars bundle.
This is done for Polymer 2.4.0

The relevant parts are:

```
<plugin>
	<groupId>biz.aQute.bnd</groupId>
	<artifactId>bnd-maven-plugin</artifactId>
	<configuration>
		<bnd><![CDATA[
			Bundle-SymbolicName: ${project.groupId}.${project.artifactId}
			Bundle-Version:	 ${project.version}
			Bundle-Description: ${project.description}
			-resourceonly:true
			WebJars-Resource: /META-INF/resources/webjars/${project.artifactId}/${project.version}
			Provide-Capability: ${project.groupId};${project.artifactId}:List<String>=${project.version}
			Require-Capability: org.webjars.bower;filter:="(github-com-webcomponents-shadycss=1.1.1)",\
								org.webjars.bower;filter:="(github-com-webcomponents-webcomponentsjs=1.1.0)"
		]]></bnd>
	</configuration>
	<executions>
		<execution>
			<goals>
				<goal>bnd-process</goal>
			</goals>
		</execution>
	</executions>
</plugin>
<!-- Required to make the maven-jar-plugin pick up the bnd 
generated manifest. Also avoid packaging empty Jars -->
<plugin>
	  <groupId>org.apache.maven.plugins</groupId>
	  <artifactId>maven-jar-plugin</artifactId>
	  <version>3.0.2</version>
	  <configuration>
	      <archive>
		  <manifestFile>${project.build.outputDirectory}/META-INF/MANIFEST.MF</manifestFile>
	      </archive>
	      <skipIfEmpty>true</skipIfEmpty>
	  </configuration>
</plugin>
```

The `WebJars-Resource` header is added so that BundleTrackers in an OSGi environment can search for webjars bundles and are then pointed to the path within the bundle where the resources are.

Each bundle additionally declares a `Provide-Cpability` which can be used by OSGi to automatically resolve dependencies between bundles. This should always be in the form `<Group ID>;<Artifact ID>:List<String>=<Version>`

Dependencies on other bundles can be declared by `Require-Capability` which also should always be in the form `<Group ID>;filter:="(<Artifact ID>=<Version>)"`, but with <Group ID>, <Artifact ID> and version for the respective dependency of course. Multiple dependencies are separated by a `,`. The `\` in the example above is only needed for the linebreak.
Actually all dependencies declared by the POM should be redeclared here.
