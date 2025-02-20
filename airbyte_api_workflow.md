# Complete Airbyte API Workflow: A Step-by-Step Guide

## Table of Contents
1. Introduction
2. Prerequisites
3. Step-by-Step Instructions
   - Creating an Application
   - Obtain an Access Token
   - Create a Source
   - Create a Destination
   - Create a Connection
4. Conclusion

## Introduction
In this guide, we’ll walk through a comprehensive step-by-step process to complete the Airbyte API workflow. The Airbyte API empowers users to manage and automate data transfers between a wide array of sources and destinations. By offering a robust set of endpoints, it simplifies the orchestration of complex data workflows, making data integration seamless and efficient.

## Prerequisites
Before you begin, ensure you have the following:

- An AWS account
- AWS CLI installed and configured
- Basic knowledge of AWS EC2 and Docker
- Docker installed on your EC2 Instance
- Airbyte installed on your EC2 Instance

### Preparation Steps:
1. **Launch the Airbyte Container:** Start the Airbyte container on your EC2 instance.
2. **Access the Airbyte UI:** Navigate to the Airbyte UI by entering `http://<instance_IP_address>:<port_number>` in your web browser.
3. **Retrieve the Workspace ID:** Obtain the Workspace ID from the URL in your developer portal. The Workspace ID is found between `workspace/` and the following `/`.

## Step-by-Step Instructions

### 1. Creating an Application
1. **Access the Airbyte UI:** Log into the Airbyte UI.
2. **Navigate to Settings:** Go to the 'Settings' section and select 'Create Application.'
3. **Create the Application:** Enter a name for your application and complete the creation process.
4. **Secure Your Credentials:** Copy and securely store the Client ID and Client Secret, as these are essential for generating tokens.

### 2. Obtain an Access Token
1. **Visit Airbyte Documentation:** Refer to the Airbyte Reference - Getting Started.
2. **Generate Token:**
   - Navigate to the "Application" tab and click on "Get an Access Token."
   - In the Body Params section, enter the `client_id` and `client_secret` obtained earlier, and set the `grant_type` to `client_credentials.`
   - Choose your preferred programming language (e.g., Python) to generate the code.
3. **Run the Code:** Copy the generated code into a new file in Visual Studio Code (VSCode) and execute it using:
   ```bash
   docker-compose up
   ```
   ```python
   import requests
   client_id = 'your_client_id'
   client_secret = 'your_client_secret'

   response = requests.post(
       'https://api.airbyte.com/oauth/token',
       data={
           'grant_type': 'client_credentials',
           'client_id': client_id,
           'client_secret': client_secret,
       }
   )

   token = response.json()['access_token']
   print(token)
   ```
4. **Store the Token:** The output will include an access token in JSON format. Save this token, noting that it is valid for only 3 minutes.

### 3. Create a Source
1. **Access Source Creation:**
   - Navigate to the "Source" tab within the Airbyte UI.
   - Click on "Create a Source."
2. **Configure the Source:**
   - Enter a name for your source and paste your `workspace_id`.
   - Select the source configuration and provide credentials (e.g., for Google Sheets).
3. **Authorize and Run:**
   - Paste the access token in the authorization tab.
   - Copy and execute the following code:
   ```python
   import requests
   token = 'your_access_token'
   workspace_id = 'your_workspace_id'

   response = requests.post(
       'https://api.airbyte.com/sources/create',
       headers={'Authorization': f'Bearer {token}'},
       json={
           'workspaceId': workspace_id,
           'sourceDefinitionId': 'your_source_definition_id',
           'connectionConfiguration': {
               'credentials': 'your_credentials'
           },
           'name': 'your_source_name'
       }
   )

   source_id = response.json()['sourceId']
   print(source_id)
   ```
4. **Save the Source ID:** The output will include a `sourceId`. Copy and securely store this ID for future use.

### 4. Create a Destination
1. **Access Destination Creation:**
   - Navigate to the "Destination" tab in the Airbyte UI.
   - Click on "Create a Destination."
2. **Configure the Destination:**
   - Enter a name for your destination and paste your `workspace_id`.
   - Select your destination configuration and provide the necessary credentials (e.g., PostgreSQL).
3. **Authorize and Run:**
   - Paste the access token in the authorization tab.
   - Copy and execute the following code:
   ```python
   import requests
   token = 'your_access_token'
   workspace_id = 'your_workspace_id'

   response = requests.post(
       'https://api.airbyte.com/destinations/create',
       headers={'Authorization': f'Bearer {token}'},
       json={
           'workspaceId': workspace_id,
           'destinationDefinitionId': 'your_destination_definition_id',
           'connectionConfiguration': {
               'credentials': 'your_credentials'
           },
           'name': 'your_destination_name'
       }
   )

   destination_id = response.json()['destinationId']
   print(destination_id)
   ```
4. **Save the Destination ID:** The output will include a `destinationId`. Copy and securely store this ID.

### 5. Create a Connection
1. **Access Connection Creation:**
   - Navigate to the "Connection" tab and click on "Create Connection."
2. **Configure the Connection:**
   - Provide a name for the connection, along with the `sourceId` and `destinationId` obtained in previous steps.
3. **Generate and Run Code:**
   - Copy and execute the following code:
   ```python
   import requests
   token = 'your_access_token'
   source_id = 'your_source_id'
   destination_id = 'your_destination_id'

   response = requests.post(
       'https://api.airbyte.com/connections/create',
       headers={'Authorization': f'Bearer {token}'},
       json={
           'sourceId': source_id,
           'destinationId': destination_id,
           'name': 'your_connection_name',
           'status': 'active'
       }
   )

   connection_id = response.json()['connectionId']
   print(connection_id)
   ```

## Conclusion
Congratulations! You’ve successfully deployed the Airbyte API on an EC2 instance using Docker. With the setup complete, you can now use the Airbyte dashboard to manage your data integrations effortlessly. For more advanced configurations and integrations, refer to the Airbyte documentation.
