# ec2-ai-agent-enhancement

### <ins>System Overview</ins><br/>
The AWS EC2 Monitoring and Remediation System is a centralized, semi-automated ServiceNow incident response system that helps DevOps engineer teams quickly remediate failing AWS EC2 instances. It automatically detects when EC2 instances fail, creates incidents for faulty EC2 instances, provides AI-powered knowledge retrieval for remediation guidance, sends instance failure alert and remediation guidance through Slack notifications, and allows engineers to trigger AWS remediation directly from ServiceNow during critical incidents. All triggered remediation attempts, whether successful or not, are logged for auditing purposes (and/or further investigation.) By providing instant detection, notification, documentation, and remediation, the EC2 Monitoring and Remediation System greatly reduces incident response time and contributes to an improved customer experience. <br/>

This **EC2 System AI Agent Enhancement** allows for an alternative method of instance remediation. As an additional response layer, it further minimizes potential user error and response time. By integrating a conversational AI agent layer to handle remediation requests, the user can apply RemediationHelper logic through natural language interaction. The AI Agent Enhancement has been tested to accept and return responses for specific requests (eg. *"Help me with Instance ID: [i-88e8800e008]"*, or *"Help me with incident INC0010020"*) as well as general inquiries (eg. *"Help me with incidents"* or *"Find issues"*). Similar to the Manual Remediation method, the AI Agent Enhancement script provides audit logs for all remediation attempts.

### <ins>Implementation Steps<ins/><br/>
Using **AI Agent Studio**, we create an agent by navigating to the "New" button. <br/>
<img width="2548" height="1317" alt="studio" src="https://github.com/user-attachments/assets/c791f85d-8235-438e-92fa-a4799c2cce48" />
<img width="2556" height="1312" alt="new agent" src="https://github.com/user-attachments/assets/ecea51a5-ae15-4f36-9ce7-92abb8e0147d" /><br/>
Here, we will be prompted to describe our use case with a name, a description of the agent's role, and an instruction prompt for the agent to follow. In our case, the <ins>**EC2 Remediation Assistant**</ins>: <br/>
> Helps DevOps engineers restart EC2 instances from incident tickets by reading incident descriptions and executing remediation scripts with human approval.<br/>

