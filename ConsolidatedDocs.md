# Overview – what is Data Activator?

Microsoft Fabric and Data Activator are currently in PREVIEW. See the Supplemental Terms of Use for Microsoft Azure Previews for legal terms that apply to Azure features that are in beta, preview, or otherwise not yet released into general availability.
# What is Data Activator?
Data Activator is a no-code tool for automatically taking actions when patterns or conditions are detected in changing data. It monitors data in Power BI reports and Event Hub data streams, for when the data hits certain thresholds or matches other patterns. It then automatically takes appropriate action such as alerting users or kicking off Power Automate workflows.

Data Activator allows customers to build a digital nervous system that acts across all their data, at scale and in a timely manner. Business users can describe business conditions in a no-code experience to launch actions such as Email, Teams notifications, Power Automate flows and call into 3rd party action system. Business users can self-serve their needs and reduce their reliance on internal IT and/or developer teams, either of which is often costly and hinders agility. Customer organizations don’t need a developer team to manage and maintain custom in-house monitoring or alerting solutions.

Some common use cases are:
*	Run ads when same-store sales decline.
*	Alert store managers to move food from failing grocery store freezers before it spoils.
*	Retain customers who had a bad experience by tracking their journey through apps, websites etc.
*	Help logistics companies find lost shipments proactively by starting an investigation workflow when package status isn’t updated for a certain length of time.
*	Alert account teams when customers fall into arrears, with customized time or value limits per customer.
*	Track data pipeline quality, either re-running jobs or alerting when pipelines fail or anomalies are detected.

## Reflex items in Fabric
A reflex item holds all the information necessary to connect to data, monitor for conditions, and act. You’ll typically create a reflex item for each business process or area you’re monitoring.
To get started, you create a reflex item in your Fabric workspace. From the New menu in the workspace, choose the reflex item.
You’ll connect to a data source and choose the data stream you want to monitor (e.g. an IoT (Internet of Things) device feed, a sensor feed, or an application telemetry stream). You create the business objects that your stream represents, such as a Freezer, Vehicle, Application, or Order. Then, define properties on the objects such as temperature, speed, sales amount. Next, specify the conditions you want Data Activator to look for (e.g. temperature below zero degrees, or speed above 60mph). Finally, you tell Data Activator what action to take as a result such as sending a Teams or email message, or starting a Power Automate workflow.

## Concepts in Data Activator
The following concepts are used to build and trigger automated actions and responses in Data Activator.

### Events
Data Activator considers all data sources to be streams of events. An event is an observation about the state of an object, with some identifier for the object itself, a timestamp, and the values for fields you’re monitoring. Event streams will vary in frequency from many times per second in the case of IoT sensors down to more sporadic streams such as packages being scanned in and out of shipping locations. 
Data being observed from Power BI is also treated as an event stream. In this case, events are observations made of the data on a regular schedule that would typically match the refresh frequency of your Power BI dataset. This might only happen once a day, or even once a week – it’s just a very slowly changing event stream!

### Objects
The business objects that you want to monitor could be physical objects like Freezers, Vehicles, Packages, Users, etc. or less tangible concepts like Advertising Campaigns, Accounts, User Sessions. In your reflex item, you model the object by connecting one or more event streams, choosing a column from the object ID, and specifying the fields you want to make properties of the object.
The term ‘object instance’ means a specific Freezer/Vehicle/Package etc. whereas ‘object’ is typically used for the definition or class of object. We talk about the ‘population’ to refer to all the object instances.

### Properties
Properties are used to model:
*	the attributes of the object
* functions you apply to the value of the attributes
*	conditions you want to monitor and detect
*	actions you want Data Activator to take when conditions are met
Properties can use any one or more of these, and none are required. For example, you might select the temperature value from an event, and apply a function to smooth it over five minute windows. That ‘Smoothed temperature’ property could be reused in many other places around your project. You might build a second property that takes 'Smoothed Temperature’ and sends an email when it goes over 90 degrees Fahrenheit.


