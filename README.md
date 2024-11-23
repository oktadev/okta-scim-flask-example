# Flask SCIM Server (Configured for use with Okta)
**NOTE:** This is not meant to be a fully-fledged, IDP-agnostic SCIM server. This server was designed specifically to integrate with Okta.

Please read [How to Build a Flask SCIM Server Configured for Use with Okta](https://developer.okta.com/blog/2021/09/01/flask-scim-server) to see how this app was created.

* [Dependencies](#dependencies)
* [Getting Started](#getting-started)
* [Links](#links)
* [Help](#help)
* [License](#license)

## Dependencies:
- [virtualenv](https://docs.python.org/3/library/venv.html)
- [PostgreSQL](https://www.postgresql.org/)
- [ngrok](https://ngrok.com/)

## Getting Started

### Setup Okta

To use Okta, you'll need to change a few things. First, you'll need to create a free developer account at <https://developer.okta.com/signup/>. After doing so, you'll get your own Okta domain, that has a name like `https://dev-123456.okta.com`.

### Setting up the Flask app and Postgres DB
1.  Clone the repo, open terminal and ```cd``` into the project root.
2.  Create a new virtualenv in the root folder with ```virtualenv env```
3.  Run the virtual environment with ```source env/bin/activate```
4.  Install necessary Python packages with ```pip install -r requirements.txt```
5.  Create a new Postgres database ```scim``` at ```postgresql://localhost/scim```. Enter the psql shell by opening a new terminal tab and typing ```psql postgres```. Create the DB with ```CREATE DATABASE scim;``` (Run ```\l``` to double check the database has been created)
6.  Go back to the terminal tab that is in the flask app root. Run the following commands to create migrations and tables in the ```scim``` database:
    - ```python manage.py db init```
    - ```python manage.py db migrate```
    - ```python manage.py db upgrade```
    
> Feel free to hop back to your postgres tab and run ```\c scim``` to navigate into the scim db, then ```\dt``` to see your new tables: ```groups```, ```users```, ```link```. (Link is a table facilitating the many-to-many relationship between users and groups)

7. Everything should be setup now to run the server locally. Finally run ```python app.py``` to do so. You should now have your SCIM server running on http://localhost:5000.

### Setting up ngrok to route requests from Okta to localhost
- Once you have ngrok installed, run ```./ngrok http 5000``` to create a tunnel from ngrok to your http://localhost:5000. Copy the ```https``` Forwarding URL created by ngrok as you will need it later.

### Creating and configuring your Okta Application
- Now it's time to create a new SCIM integration in Okta. If your SCIM app(s) are already setup on the Okta side, feel free to skip ahead to **Test the SCIM Server**. There are two options that will work with this server, and I will ALWAYS recommend the first, which is using an Okta SCIM template application.

#### Option 1: SCIM Template App

1. In your Okta dashboard, go to **Applications** -> **Applications**, then click the **Browse App Catalogue** button. Search for **SCIM 2.0 Test App (Header Auth)** and click the **Add** button once you have it pulled up.
2. In the **General Settings** tab, click **Next**.
3. We will set this up as a SWA application, so in the **Sign-On Options** tab, click **Secure Web Authentication**.
4. Click **Done**.
5. Tab over to **Provisioning** and click **Configure API Integration.**
6. Check **Enable API integration**.
7. In the Base URL field, paste in the ngrok url you generated above with **/scim/v2** appended to the end. In the API Token field, type **Bearer 123456789**. (Later on we will go over how to customize this auth header, but out-of-the-box, the SCIM server expects this value)
8. Click Test API Credentials and you should get a success message like the below:

![SCIM_1](https://i.imgur.com/iFaxU9G.png)

> You can navigate to `http://localhost:4040` to see the request from Okta on this request, as well as the response from the SCIM server.

![SCIM_2](https://i.imgur.com/gCht05S.png)

9. Click **Save**.

Now your **Provisoning** tab will look a bit different.

10. Click **Edit** next to **Provisioning to App** and check off:
    - Create Users
    - Update User Attributes
    - Deactivate Users
    
And **Save**.

![SCIM_3](https://i.imgur.com/KRZCbiw.png)

#### Option 2: Enable SCIM Provisioning for Existing AIW App

> Feel free to skip over this section to **Test the SCIM Server** if you set your SCIM integration up above.

1. In your Okta dashboard, go to **Applications** -> **Applications**, then click the **Create App Integration** button. For this setup we will select **SWA - Secure Web Authentication**. Click **Next**.
2. You can put whatever you'd like for the **App Name** and **App Login Page URL**, as we will just be loking at the SCIM functionality and not the SWA aspect of this app. Click **Finish**.
3. In the **General** tab of the app, click **Edit** and toggle Provisioning from **None** to **SCIM**. Click **Save**.
4. Your app should now have a **Provisioning** tab. Tab over to it and fill out the integration settings like the below image. Make the Authorization header **123456789**. You can change this later in the SCIM flask app.

![SCIM_4](https://i.imgur.com/yaEl9FD.png)

5. Click **Test Connector Configuration** and you should see the following success confirmation:

![SCIM_5](https://i.imgur.com/OetQUDR.png)

6. At which point, you can now click **Save**.

> You can navigate to `http://localhost:4040` to see the request from Okta on this request, as well as the response from the SCIM server.

![SCIM_6](https://i.imgur.com/gCht05S.png)


7. Now your **Provisoning** tab will look a bit different. Click **Edit** next to **Provisioning to App** and check off:
    - Create Users
    - Update User Attributes
    - Deactivate Users
    
And **Save**.

![SCIM_7](https://i.imgur.com/QzzUgHk.png)

You should now be set on the Okta side to start testing the SCIM server.


## Links

This example uses the following open source libraries:

* [SCIM](http://www.simplecloud.info/)
* [Flask](https://flask.palletsprojects.com/en/2.0.x/)
* [PostgreSQL](https://www.postgresql.org/)

## Help

Please post any questions as comments on the [How to Build a Flask SCIM Server Configured for Use with Okta](https://developer.okta.com/blog/2021/09/01/flask-scim-server), or visit our [Okta Developer Forums](https://devforum.okta.com/). 

## License

Apache 2.0, see [LICENSE](LICENSE).

[blog-post]: https://developer.okta.com/blog/2021/09/01/flask-scim-server "How to Build a Flask SCIM Server Configured for Use with Okta"
