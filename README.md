**This is Document describes below topics:**

1. How to install and configure backstage locally
1. How to create a backstage component
1. How to integrate GitHub with backstage
1. How to trigger a CICD from backstage using GitHub actions
1. Issues and Solutions used while developing Above solution
1. How to replace same solution with AWS


1. **How to install and configure backstage locally:**

**Step 1. Prerequisite:** 

You should have curl,wget,Node.js (preferably stable version of nodejs version 18),yarn,docker,git installed on your local machine.

`       `# sudo apt update

`       `# sudo apt install make

`       `# sudo apt install build-essential

`       `# sudo apt install npm

`       `# sudo apt install yarn

Refer link - <https://backstage.io/docs/getting-started/>

**Step 2. Configure PostgreSQL as a database:**

\# sudo apt-get install PostgreSQL

\# sudo -u postgres psql

On postgres prompt enter below command which will create a password for a user postgres:

\# postgres=# ALTER USER postgres PASSWORD 'secret';

Use below command to configure PostgreSQL client from backstage root directory:

\# yarn --cwd packages/backend add pg

Add below configuration parameters inside app-config.yaml :

![](Aspose.Words.ac2800d3-10df-4f2f-b967-f3a87f41319b.001.png)

Here you can either export these variables from shell or command line or can have more secure way to configure and send these variable values to app-config.yaml.

**Step 3. Create your Backstage App:**

Use below command to create backstage app:

\# npx @backstage/create-app@latest

This will ask you to enter a name of the app where you can mention app name as per your wish for e.g. my-backstage-app

Start both Frontend and backend process using below command:

\# cd my-backstage-app

\# yarn dev

1. **How to create a backstage component**

`       `You can create backstage component using several ways such as using template which will create 

`       `A component and then you can add stages like repo creation, triggering a cicd etc.

`       `For better and quick understanding, we will go ahead with ‘Register Existing Application’ option.

`      `**Step 1. From UI Register existing application:**

`      `Click on ‘Register Existing Application’ tab:

![](Aspose.Words.ac2800d3-10df-4f2f-b967-f3a87f41319b.002.png)

`       `Enter the correct git url where your catalog-info.yml resides:

![](Aspose.Words.ac2800d3-10df-4f2f-b967-f3a87f41319b.003.png)
**
` `The wizard analyzes the file, previews the entities, and adds them to the Backstage App catalog.

` `You will get component looking like below :

![](Aspose.Words.ac2800d3-10df-4f2f-b967-f3a87f41319b.004.png)

1. **How to integrate GitHub with backstage:**

**Step 1. Generate a PAT:**

For integrating GitHub with backstage you need to generate a PAT , follow below link to do that:

<https://docs.github.com/en/authentication/keeping-your-account-and-data-secure/managing-your-personal-access-tokens#creating-a-personal-access-token-classic>

Once the token is generated save it safely somewhere and use the same token inside app-config.yml under section integration:

![](Aspose.Words.ac2800d3-10df-4f2f-b967-f3a87f41319b.005.png)

**Step 2. Create Oauth app and configure in backstage:**

Login to your GitHub account go to ‘Developer Settings’ and add respective values for Application name , Homepage URL, Authorization call back URL and click on register application , below is one sample :

![](Aspose.Words.ac2800d3-10df-4f2f-b967-f3a87f41319b.006.png)

Once the Application is registers you will get client id and clientsecret (this needs to be copied and kept somewhere safe, as it won’t be shown again):

![](Aspose.Words.ac2800d3-10df-4f2f-b967-f3a87f41319b.007.png)

Configure the ClientID and clientsecret under auth section of app-config.yml , below is sample:

![](Aspose.Words.ac2800d3-10df-4f2f-b967-f3a87f41319b.008.png)
**


1. **How to trigger a CICD from backstage using GitHub actions:**

**Step 1: Annotate your component with a correct GitHub Actions repository and owner:**

The annotation key is github.com/project-slug.

Example:

![](Aspose.Words.ac2800d3-10df-4f2f-b967-f3a87f41319b.009.png)

**Step 2: Install the plugin dependency in your Backstage app package:**

\# From your Backstage root directory

\# yarn --cwd packages/app add @backstage/plugin-github-actions

Add to the app EntityPage component:

// In packages/app/src/components/catalog/EntityPage.tsx