Instructions for the agent are as follows:<br/>
```
First, discover EC2 *Instance IDs* and *Instance Names* on the EC2 instance table (x_snc_ec2_monito_0_ec2_instance).

Second, search the incident table (u_incident) and return record numbers containing the EC2 *Instance ID* or the *Instance Name* in the record description.
ALWAYS return identified Incident Number and ALWAYS return Instance ID and ALWAYS return Instance Name.

Ask to proceed with the following:
*Execute remediation script "call_ec2_remediation.js"

Return results of the executed script after activation.
```
<img width="2546" height="1312" alt="agent use case" src="https://github.com/user-attachments/assets/fe3a974a-0ac7-45e3-85b4-e4efbd2c275e" /><br/>
Following our agent's use case, we will arm it with the appropriate agent tools to allow it to retrieve incident record details, and remediate the appropriate instances following those record details ("EC2 Instance Incident Reporter" and "call_ec2_remediation.js" respectively.)<br/>
<img width="2547" height="1316" alt="ai tools" src="https://github.com/user-attachments/assets/dd3edd90-4374-4d90-9a4b-2c10b57154b1" /><br/>
The first tool, the **EC2 Instance Incident Reporter** is an Edit Record Operation tool that looks up records on the incident table (u_incident) and retrieves incident record numbers containing EC2 <ins>Instance names</ins> and <ins>Instance IDs</ins>. It also retrieves these Instance Name and Instance ID values from the record short descriptions.<br/>
<img width="780" height="582" alt="Reporter1" src="https://github.com/user-attachments/assets/4bad0f68-8a5b-4604-a326-0633bd6f3197" />
<img width="790" height="587" alt="reporter2" src="https://github.com/user-attachments/assets/ea600ffd-1a00-4741-81f1-edcbb3e54b9f" />
<img width="790" height="586" alt="Reporter3" src="https://github.com/user-attachments/assets/0e5e657d-2c3d-4abb-b0a9-c72605de7f40" /><br/>
The second tool, **call_ec2_remediation.js**, is a script tool used to remediate failed EC2 instances discovered in the incident table. It remediates EC2 instances by working with existing AWS Integration Server connections and establishing an API call. Incident record short descriptions will provide instance IDs. Because we want to confirm approval with the AI agent before every remediation attempt, we will toggle it's execution mode to supervised.<br/>
<img width="787" height="588" alt="caller1" src="https://github.com/user-attachments/assets/3435d17e-e297-4b27-a10b-e16ba7998d36" />
<img width="787" height="592" alt="caller2" src="https://github.com/user-attachments/assets/2bc2b718-b6a9-4152-aa17-bc020201cc94" /><br/>
After tool assignment, we define the agent's availability by setting it to active. In order to help users remediate incident cases conversationally, we allow the EC2 Remediation Agent to be displayed in the Now Assist panel. We do not, however, grant access to everyone; We only grant access to users whose roles are immediately relevant to Networking DevOps.<br/>
(In our case, users and roles have not been defined yet. Our test environment uses a basic list of roles with access to EC2 Instance and Remediation Log tables.)<br/>
<img width="2552" height="1315" alt="availability" src="https://github.com/user-attachments/assets/dcff9a99-3370-45f7-b5a5-872e9d4fc7ec" /><br/>
Final steps will involve testing of various prompts and inputs, to confirm the agent can process natural language prompts, as well as required confirmation of human approval. Examples:<br/>
<img width="2542" height="1306" alt="test " src="https://github.com/user-attachments/assets/ecbe7a94-b8b5-4dbb-b0da-4c27c7d97613" />
<img width="2553" height="1317" alt="AI Test" src="https://github.com/user-attachments/assets/2298e8ec-8b49-4930-93a8-11a07aab8a7c" />
### <ins>Architecture Diagram</ins><br/>
<img width="1691" height="990" alt="Diagram" src="https://github.com/user-attachments/assets/9dc5bd43-30c5-4343-988b-2e07a6739429" /><br/>

### <ins>Optimization</ins><br/>
As an additional conversational AI agent layer to the existing EC2 Monitoring and Remediation System in place, the EC2 System AI Agent Enhancement allows the user additional flexibility in remediating EC2 instance issues. Along with this flexibility, margins for error or misinterpretation, as well as incident response times are reduced. As a DevOps user, one has the choice to remediate downed systems by manually triggering AWS integration server API calls as instructed by existing documentation and UI actions on individual instance records, or one can start a conversational dialogue with the EC2 Remediation Assistant in the method and manner of their choosing, to automatically send an AWS integration Server API call. Comparatively, both methods reach the same conclusion, and both methods create logs of each and every remediation attempt in the Remediation Log table. The difference, however, is evident in the use case environment.<br/>
<br/>
While remediation of a single downed instance is easily achievable through both manual and agentic methods, the AI Agent Remediation Enhancement shines most when used in an environment where multiple instances run concurrently. In such cases, a DevOps engineer might find that parsing through incident records and finding affected instances to manually remediate can impact response times, cause delays, and breach possible SLA's. The DevOps engineer might instead prefer to use the AI Agent Remediation Enhancement to "automatically" arrive at any impacted instances and/or incident records and immediately implement a remediation.<br/>
<br/>
All things considered, this faster response time comes at the cost of being unable to manually update incident and instance record notes, such as incident resolution information, callers and assignment groups/users, or work notes. In the case that a repeated incident work note or other record must be documented for a repeatedly impacted instance (eg. instance health assessment), manual remediation would allow a more focused and in-depth view of incident and instance records.
<br/>
### <ins>DevOps Usage</ins><br/> 
In conjunction with the EC2 Instance Monitoring and Remediation System, the EC2 System AI Agent Enhancement has been prompted and programmed to work with NowAssist on the ServiceNow platform. Users can interact with Now Assist to remediate failed EC2 instances. With already existing workflows providing impacted EC2 instance information such as instance name and ID through Slack communication, as well as incident record creation, users can simply ask NowAssist to help them with relevant incident numbers, instance names, or instance IDs. NowAssist will walk the user through remediation steps and, with the user's approval, apply an AWS integration server API call to get the instance back up and running in no time!
