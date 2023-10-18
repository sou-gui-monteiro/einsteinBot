# Einstein BOT


## Scratch org creation

To make our lives easy, we can automate many things, putting the Salesforce CLI, and the bash scripts to run together!

    ./scripts/bash/createScratchOrg.sh tmpBot

### Create a digital experience

Yes, we'll not clear see that on the official cookbook, but you'll need that during the bot configuration, so... let's do that.

You can do that out of the box... or execute the CLI:

    Bash command:
    sf community create --name 'Total Bot' --template-name 'Customer Service' --url-path-prefix totalbot --description 'The Total bot community'

    Windows command:
    sf community create --name "Total Bot" --template-name "Customer Service" --url-path-prefix totalbot --description "The Total bot community"

After that, you'll still needing apply the necessary configurations like activation, publishing, etc, but that is it for now...

And when the org and digital experience have been created, deploy the thing done before...

    sf project deploy start --ignore-conflicts --manifest manifest/package.xml 


### Add some data 

You can create some data on the BotOrder object using the code below.

<!-- SELECT Id, Name, Status__c, OrderDate__c, ContactId__c, Amount__c FROM BotOrder__c -->

    List<String> orderListNumber = new List<String>{
        'O-00123456',
        'O-00123457',
        'O-00123458',
        'O-00123459',
        'O-00123460'
    };

    List<BotOrder__c> BotOrderList = new List<BotOrder__c>();

    Contact newContact = new Contact(
        FirstName = 'First',
        LastName = 'Contact'
    );

    insert newContact;

    String contactId = String.valueOf(newContact.Id);

    system.debug('contactId ' + contactId);

    Integer intCount = 100;

    for (String orderMumber : orderListNumber){
        Date thisDate = Date.Today() + intCount;
        Decimal decAmount = 1000 + intCount;
        BotOrderList.add(
            new BotOrder__c (
                Name = orderMumber, 
                Status__c = 'New', 
                OrderDate__c = thisDate, 
                ContactId__c = contactId, 
                Amount__c = decAmount
            )
        );
    }

    insert BotOrderList;



## My MIAW cookbook

