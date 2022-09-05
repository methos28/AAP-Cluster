# Create Slack Notification for Ansible Automation Platform

## On Slack Side:

1. Create account, login and navigate to https://api.slack.com/apps
2. Click "Create New App" at the top right
3. Enter a name for your app and assign it to a workspace.
4. Click on "OAuth and Permissions" on the left.
5. Scroll down to Scopes and click "Add an OAuth Scope" and type "chat:write" and select the first result.
6. Scroll to the top and click "Install App to Workspace" and click "Allow"
7. Copy "Bot User OAuth Access Token" as this is the token that will be used by Ansible Automation Platform.
8. Optional: For private channels, you will need to add the app from the Slack channel itself. To do this, open the channel details pane, expand the Apps section, and click Add app. Type in the name of your app and click Add.
9. You must also invite the notification bot to join the channel(s) in question in Slack. Note that private messages are not supported. 
10. In order to invite, in that respective channel message section, type '@'and the name of the bot and select send now. This will ask if you want to add that person to the channel and select 'Add to Channel'.


## On Ansible Automation Platform Side:

1. Log in to AAP as an admin user and select Notifications
2. Click Create New Notification.
3. Fill in the appropriate fields, and select Slack.
4. Add the appropriate channels following the format in the tool tip (Enter one Slack channel per line. The pound symbol (#) is required for channel)
5. For the token, you'll need the "Bot User OAuth Access Token" (noted above). This can be obtained from the "Install App" or "OAuth Permissions"
6. Click Save.
7. Click Test Notification to verify it is successful.

![image](https://user-images.githubusercontent.com/24843193/188406899-d1361e98-9b23-4422-9e94-38bae1754e9f.png)


> References:
>
> https://docs.ansible.com/automation-controller/latest/html/userguide/notifications.html#slack
> https://access.redhat.com/solutions/2999831
> https://access.redhat.com/solutions/3991231
