1. Remove existing proxy
```bash
$gcloud config unset proxy/type
$gloud config unset proxy/address
$gcloud config unset proxy/port
```
To add proxy
```bash
$ gcloud config set proxy/type http
$ gcloud config set proxy/address <proxy URL>
$ gcloud config set proxy/port 12345
```
2. Authenticate to GCP
To authenticate to GCP, execute the following command. 
It will open a browswer and allow you to confirm access for gloudsdk.
```bash
$gcloud auth login
```
3. SDK Configuration
Configuring gcloudsdk for Project, Zone, and Region
To set your project, you will want to see which projects are available to you
and set the appropriate project
```bash
$gcloud projects list
$gcloud config set project <project id>
```
Check the avaiable zones and set your preference
```bash
$gcloud compute zones list
$gcloud config set compute/zone <zone>
```
Update gcloudsdk
```bash
$<path/to/bin>/gcloud components update
```
4. gcloudsdk Configurations
View the summary list of configuration settings
```bash
$ gcloud config configurations list
NAME IS_ACTIVE ACCOUNT PROJECT DEFAULT_ZONE DEFAULT_REGION
default True <your email address> <your project> <your zone> <your region>
```
View the details of a specific configuration, such as "default"
```bash
$ gcloud config configurations describe default
is_active: true
name: default
properties:
    compute:
        region: <your region>
        zone: <your zone>
    core:
        account: <your email address>
        project: <your project>
```
Once complete, you can find your default configuration at:
- MacOS: /Users/<your id>/.config/gcloud/configurations/config_default
- Windows: C:\Users\<your id>\AppData\Roaming\gcloud\configurations\config_default
5. Installing Additional gcloud Components
Only a few components are installed in gcloud by default. 
You'll want to review the available components and install any additional
that you require.
To list the available componets
```bash
$ gcloud components list
```
To install an additional component
```bash
$ gcloud components install COMPONENT_ID
```
To remove an additional componet
```bash
$ gcloud components remove COMPONENT_ID
```
6. SSH to a Compute Instance
```bash
$ gcloud compute ssh --zone <zone> <instance name>
$ gcloud compute --project <project name> ssh --zone <zone> <instance name>
$ gcloud compute --project <project name> ssh --zone <zone> <instannce name> --internal-ip
```
7. SCP a file to a Compute Instance
To transfer a file to your compute instance
```bash
$ gcloud compute scp --zone <zone> </path/to/file> <instance name>:
```
Don't forget the colon at the end of the instance name