Based on the official salesforce [Einstein Bots Developer Cookbook](https://resources.docs.salesforce.com/latest/latest/en-us/sfdc/pdf/bot_cookbook.pdf), I have created this one.

But here, besides we have the bot feature, I'm also will show you [What’s Messaging for In-App and Web](https://help.salesforce.com/s/articleView?id=sf.reimagine_miaw.htm&type=5) and the necessary things, to have that working.

Let's start understanding were I got this ideas? Sure, from the free official salesforce material!
* [Digital Engagement](https://help.salesforce.com/s/articleView?id=sf.sales_core_digital_engagment.htm&type=5) is the big picture;

You need to understand a little bit about [Messaging in Service Cloud](https://help.salesforce.com/s/articleView?id=sf.livemessage_intro.htm&type=5) and what would be the "Messaging for In-App and Web" aka [MIAW](https://help.salesforce.com/s/articleView?id=sf.reimagine_miaw.htm&type=5), and mainly [how to Set Up Messaging for In-App and Web](https://help.salesforce.com/s/articleView?id=sf.miaw_setup_stages.htm&type=5)

### User Access

1. So, [Give Users Access to Messaging for In-App and Web](https://help.salesforce.com/s/articleView?language=en_US&id=sf.miaw_prepare_users.htm&type=5)

    After that, assign the permission to the user if you are using a CLI approach

    sfdx force:user:permset:assign --perm-set-name MIAWPermissionSet --target-org tmpBot
1. Create a the following [Presence Status for messaging](https://help.salesforce.com/s/articleView?id=sf.service_presence_create_presence_status.htm&type=5clear)
    1. Chat
    1. Messaging
    1. Chat & Messaging
    1. Busy
1. Create a permission set for your messaging agents (CMIAW Agents Permission Set)
    1. Select None from the License dropdown menu;
    1. Click the name of your permission set from the related list, and select App Permissions.
    1. Click Edit, and check Messaging for In-App and Web Agent;
    1. Set the **Service Presence Statuses Access**;
    1. Set the Messaging Sessions object the appropriate access;
    1. Set the Messaging Users object the appropriate access;
    1. Save your changes whenever is needed;

    After that, assign the permission to the user if you are using a CLI approach

    sfdx force:user:permset:assign --perm-set-name MIAWAgentsPermissionSet --target-org tmpBotTwo

### Org preparation

Many features works together, so you need to [Prepare a Salesforce Org for Messaging for In-App and Web](https://help.salesforce.com/s/articleView?id=sf.miaw_prepare_org_1.htm&type=5)


1. [Enable Omni-Channel](https://help.salesforce.com/s/articleView?id=sf.omnichannel_enable.htm&type=5)
1. [Create the Service Channel for messaging](https://help.salesforce.com/s/articleView?id=sf.service_presence_create_service_channel.htm&type=5)
1. Create the the **MIAW Queue** (and a **Fallback Queue**) following this [Queues guidance](https://help.salesforce.com/s/articleView?id=sf.setting_up_queues.htm&type=5)
1. Create an Omni Flow, but if you set up your Pre-Chat Form, return to this flow to map pre-chat fields to your messaging channel.
    1. From Setup, in the Quick Find box, enter Flows, and select Flows.
    1. Create a New Flow.
    1. In the All + Templates tab, select Omni-Channel Flow.
    1. From the Manager tab, create a New Resource.
    1. Select Variable as your Resource Type.
    1. For the API Name, enter recordId. For the Data Type, specify text.
    1. Check Available for input, and then click Done.
    1. From the Elements tab, select a Route Work action in your flow.
    1. Name the New Action. Use recordId variable as the input value. Select Messaging for the Service Channel.
    1. Specify Queue, Agent, Bot, or Skills as the Route To value.
        1. If you select Queue, use the Queue ID for the queue where you want to direct the work.
        1. If you select Agent, add the agent’s name via the Agent ID field.
        1. If you select Bot, search for the bot name.
        1. If you select Skills, add the Skill Requirement List.

        BOT BOT BOT BOT BOT BOT BOT BOT BOT BOT BOT BOT BOT BOT BOT BOT BOT

    1. Click Done.
    1. Save and Activate your flow.
1. Add a Messaging Channel, but bear in mind that you don't need to toggle the **Messaging** button to on. The Messaging toggle is for Facebook, WhatsApp, and SMS messaging only.
    1. Set the flow created on the previous steps as the "Flow Definition";
    1. Check the "Show estimated wait time" option;
    1. Set "5" as "Minutes to Timeout";
    1. Save and activate this channel.
1. Prepare the Messaging Session Layout
    1. From Setup, in the Quick Find box, enter Lightning App Builder, and select Lightning App Builder.
    1. To create a Lightning page, select New.
    1. Select the page type Record Page.
    1. Name the page, and then select Messaging Session as the Object.
    1. On the next screen, Select CLONE SALESFORCE DEFAULT PAGE and finish.
    1. When you’re inside the app builder, add the Enhanced Conversation component to the page.
    1. Activate the page.

### Einstein Bot configuration

So, now, let's create the Einstein Bot.
This activation add the **sfdc.chatbot.service.permset** permission set  to your org, and here you can grant access to Apex classes, objects and enable access to flows from the Run Flows checkbox in the System Permissions section.


1. From Setup, use the Quick Find box to find Einstein Bots. 
1. Enable Einstein Bots.
1. Click New on the Einstein Bots setup page (let's create the **MIAW Bot**).
1. After filling up other information, when you arrives at "Route bot conversations with Omni-Channel Flow", add the flow you have created before;
1. Clicking on **Proceed** button, the setup will create or adjust:
    * [Custom Dialogs](https://help.salesforce.com/s/articleView?id=sf.bots_service_dialog_about.htm&type=5) for the Welcome message and Main Menu
    * [System Dialogs](https://help.salesforce.com/s/articleView?id=sf.bots_service_system_bot_dialog.htm&type=5) for commonly-used actions like agent escalations
    * Bot Analytics to help measure performance
    * Omni-Channel Flow for routing conversations
1. Click **Finish**.
1. To activate your bot, click Activate.

<!-- We'll not activate this bot now, but we'll get back here later... -->

### Routing to the Bot

At this point, the setup have created another "Route to MIAW Bot" flow (following the name we have used in this cookbook), that will route the arriving chats to this bot.

But any way, you need to change this flow, adding a real queue there and activating it.
In this example, I did the same done on the "MIAW to Queue" flow, read the queue name from the configuration.

### Preview the bot

To preview the bot from within the Bot Builder, add an [Embedded Chat](https://help.salesforce.com/s/articleView?id=sf.snapins_chat_setup.htm&type=5) deployment.

1. Go to [Embedded Service Deployments](https://help.salesforce.com/s/articleView?id=sf.snapins_create_deployment.htm&type=5);
1. Clicks on "New deployment"
1. Select "Embedded Chat" and "Next"
1. Name it as "Bot Preview" and select the digital experience created before and save;

### Grab user information with apex

We are using this simple "MessagingContactData" apex class, that you need to grant the bot access through the "Chatbot" permission set to use.

1. Create a new bot dialog named "Messaging Initialization"
1. Add an action at the top of the flow using the Apex action type;
1. For the input, use the built-in variable [Context] End User Id.
1. Create a new variable named MessagingContact;
1. Return to the "Welcome" dialog and create a Call Dialog rule;
1. Add a rule to the top of the “Welcome” dialog flow.
1. Add Condition to the rule. 
    1. Configure the condition so that MessagingContact is the variable name and that the operator is Is Not Set. 
    1. Set the Rule Action to CallDialog and the Dialog Name to MessagingInitialization. 


### Asking things to the bot

To question things....


You can create some data on the BotOrder object using the code below

<!-- SELECT Id, Name, Status__c, OrderDate__c, ContactId__c, Amount__c FROM BotOrder__c -->

    List<String> orderListNumber = new List<String>{
        'O-00123456',
        'O-00123457',
        'O-00123458',
        'O-00123459',
        'O-00123460'
    };

    List<BotOrder__c> BotOrderList = new List<BotOrder__c>();

    String contactId = String.valueOf([
        SELECT Id FROM Contact
        LIMIT 1
    ].Id);

    system.debug('contactId ' + contactId);

    Integer intCount = 100;

    for (String orderMumber : orderListNumber){
        Date thisDate = Date.Today() + intCount;
        Decimal decAmount = 1000 + intCount;
        BotOrderList.add(
            new BotOrder__c (
                Name = orderMumber, 
                Status__c = 'New', 
                OrderDate__c = thisDate, 
                ContactId__c = contactId, 
                Amount__c = decAmount
            )
        );
    }

    insert BotOrderList;

Let's query the data using a "OrderNumberString" (regular expression: \bO\-\d{8}\b) entity to understand the order pattern we have created above, and the "OrderNumber" variable.

Create a “Order Status”  question dialog, and add a "Question" on the main body, setting the "OrderNumberString" as the "Expected response format" and the "OrderNumber" as the "Save Answer to Variable".

After that, you can add a "response message" to the "Order Status" dialog.


The end point is the same you see under "Chat Settings", but without the protocol.

Optimize Bot Flow with Embedded Chat

[Glossary](https://help.salesforce.com/s/articleView?id=sf.miaw_glossary.htm&type=5)

[Set Up Messaging for In-App and Web](https://help.salesforce.com/s/articleView?id=sf.miaw_setup_stages.htm&type=5)
[Messaging for In-App developer guide](https://developer.salesforce.com/docs/service/messaging-in-app/overview)
[Configure a Messaging for In-App Deployment](https://help.salesforce.com/s/articleView?id=sf.miaw_deployment_mobile.htm&type=5)

[Get Started with Messaging for Web](https://developer.salesforce.com/docs/service/messaging-web/guide/introduction.html)

[Get Started with Einstein Bots API](https://developer.salesforce.com/docs/service/einstein-bot-api/guide/prerequisites.html)
    [Introducing the Einstein Bots Platform API](https://developer.salesforce.com/blogs/2022/05/introducing-the-einstein-bots-platform-api)

The bot get external access through the "Chat Buttons & Invitations", where you define the routing flow

1. []()
1. []()
1. []()
1. []()
1. []()
1. []()
1. []()
1. []()
1. []()
1. []()
1. []()
1. []()




-----------------------------------------------------------------


1. But actually, we'll start configuring [Messaging](https://help.salesforce.com/s/articleView?id=sf.livemessage_enable.htm&type=5) settings:
    1. [Prepare for WhatsApp, Facebook Messenger, and SMS](https://help.salesforce.com/s/articleView?id=sf.messaging_prepare.htm&type=5) is the same preparation you need to other MIAW features;

			
			
				
		Set Up Automated Notifications in Service Cloud Messaging
			https://help.salesforce.com/s/articleView?id=sf.livemessage_automatic_message_notifications.htm&type=5
		Message with Customers in the Service Console
			https://help.salesforce.com/s/articleView?id=sf.livemessage_agent.htm&type=5

	Omni-Channel
		Set Up Omni-Channel
			https://help.salesforce.com/s/articleView?id=sf.service_presence_intro.htm&type=5
	Web chat
		Create a Basic Chat Implementation
			https://help.salesforce.com/s/articleView?id=sf.live_agent_set_up_basic_implementation.htm&type=5
		Set Up Your Embedded Chat Window
			https://help.salesforce.com/s/articleView?id=sf.snapins_chat_setup.htm&type=5
	Einstein Bots
		https://help.salesforce.com/s/articleView?id=sf.bots_service_intro.htm&type=5



![CMS done](images/b2bCMSImport4.png)