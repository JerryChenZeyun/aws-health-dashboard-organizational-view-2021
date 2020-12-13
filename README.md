# Building Organization Service Health Check Solution with Lambda and QuickSight


This lab is intended to showcase the Health API Organization View feature. Organization View is intended to aggregate Personal Health Dashboard/Health events at the PAYER account level within an organization. Thus it requires an Organization hierarchy, including linked accounts. Furthermore, for the data required to be aggregated at the PAYER account level, it needs to be enabled prior to the Personal Health Dashboard notification being created.

At minimum, user can use their own AWS account with Business or Enterprise support and with single account under the AWS organization to run this lab. The lab content and process can be best referenced for user account with multiple linked accounts under the AWS organization.

<br />


# Introduction
This lab aims to show users how easy it is to call AWS health API at organization level through Lambda. The python code initially runs locally in the Lambda environment, where we will upload the health status data to S3 bucket, and then visualise the data using QuickSight. Optionally, user can consider to integrate email SNS to get notification upon the conditions setup by the operation team.

![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-dashboard-organizational-view-2021/blob/main/media/Screen%20Shot%202020-12-13%20at%204.16.09%20pm.png)
<br /><br />

# Setup

Step 1. Go to AWS Console, select CloudFormation service. Or simply click this link.

Step 2. Click “Create stack” and select “With new resources (standard)” to initiate the stack configuration process.

Step 3. Copy the following URL into the Amazon S3 URL, then click “Next”.

Step 4. Give a name to the stack and the S3 bucket used to host the health organizations data, then click “Next”.

Step 5. Accept the default settings in “Configure stack options” page, then click “Next”.

Step 6. In the "Review" page, check the 3 boxes at the bottom of the page to acknowledge that AWS CloudFormation might create relevant IAM resources and capability, then click “Create stack” to launch the stack provisioning process. It would take around 2 mins for the stack to be provisioned.

Once the stack has been created, we can use QuickSight to create the dashboard to visualise the health status csv data.

Step 7. Configure the data refresh frequency – Go to “Datasets” and click on the dataset that’s been just created. Click the “Schedule refresh” button and then click “Create” in the pop-up window.

<br />

# Sign up for QuickSight and create the new dataset 

After the python script has been executed, the health status data would be stored in S3 bucket as csv file. We can utilise various tooling to visualise the dataset. In this Lab, we are going to use QuickSight.

1. Go to the following QuickSight link to generate the view of the dataset
https://us-east-1.quicksight.aws.amazon.com/sn/start

   As a first time QuickSight user, you might need to sign up for QuickSight service. Please refer to [Setting up QuickSight](#setting-up-quicksight) in appendix section for the sign up process.
   
2. Enable QuickSight to be able to access the data file stored in S3 bucket you created

a) Click the user icon on the top right corner of QuickSight page, then select "Manage QuickSight".
![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/Screen%20Shot%202020-05-05%20at%209.33.32%20pm.png)

b) Select "Security & permissions" on the left, then click the "Add or remove" button under "QuickSight access to AWS services"
![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/WX20200505-213724%402x.png)

c) Click the "Details" link under "Amazon S3", then click the "Select S3 buckets" button
![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/WX20200505-214220.png)

d) Check the box left to the bucket that you created for this lab in previous step, then click "Finish". 
![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/WX20200505-214553.png)

e) Click the "Update" button. Once this done, QuickSight will have access right to this bucket to visualise the data. 
![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/WX20200505-220505.png)


