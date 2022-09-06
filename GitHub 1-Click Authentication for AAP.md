# GitHub 1-Click Authentication for AAP

## On GitHub side:

1. Login your GitHub account, click on your profile picture on right upper side of your screen and go to 'Settings'.
2. On the left side, scroll down and select 'Developer settings'.
3. On the left side, select 'OAuth Apps'.
4. Click on 'Register a new application'.
5. Give an application name which will be display name of application while authenticating any user.
6. In 'Homepage URL', enter the homepage URL as you see when you login normally.
7. In 'Authorization callback URL', put 'https://<your IP address/domain here>/sso/complete/github/' and click on 'Register application'.
8. Go to 'Settings /Developer settings/<yourapp>', go to 'General' and note down 'Client ID'.
9. In 'Client Secrets', click on 'Generate a new client secret'.
10. Note down the 'Client secret' string, go below and click on 'Update application'.
  
  ![image](https://user-images.githubusercontent.com/24843193/188623702-f72d66a6-d275-4cdd-86da-d1e785f49913.png)
  
  
  ![image](https://user-images.githubusercontent.com/24843193/188622353-df964388-ee19-4a9e-9d83-2627b0e0202d.png)

  
  
## On AAP side:
  
1. Login as 'admin' on AAP.
2. Scroll down to left pane and go to 'Settings'
3. In 'Authentication' section, click on 'GitHub settings'.
4. Go to 'GitHub Default' and click on 'Edit'.
5. Enter your 'Client ID' in 'GitHub OAuth2 Key' field.
6. Enter your 'Clinet secret' string in ''GitHub OAuth2 Secret' field.
7. Click on 'Save' and test if it works.
  
  ![image](https://user-images.githubusercontent.com/24843193/188622659-d8b393a1-99fe-43b3-b504-b3efd67381f3.png)

> Note: By default, the user role will be as normal user.