# Get Started from a Power BI report
You can use Data Activator to trigger notifications when conditions are met about data in a Power BI report. For example, given a report displaying daily sales per store, you could send a notification at the end of the day if daily sales for any store fall beneath a threshold. You can send notifications to yourself, or to others in your organization. This section explains how notifications can be created and triggered.
## Prerequisites
Before you begin, you will need a Power BI report that is published online to a Fabric workspace in a Premium capacity

NOTE: Data Activator currently supports only Power BI visuals that meet these criteria:
- The visual has both an X- and Y- axis. This includes column, bar, line, pie and area charts.
- The visual’s X-axis is not a time dimension.

## Step 1: Select “Trigger Action” on your Power BI visual.
To begin creating a trigger from a Power BI report:
1)	Open your Power BI report.
2)	Choose a visual on the report for Data Activator to monitor. Make sure it is a supported visual type, as described in the previous section.
3)	Click the ellipsis (“…”) at the top-right of the visual, and select “Trigger Action.”
The following image shows an example of how to trigger an action from a visual that displays today’s sales for each store in a retail chain:
 ![image](https://github.com/microsoft/dataActivator/blob/main/images/pbi_context_menu.png)

## Step 2: Create your Data Activator trigger.
Next, define your trigger conditions and to create your trigger. In the “Create Alert” dialog that appears, do the following:
1)	Fill out the “What to monitor” section. This tells Data Activator which measure to monitor, and how often to check its value. Data Activator will check the value of the measure separately for each value of the “For Each” dimension that you select.
2)	Fill out the “What to detect” section. This defines your trigger condition. Whenever this condition is met, Data Activator will send a notification.
3)	Fill out the “Where to save” section. This tells Power BI where to save your Data Activator trigger. You can choose an existing reflex item, or you can create a new reflex item.
4)	Choose “Continue.” This will create your Data Activator trigger and open it within your reflex item.

Continuing with the example from step 1, the following image shows how you would create a trigger that will fire daily if the sales for any store drop below $60,000:

![image](https://github.com/microsoft/dataActivator/blob/main/images/pbi_flyout.png)
 

## Step 3: Add an action to your trigger.
The final step is to define the notification that Data Activator should provide when your trigger fires.  To add an email or Teams notification to your trigger, follow the steps described in the “Adding an action to your trigger” section. 
 
# Get Started from Azure Event Hubs data
Data Activator can trigger notifications from events streaming into Azure Event Hubs. This is useful for real-time monitoring. For example, a store sending fridge temperatures to Azure Event hubs could use Data Activator to trigger a notification if a fridge becomes too warm. This section explains how to create a Data Activator trigger from data in an Azure Event Hubs instance.
## Prerequisites
Before you begin, you will need a reflex item, and an Azure Event Hubs Instance. 
NOTE: Your Azure Event Hubs instance must meet these criteria:
   1. Each event in the instance is in JSON dictionary format.
   2. At least one of the keys in the dictionary uniquely identifies an object.
Here is an example of an event that meets these criteria. In this example, the “DeviceID” key identifies the object:   {DeviceID: 4, Temperature: 23.5}

## Step 1: Get the connection string for your Azure Event Hubs instance.
Data Activator will need the connection string for your Azure Event Hubs instance. To get the connection string:
TIP: Azure Event Hubs has namespaces. Within each namespace are one or more instances. Data Activator connects to instances. Be sure to start this step from an instance, rather than a namespace.
1)	Within the Azure Portal, navigate to your Event Hubs instance.
2)	Select “Shared Access Policies” from the navigation menu in your instance.
3)	Select (or create) an access policy with at least “Listen” permission.
4)	Copy the access policy’s primary connection string to your clipboard.
The following image shows the Azure Event Hubs dialog that contains the connection string:
 ![image](https://github.com/microsoft/dataActivator/blob/main/images/eh_sas_policy.png)

## Step 2: Connect your reflex item to your Azure Event Hubs instance.
1)	Within your reflex item’s “Data” tab, select “Get Data” 
2)	Within the “Get data” dialog, paste the connection string you copied in step 1
3)	Give your event stream a name
4)	Optionally provide a consumer group, if you do not wish to use the Default group
5)	Click “Connect.”
   
 ![image](https://github.com/microsoft/dataActivator/blob/main/images/get_data_eh.png)

Within a few seconds, your event hubs events will appear in your newly created event stream.


# Working with Data Activator’s object model
## Creating objects
Once you have an event stream bringing data into Data Activator, the next step is to create an object starting from the object ID. The object ID is a field that uniquely identifies an instance of the object, for example and employee ID or device ID.

In the Data view, select Create object from the menu on the column in the data preview.

 ![image](https://github.com/microsoft/dataActivator/blob/main/images/table_create_object.png)

Then, enter a name for your object.

 ![image](https://github.com/microsoft/dataActivator/blob/main/images/name_object.png)

When you create an object you’ll be navigated to Design mode with the event stream for the new object selected. The preview in the following image shows you the events that were received split out by the unique values from the ID column.
 ![image](https://github.com/microsoft/dataActivator/blob/main/images/event_stream.png)
By default the events for five random instances for the previous 24 hours are displayed in the chart. You can change this using the population selector and time selectors above the card.
## Creating properties and adding steps to analyze your data 
Properties are aspects of an object that you want to monitor and respond to. You use the ‘New property’ button in the ribbon to create a new property. The new property appears as a draft called Untitled, with a Reference card as the first step in the property. ‘Cards’ represent the different steps or functions you use to manipulate the events and data to set up the conditions you want to monitor, and the actions you want Data Activator to take. 
 ![image](https://github.com/microsoft/dataActivator/blob/main/images/event_stream_draft.png)

The reference card brings in either an event stream, or the output of another property as the starting point for your new property.

The buttons in the ‘Add new step’ toolbar let you add new cards to manipulate the event data. For example, the most common first step will be to use ‘Select > Value from event’ to extract the value of a field from each event.
  ![image](https://github.com/microsoft/dataActivator/blob/main/images/select_value.png)

The chart on the card shows the output of the card's function (e.g. Grouping, comparisons, change detection). Each point on the chart shows when an action would be taken if you were to attach an action card immediately afterwards. 
## Adding actions 
Once you have a card showing the points at which you want an action take, you can add an action card. Different action types will have different parameters, such as the email address you want to send to, the workflow you want to start, subject line or additional information etc.

After adding an action you’ll see the toolbar change to allow you to start the trigger, or test the action. Testing the action will use the latest data from the property and pass those values to the email/Teams message.
## Property and trigger lifecycle
As mentioned previously in this article, newly created properties are in a Draft state. When you save them (or when autosave happens), the current configuration of the property is still a draft. Selecting ‘Publish’ commits the property/trigger and moves it out of a draft state. Published properties can be re-used in other properties or triggers.

If your property has an action, you also need to select Start from the toolbar for Data Activator to start running the trigger and taking action. 
 ![image](https://github.com/microsoft/dataActivator/blob/main/images/start_trigger.png)

Once started, you’ll see ‘Running’ appears in the title area of the property.
 ![image](https://github.com/microsoft/dataActivator/blob/main/images/trigger_running.png)

The toolbar will change to allow you to Stop the trigger. If you want to go back and make changes to the trigger (for example changing the condition it looks for) you need to select Edit from the ribbon, putting the property back into a Draft state.


 
# Adding an action to your trigger
Once you’ve defined a trigger condition, you’ll want to set up an action for Data Activator to take when the trigger condition is met. Data Activator can take three types of action:
1)	Send an email to you or someone else in your organization.
2)	Send a Teams message to you or someone else in your organization.
3)	Trigger a Power Automate flow.
The following section explains how to set up each type of action.
## Prerequisites
Before you set up an action, you must define a property with a trigger condition, as described in “Working with Data Activator’s object model”.
## Send an email notification
To send an email notification when your trigger condition met, begin by adding a “Act” step to your trigger definition. 

In this screenshot, the user is monitoring a fleet of electric scooters, and wants to send an email if the battery level on any scooter drops below 10%. The user has clicked “Act” to begin adding an email notification:
 ![image](https://github.com/microsoft/dataActivator/blob/main/images/select_email_action.png)

Next, define the email recipient, and supply an informative subject and headline. You can also choose which object properties to include in the mail body.
In the following example screen, a user sets up an email to an engineer to request that the engineer recharge the scooter with the low battery level:
 ![image](https://github.com/microsoft/dataActivator/blob/main/images/fill_email_template.png)

Before you activate your trigger, you’ll typically want to test it to make sure that the condition and the resulting email are correctly defined. To test your trigger, select the “Run Test” button:
![image](https://github.com/microsoft/dataActivator/blob/main/images/run_test.png)

 
This will generate a test email like the one shown below. To generate the data for the test mail, Data Activator will select one object from your input data for which the trigger condition is true and will send a test mail for that object. The test email will go to you, regardless of who you have specified as the recipient in the email card.

NOTE: To send a test mail, you will need to select at least 1 object instance for which the trigger condition is true. If the “Run Test” button is disabled, then use the object selector to select an object for which the condition is true.

 ![image](https://github.com/microsoft/dataActivator/blob/main/images/test_action_email.png)

Finally, once you are satisfied with the test mail, you can activate your trigger by pressing “Start”. Confirm the action by pressing “Continue” to activate your trigger.
 ![image](https://github.com/microsoft/dataActivator/blob/main/images/start_trigger.png)

At this point, your trigger is active, and Data Activator will send a mail to the specified recipient whenever the trigger condition is met.

After you start your trigger, Data Activator will publish the trigger to the “Triggers” section of your object definition. You can access it here later if you want to modify it. 
 
# End-to-end tutorial using simulated data
If you don’t have access to a streaming data source, you can use simulated data to try out Data Activator. When you create a new reflex item, select Get Data and choose the Simulator option.
 ![image](https://github.com/microsoft/dataActivator/assets/10352928/2b12c8a7-ae42-4b6a-a4f9-6bfe32da5f69)

Select Connect to continue.

You’ll see two new event streams created for Package and Driver data, and new events will arrive in the main area of the screen.
IMPORTANT: It can take up to 2 minutes for data to start appearing from the simulator.

If you now go into Design mode, you’ll see two objects have already been created, with the simulated data streams connected.
 ![image](https://github.com/microsoft/dataActivator/assets/10352928/79889e28-43c5-4f35-b1ae-d1d98655344a)

Data will start appearing as the simulator runs. Let’s try creating a new property to track the temperature of packages, and send an email if any package gets too hot.
Start by selecting New Property from the ribbon. Select the edit icon or the name of the new property to rename it from Untitled:
 ![image](https://github.com/microsoft/dataActivator/assets/10352928/0dcb7b5b-e924-4a80-b44d-6627ffa1086f)

The first step is to extract the temperature value from the events. From the toolbar at the bottom of the screen, choose Select -> Value from event.
 ![image](https://github.com/microsoft/dataActivator/assets/10352928/1401a31f-a8ed-4c9e-a256-8542fb24facd)

On the new Select card, choose Temperature from the Value dropdown menu
 ![image](https://github.com/microsoft/dataActivator/assets/10352928/eb02bdf2-f89c-489e-8812-3a9cdd9d349a)

This will give you a chart showing the temperature of each package.
 ![image](https://github.com/microsoft/dataActivator/assets/10352928/ac9df8f9-f521-43ac-860e-aca7f909aae8)

Well done, you’ve created your first property! We could immediately add an Act step after this and send an email each time an event is received, but we probably want to only send an alert when a certain threshold is crossed. The ‘Crosses above’ step lets us detect when that happens. Select it from the Detect button in the toolbar;
 ![image](https://github.com/microsoft/dataActivator/assets/10352928/6bdb88df-31a8-42c6-8f10-71461123eb55)

You’ll have to specify the threshold depending on the data your simulator created, but a threshold of 68-70 should generate some events:
 ![image](https://github.com/microsoft/dataActivator/assets/10352928/832572ca-1f4e-4e8c-b7b2-9a74696891a5)

Finally, let’s add an action from the Act toolbar button.
 ![image](https://github.com/microsoft/dataActivator/assets/10352928/6321c3f1-ba2b-4951-9dfa-e066410c741a)

Give the alert a Subject and Optional message if you want. In the preview, you should see dots representing times when an email would have been fired if the trigger was running. Select ‘Run Test’ on the toolbar to send a test message. Choosing Start will save the trigger and start monitoring the simulated data in the background.