3. Go back to the QuickSight front page (https://us-east-1.quicksight.aws.amazon.com/sn/start) and choose "New analysis" at the top left corner of the QuickSight page

![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/Screen%20Shot%202020-02-27%20at%209.46.46%20pm.png)

4. Choose "New data set" at the top left corner of the page

![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/Screen%20Shot%202020-02-27%20at%209.52.27%20pm.png)

5. Up till now, there's a "manifests3.json" file that has been stored in the S3 bucket you created for this Lab. Please jot down the URL for this json file. You can use this link (https://s3.console.aws.amazon.com/s3/object/) to go to your S3 and click on your bucket, and then click on "manifests3.json" file to copy its URL. The screenshot is for reference.

![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/Screen%20Shot%202020-03-09%20at%209.20.09%20pm.png)

6. Select S3, then fill in "Data source name" (e.g. event_data_file). Then choose the "URL" radio button. Then paste the URL you copied from last step in the URL blank.

   Then hit "Connect", and then click "Visualize" to proceed. 

![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/Screen%20Shot%202020-03-09%20at%209.22.56%20pm.png)

7. Up till this step, you should be able to upload the file to QuickSight. If you meet with SPICE related error message, please take the following actions:<br />

a. Go to this link to increase the SPICE capacity in US east 1 region:https://us-east-1.quicksight.aws.amazon.com/sn/admin?#capacity.

![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/WX20200508-221500.png)

b. Fill in "1" GB in the black, the click "Purchase SPICE capacity", then you can retry from step 3 to upload the data file from S3 to QuickSight.

![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/Screen%20Shot%202020-05-08%20at%2010.18.05%20pm.png)
/br

# Visualize the health event csv data through QuickSight 

With the new dataset and analysis created, you can now create the layout of the analysis. The following steps are to create the first sheet in the analysis.

Step 1. In the “Sheet 1” of the newly created analysis, from the “Fields list” select the “service”, and then select “Donut chart” in the Visual types. So you will get the “Count of Records by Service” visual item created.

Step 2. Select “Filter” on the left column bar then click “Create one…” for the “statusCode”. Once the filter’s been created, click the filter to only include the “open” value, then click “Apply”. This is to filter out the “closed” and “upcoming” events, so this tab will only show the active events.

Step 3. Click the “…” to the right of the visual and select “Duplicate visual”. In this way you can easily create multiple visuals in the QuickSight analysis. 

Step 4. Once you’ve duplicated the visual, you can click on this new visual and then select the “region” in the “Fields list”, so you can get the visual of “Count of Records by Region”. You can also change the Visual types of your choice. Here we use “Pie chart”. 

Step 5. You may repeat the Step 3 and 4 to create other Visuals in the Analysis. You can adjust the size and position of the Visual to fit into the layout of your Analysis. In our case we will create 2 more Visuals for “impactedAccount” and “impactedEntity” from the “Fields list”. You can click on the Visual chart and change the colour that you preferred.

Step 6. Duplicate another Visual and select “eventTypeCategory” from the “Fields list”. Change the Visual types to “Vertical bar chart”. Then adjust its size to fit the Analysis layout. 

Step 7. Duplicate one more Visual and select the Visual types as “AutoGraph”. Adjust its size to cover the Analysis bottom area. In the “Fields list”, we will select all the fields to make this visual as an event detail spreadsheet. 

Notice that the sequence of the field items selected will affect the column sequence of the spreadsheet. So for better presentation of the spreadsheet, recommend to choose the field items in this order (as some items will take up more room to present):
“Service, Region, Statuscode, Starttime, Endtime, Lastupdatedtime, Impactedaccount, Impactedentity, Eventtypecode, Eventtypecategory, Arn, and Eventdescription”

Step 8. Now we’ve completed 1 sheet of the dashboard. We can now select “Duplicate sheet” from the “Sheet 1” dropdown list to create 2 more sheets. 

Step 9. In the second sheet, click one of the visual. Go to “Filter” tab and select “statusCode” filter, then select “Exclude” under the Filter type and choose “Select all”. Click “Apply” to activate the filter.

Repeat the above configuration to all visuals in this sheet. This is to filter out the “open” and “closed” events, and only shows the “upcoming” events. 
 
It’s OK that there’s no data shown up for the “upcoming” events as normally events will fall into “open” and “closed” categories. 

Step 10. Similarly in the third sheet, click one of the visual. Go to “Filter” tab and select “statusCode” filter, then select “Include” under the Filter type and choose “closed”. Click “Apply” to activate the filter.

Repeat the above configuration to all visuals in this sheet. This is to filter out the “open” and “upcoming” events, and only shows the “closed” as historical events. 

Step 11. Now you can change the tab titles accordingly by double click the tab title to reflect the health event data categories. For example, we will name these 3 tabs as “Active Service Event”, “Upcoming Service Event” and “Historical Service Event”. 

Step 12. Now your analysis is ready to be published. Click the “Share” at the top right conner and select “Publish dashboard” to create the dashboard from the analysis. 

Up till now you’ve setup the fully functional AWS health dashboard for organizational view. Cloud platform 
& operation team can refer to those 3 tabs within the dashboard to gain quick visibility for the active, upcoming and historical health events that you’d need to pay attention to. You can bookmark this dashboard in your browser for future access.


Please proceed to next step to clean up the resource when it's not needed.
<br />
<br />
<br />

# Clean Up Step

1.	Go to the S3 Console to delete the files within the S3 bucket that you created.
2.	Go to CloudFormation dashboard in AWS Console or simply click this link, select your CloudFormation stack and click "Delete".
3.	Cancel your Amazon QuickSight Subscription following this instruction.


<br />
<br />
<br />



# Appendix
<br />

# Create new S3 bucket

This section give you more detailed guidance on how to create the S3 bucket to host data fed back from Organization Health API Call. Please follow these steps to complete the bucket creation:

1. Go to the following link to access AWS S3 service, then click "Create bucket".
    `https://s3.console.aws.amazon.com/s3/home?region=us-east-1`
    
![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/Screen%20Shot%202020-05-08%20at%2010.07.45%20pm.png)

2. Give the new bucket a name, which cannot duplicate with other S3 bucket that have been existed. In this example, the new bucket is named as "my-health-api-lab-202003082100", and accept other default settings, Then click "Create bucket" to finish the creation process.

![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/Screen%20Shot%202020-05-08%20at%2010.08.25%20pm.png)

<br />

# Setting up QuickSight

For those AWS accounts that use QuickSight for the first time, users need to explicitely sign up for QuickSight. Please follow the following steps:

1. Go to follow link to access AWS QuickSight service, then click "Sign up for QuickSight".

   `https://us-east-1.quicksight.aws.amazon.com/sn/start`

![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/Screen%20Shot%202020-03-08%20at%204.48.39%20pm.png)

2. In the QuickSight account selection page, please select "Standard" edition for this lab as shown below. Then click "Continue" at the bottom right corner.

![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/Screen%20Shot%202020-03-08%20at%204.56.48%20pm.png)

3. Give an Account Name to QuickSight service. 
   <p>a. You might use "dev-lab" as shown in screenshot. <p/>
   <p>b. Fill in an email address for the registration. For simplicity, you might use a fake email address "abc@gmail.com" to proceed.<p/>
   <p>c. leave all other options as default settings, and click "Finish" to finalise the QuickSight sign up process.<p/>

![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/Screen%20Shot%202020-03-08%20at%209.49.24%20pm.png)

# Canceling Your Amazon QuickSight Subscription

Here's the public guidance link: https://docs.aws.amazon.com/quicksight/latest/user/closing-account.html

1. Goes to this URL https://us-east-1.quicksight.aws.amazon.com/sn/admin?#permissions
2. Choose Unsubscribe.
![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/Screen%20Shot%202020-05-08%20at%2010.00.57%20pm.png)
![Image of Yaktocat](https://github.com/JerryChenZeyun/aws-health-api-organization-view/blob/master/media/Screen%20Shot%202020-05-08%20at%2010.01.05%20pm.png)
