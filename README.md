
#Wunderlist Tasker Pebble Integration Tutorial
Description: This tutorial will walk through the setup of integrating Wunderlist with the Pebble watch. The goal is to have a watch face which displays the total count of Wunderlist tasks which are Overdue, Due Today, and Due Tomorrow.The fields will be updated automaticlly whenever a task is added or completed.
Tools: Tasker, AutoWeb, Wunderlist, Glance For Pebble, AutoRemote

---

###Wunderlist Setup
The first thing you need to do is setup an application within the Wunderlist web administration.
You can access the administration through this link:
git remote add origin <server>
https://developer.wunderlist.com/apps/new

You will need to create a new application. Fill in the App URL and Auth Callback URL fields with anything you want.
These are used for Oauth authentication, but we will not be using it here.

Copy the "Client ID" to notepad. We will need this for later.

Once you save the Wunderlist application, click the "Create Access Token" button.

Copy this value to notepad as well. We will need this for later too.

###AutoWeb setup
If you don't already have Autoweb, go out a get it from the GooglePlay store.
The application is currently in beta, but I haven't had any issues with it yet.
This will make the setup much easier. You can also use the Tasker plugin called RESTask for Takser, but I find it has lots of bugs. Autoweb is more stable and user friendly.

Here is the link to the play store:

https://play.google.com/store/apps/details?id=com.joaomgcd.autoweb

I hosted my AutoWeb configuration file for AutoWeb here in this repository. It contains all the setup you need to 
connect to the Wunderlist webservices. Click the "+" button on the top of the screen and press
IMPORT FROM FILE. Use the config file downloaded from this repository titled "Wunderlist API for AutoTools.txt".

###Tasker Setup
Now it's time to setup up Tasker. I've included the source of my Tasker task here in the github repository.
Download the file titled "Tasker_Task_GlanceWunderlistUpdate.txt" and import it into Tasker.

You should now have a new task called GlanceWunderlistUpdate.
The only thing you need to do is configure the AutoWeb Actions to import the ClientID and X-Access-Tokens.
These are the two peices of data I had you save to notepad in the first steps of this tutorial.
- Click on action 4. and 7. for the AutoWeb webservice
- Click the configuration button
- You should see the two fields at the bottom under the inputs section.
- Click on the two fields and enter the values here and save the configuration.

This task will take care of all the actions:
- Setup variables for Over Due Tasks, Tasks Due Today, and Tasks Due Tomorrow.
- It will run the AutoWeb Action to get all of your lists
- It will iterate over all of your lists and get all uncompleted tasks.
- It will then increment the variables created at the beginning of the task if the task has a due date of yesterday, today or tomorrow.
- Finally, it will send these variables to Glance with intent messages.

###Install the Glance Watch Face
You can now use the variables within Glance for your watch face. If you want to give my Glance Productivity Watch face a try
you can download it this repository titled "Productivity_Layout.gwf". If you prefer to use your own Glace watchface, you can reference the variables that were sent by tasker.

###AutoRemote
Being able to display these variables is all good and dandy. However, what happens when you complete a task?
We want the variables to update on our watch in real-time when we complete or add a task to our list.
We can do this with AutoRemote and Wunderlist Webhooks.

https://play.google.com/store/apps/details?id=com.joaomgcd.autoremote&hl=en

Once you download AutoRemote, there will be google shortcut link that you can use to configure
AutoRemote for your device. This will bring you into the messaging service.
You want to create a message here. I called my message "WunderListUpdate".
I also set a long random secure password for the message as well. You can configure this password on your device in the Autoremote settings.
After you type you message, you will see a box show up to the right of the page with a message that says:
"Use this url to directly send this message:". This is the link we are after! We are going to use this link in the 
WunderList Webhook API. Keep this link handy.

###Wunderlist Webhook setup
The Wunderlist Webhook API will send an HTTP notification based on a configurable URL.
A notifcation will be sent each time a list is updated. Unfortunately there is no GUI to manage these nofication.
You have to do it through the web service.

I'm not going to go through a deep dive on this, but I used a Chrome plugin called Advanced REST client.
It allows you to craft HTTP messages. First I used the Wunderlist API 
to get the IDs of all of the Lists I want to receive updates:

https://developer.wunderlist.com/documentation/endpoints/list

Then I used the Webhook API to send a JSON message which contains the list id and the URL we created in AutoRemote.
This is the documentation on the API.

https://developer.wunderlist.com/documentation/endpoints/webhooks

Once this has been setup, whenever a list is updated, the Webhook API will send HTTP message to the URL, which will send it to our AutoRemote client.

###The Final piece
We now can setup a new tasker profile. Create a new Event with a plugin of AutoRemote.
In the configuration you will see a "MessageFilter". Click this and enter the Message title we set in the Autoremote message settings above.
In my case it is "WunderListUpdate". Now point this to the GlanceWunderlistUpdate Task.
That's it! Now whenever a task is added or completed wunderlist will send a notification to
AutoRemote, which will in turn send it to our phone, which then will send to tasker to kick off the Glance update task.
The task will run and get the updated lists, increment our variables and then update glance which will update our watchface on demand!

###Other suggestions
- I also setup a second Tasker Event to run the task at midnight every night. This will ensure our our watch face stays updated from day to day.
- I also use a Pebble app called WunderPebble which allows me to view and complete tasks right from the pebble.
- In addition, if you setup reminders on your tasks in Wunderlist and push them to your pebble, you can complete and dismiss tasks right from the quick notifcations.
- I setup a task to also run every hour to keep the numbers up to date. Sometimes there is network hiccups which cause the task to fail. This tasks helps keep things in synch if there are ever any issues.
- Final note, there is another application called WunderVoice for Pebble where you can add items directly to your Wunderlist inbox with voice commands.

It is a lot to setup and configure, but once it is done it work well. And it has kept me much more productive!