import {

`  `EntityGithubActionsContent,

`  `isGithubActionsAvailable,

} from '@backstage/plugin-github-actions';

// You can add the tab to any number of pages, the service page is shown as an

// example here

const serviceEntityPage = (

`  `<EntityLayout>

`    `{/\* other tabs... \*/}

`    `<EntityLayout.Route path="/github-actions" title="GitHub Actions">

`      `<EntityGithubActionsContent />

`    `</EntityLayout.Route>

Then start the app or # yarn dev

**Step 3: Create a GitHub workflows:**

Create a yml under a folder structure ‘.github/workflows’ in your GitHub repo , below is sample :

![](Aspose.Words.ac2800d3-10df-4f2f-b967-f3a87f41319b.010.png)

This yml will make sure that any push to main branch in this repository will initiate a GitHub action flow and it will first login to ghcr and create a docker image using Dockerfile in this repo.

Then we will tag and push the image to ghcr. Nest stage will pull the image and run the docker image in an GitLab Runner.

Here we have configured self-hosted GitHub runner so that we can see the output on localhost on mentioned port.

Link to GitHub repo - <https://github.com/Darknight123/backstage-repo>


1. **Issues and Solutions used while developing Above solution:**

1. **Issue :** While starting backstage frontend and backend post all configurations I came across below error:

   /home/orj/.npm/\_npx/54e75d7b4fe5bb68/node\_modules/commander/lib/command.js:380

   `    `enableOrNameAndArgs = enableOrNameAndArgs ?? 'help [command]';

   `                                               `^

   SyntaxError: Unexpected token '?'

   `    `at wrapSafe (internal/modules/cjs/loader.js:915:16)

   `    `at Module.\_compile (internal/modules/cjs/loader.js:963:27)

   `    `at Object.Module.\_extensions..js (internal/modules/cjs/loader.js:1027:10)

   `    `at Module.load (internal/modules/cjs/loader.js:863:32)

   `    `at Function.Module.\_load (internal/modules/cjs/loader.js:708:14)

   `    `at Module.require (internal/modules/cjs/loader.js:887:19)

   `    `at require (internal/modules/cjs/helpers.js:74:18)

   `    `at Object.<anonymous> (/home/orj/.npm/\_npx/54e75d7b4fe5bb68/node\_modules/commander/index.js:2:21)

   `    `at Module.\_compile (internal/modules/cjs/loader.js:999:30)

   `    `at Object.Module.\_extensions..js (internal/modules/cjs/loader.js:1027:10)

   **Solution**:

   This was resolved by installing latest stable version of nodejs 18 since as part of my ubuntu repo nodejs version 12 was available which was not supported for backstage installation.

   # curl -fsSL https://deb.nodesource.com/setup\_18.x | sudo -E bash -

   # sudo apt install nodejs -y

1. **Issue**: Post adding plugin-github-actions package plugin in backstage using command:

   # yarn --cwd packages/app add @backstage/plugin-github-actions

   Error received post starting # yarn dev

   ERROR in ./src/components/catalog/EntityPage.tsx 8:0-104

   Module not found: Error: Can't resolve '@backstage/plugin-github-actions' in '/home/orj/myapp/packages/app/src/components/catalog'

   **Solution** : I modified the command and added the plugin under backend as below :

   # yarn --cwd packages/backend add @backstage/plugin-github-actions

   And then restarting backstage using # yarn dev

1. **Issue:** While configuring local GitHub runner on my local machine post running ./config.sh I received error as below :

   ![](Aspose.Words.ac2800d3-10df-4f2f-b967-f3a87f41319b.011.png)

   **Solution:** I have applied below steps to stop and resync and then start the ntp service from localhost:

   # sudo service ntp stop

   # sudo ntpd -gq

   # sudo service ntp start





1. **How to replace same solution with AWS:**
1. In order to deploy the existing application to AWS in place of localhost or local server. We will need ECR to store the Docker image and then ECS to deploy the image with (ECS task definition).
1. For this to work we will also need to pass number of variables from GitHub action workflow such as below ones:

   aws-access-key-id

   aws-secret-access-key

   aws-region

   ecs\_service\_name

   ecs\_cluster\_name

   ecs\_task\_definition\_name

   container\_name

   I can provide a demo of this solution.

