# AppDynamics ServiceNow REST API Alerting Extension

##Use Case
ServiceNow ([http://www.servicenow.com](www.servicenow.com)) is a software-as-a-service (SaaS) provider of IT service management (ITSM) software. AppDynamics integrates directly with ServiceNow to create tickets in response to alerts. With the ServiceNow integration you can leverage your existing ticketing infrastructure to notify your operations team to resolve performance degradation issues.

**Note**: This extension works only with AppDynamics Health Rule Violation events.

##Prerequisites

To post events to ServiceNow user should have "rest_service" role.

##Installation Steps

 1. Run "mvn clean install"

 2. Find the zip file at 'target/servicenow-api-alert.zip'.

 3. Unzip the servicenow-api-alert.zip file into <CONTROLLER_HOME_DIR>/custom/actions/ . You should have  <CONTROLLER_HOME_DIR>/custom/actions/servicenow-api-alert created.

 4. Check if you have custom.xml file in <CONTROLLER_HOME_DIR>/custom/actions/ directory. If yes, add the following xml to the <custom-actions> element.

   ```
      <action>
		<type>servicenow-api-alert</type>
		<!-- For Linux/Unix *.sh -->
		<executable>servicenow-alert.sh</executable>
		<!-- For windows *.bat -->
		<!--<executable>servicenow-alert.bat</executable>-->
	  </action>
  ```

   If you don't have custom.xml already, create one with the below xml content

      ```
      <custom-actions>
          <action>
            <type>servicenow-api-alert</type>
            <!-- For Linux/Unix *.sh -->
            <executable>servicenow-alert.sh</executable>
            <!-- For windows *.bat -->
            <!--<executable>servicenow-alert.bat</executable>-->
          </action>
      </custom-actions>
      ```
      Uncomment the appropriate executable tag based on windows or linux/unix machine.

    5. Update the config.yaml file in <CONTROLLER_HOME_DIR>/custom/actions/servicenow-api-alert/conf/ directory with the domain, username, password and serviceNowVersion. You can also configure the default service now details like assignmentGroup, assignedTo, callerId, category, location.

###Note
Please make sure to not use tab (\t) while editing yaml files. You may want to validate the yaml file using a yaml validator http://yamllint.com/


```
	#ServiceNow Domain
	domain: "https://sandbox.service-now.com"
	
	#ServiceNow User
	username: "admin"
	
	#ServiceNow Password, provide password or passwordEncrypted and encryptionKey.
	password:
	
	passwordEncrypted: "0LKLGb4hc3pAczHmzxQ5ig=="
	encryptionKey: "welcome123"
	
	#ServiceNow Version
	serviceNowVersion: "Fuji"
    
    #Close notes text to be posted when resolving the incident
    closeNotesText: "This incident is resolved"

	#Proxy server URI
	proxyUri:
	#Proxy server user name
	proxyUser:
	#Proxy server password
	proxyPassword:
	
	#ServiceNow User Specific
	#Please define your ServiceNow specific fields here like assignment_group, assigned_to, caller_id, category and location.
	#The name should be a valid Incident table's column name.
	#Do not add properties short_description, comments, impact and priority here, as they are added by the extenson automatically
	fields:
	   - name: "assignment_group"
	     value: "Capacity Mgmt"
	   - name: "assigned_to"
	     value: "Don Goodliffe"
	   - name: "caller_id"
	     value: "abel.tuter@example.com"
	   - name: "category"
	     value: "request"
	   - name: "location"
	     value: "Denver"
	   - name: "contact_type"
	     value: "email"
```

###Password Encryption
To set passwordEncrypted in config.yaml, follow the steps below:

1. Download the util jar to encrypt the password from [here](https://github.com/Appdynamics/maven-repo/blob/master/releases/com/appdynamics/appd-exts-commons/1.1.2/appd-exts-commons-1.1.2.jar).
2. Run command:

   	~~~   
   	java -cp appd-exts-commons-1.1.2.jar com.appdynamics.extensions.crypto.Encryptor EncryptionKey CredentialToEncrypt
   	
   	For example: 
   	java -cp "appd-exts-commons-1.1.2.jar" com.appdynamics.extensions.crypto.Encryptor test password
   
   	~~~
   	
3. Set the resulting encrypted password in passwordEncrypted field and the encryption key used in encryptionKey field in config.yaml file

### Below is how the AppDynamics event's parameters are associated with ServiceNow parameters:

<table>
	<tr>
	<td><strong>AppDynamics Parameters</strong></td>
	<td><strong>ServiceNow Parameters</td>
	<td><strong>Comments</td>
	</tr>

	<tr>
	<td> </td>
	<td>assigned_to</td>
	<td>This is the field used to assign an incident to an individual/department. The email address or full name of the designated user can be written here. This should be entered into the params.sh file as an email address that is already configured in the ServiceNow environment.</td>
	</tr>

	<tr>
	<td>
	<table>

	<tr>
	<td>
	APP_NAME</td>
	</tr>

	<tr>
	<td>PVN_ALERT_TIME</td>
	</tr>

	<tr>
	<td>SEVERITY</td>
	</tr>

	<tr>
	<td>POLICY_NAME</td>
	</tr>

	<tr>
	<td>AFFECTED_ENTITY_TYPE</td>
	</tr>

	<tr>
	<td>AFFECTED_ENTITY_NAME</td>
	</tr>

	<tr>
	<td>EVALUATION_TYPE</td>
	</tr>

	<tr>
	<td>EVALUATION_ENTITY_NAME</td>
	</tr>

	<tr>
	<td>SCOPE_TYPE_x</td>
	</tr>

	<tr>
	<td>SCOPE_NAME_x</td>
	</tr>

	<tr>
	<td>CONDITION_NAME_x</td>
	</tr>

	<tr>
	<td>THRESHOLD_VALUE_x</td>
	</tr>

	<tr>
	<td>
	 OPERATOR_x</td>
	</tr>

	<tr>
	<td>BASELINE_NAME_x</td>
	</tr>

	<tr>
	<td>USE_DEFAULT_BASELINE_x</td>
	</tr>

	<tr>
	<td>OBSERVED_VALUE_x</td>
	</tr>

	<tr>
	<td>INCIDENT_ID</td>
	</tr>

	<tr>
	<td>DEEP_LINK_URL</td>
	</tr>
	</table>
	</td>
	<td>description</td>
	<td>The format is as follows for the following Policy Violation Parameters:

	<table>

	<tr>
	<td><strong>Variable name: Variable value</strong></td>
	</tr>

	<tr>
	<td>Application Name: APP_NAME</td></td>
	</tr>

	<tr>
	<td>Policy Violation Alert Time: PVN_ALERT_TIME</td>
	</tr>

	<tr>
	<td>Severity: SEVERITY</td></td>
	</tr>

	<tr>
	<td>Name of Violated Policy: POLICY_NAME</td>
	</tr>

	<tr>
	<td>Affected Entity Type: AFFECTED_ENTITY_TYPE</td>
	</tr>

	<tr>
	<td>Name of Affected Entity: AFFECTED_ENTITY_NAME</td>
	</tr>

	<tr>
	<td>Evaluation Entity #x</td>
	</tr>

	<tr>
	<td>Evaluation Entity: EVALUATION_TYPE</td>
	</tr>

	<tr>
	<td>Evaluation Entity Name: EVALUATION_ENTITY_NAME</td>
	</tr>

	<tr>
	<td>Triggered Condition #x</td>
	</tr>

	<tr>
	<td>Scope Type: SCOPE_TYPE_x</td>
	</tr>

	<tr>
	<td>
	Scope Name: SCOPE_NAME_x</td>
	</tr>

	<tr>
	<td>CONDITION_NAME_x</td>
	</tr>

	<tr>
	<td>OPERATOR_x</td>
	</tr>

	<tr>
	<td>THRESHOLD_VALUE_x (this is for ABSOLUTE conditions)</td>
	</tr>

	<tr>
	<td>Violation Value: OBSERVED_VALUE_x</td>
	</tr>

	<tr>
	<td>Incident URL: DEEP_LINK_URL + INCIDENT_ID</td>
	</tr>

	</table>
	</td>
	</tr>

	<tr>
	<td>SEVERITY</td>
	<td>impact</td>
	<td>Represents the impact of the new Problem. Use the SEVERITY parameter where: ERROR = 1,  WARN = 2, and INFO = 3</td>
	</tr>

	<tr>
	<td></td>
	<td>knowledge</td>
	<td>This is either "true" or "false" but for policy violations, leave this as "false".</td>
	</tr>

	<tr>
	<td></td>
	<td>known_error</td>
	<td> This is either "true" or "false" but for policy violations, leave this as "true".</td>
	</tr>

	<tr>
	<td>PRIORITY</td>
	<td>priority</td>
	<td>This is a value from 1 to 5 where: 1 = Critical, 2 =  High, 3 = Moderate, 4 = Low, 5 = Planning. The PRIORITY parameter will fill this out directly.</td>
	</tr>

</table>

### Look for the newest created Incident in ServiceNow

When incident is created through AppDynamics it should look similar to the following screenshots.
The following is an overview shot of ServiceNow:

![](https://raw.githubusercontent.com/Appdynamics/servicenow-api-alerting-extension/master/overview.png)

The following shows the specifics of the ticket description:

![](https://raw.githubusercontent.com/Appdynamics/servicenow-api-alerting-extension/master/description.png)

**Note**: Notice that the "assigned to" field has "ITIL User" current in place here. This is taken from config.yaml.


###Limitation

Comments are only shown in REST API supported versions (Eureka, Fuji). In older versions (Calgary, Dublin) comments are not visible.

From version 1.4, we have added support to display comments in the Calagry and Dublin version too. To achieve this, instead of REST API we are using SOAP API to create incidents.

##Contributing

Always feel free to fork and contribute any changes directly via [GitHub](https://github.com/Appdynamics/servicenow-api-alerting-extension).

##Community

Find out more in the [AppSphere](https://www.appdynamics.com/community/exchange/extension/appdynamics-servicenow-rest-api-alerting-extension/) community.

##Support

For any questions or feature request, please contact [AppDynamics Center of Excellence](mailto:help@appdynamics.com).

