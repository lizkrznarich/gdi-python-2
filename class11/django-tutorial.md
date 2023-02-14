# Tutorial: Build a web app with Django & Replit

In part 1 of this tutorial, we'll create a basic Django app in Replit with 1 view and 1 URL.

1. [Django project setup](#django-project-setup)
2. [Create homepage templates](#create-homepage-templates)
3. [Create homepage view & configure URL](#create-homepage-view--configure-url)
4. [Get the user's IP address](#get-the-users-ip-address))
5. [Get the user's location](#get-the-users-location)
6. [Get the current weather for the user's location](#get-weather-data-for-the-users-location)

# Django project setup

Before we can get started, we first need to set up a new Django project in Replit.

1. Sign into your Replit account at [replit.com](https://replit.com). If you don't have an account, create a new one.

2. In Replit, click the **+ Create** button. In the window that opens, type **django** in the Template field and choose the **dajngo replit** option from the dropdown. Optionally, add a custom title in the Title field and click **+ Create Repl**

![](../images/create-repl-django.png)

3. The basic Django file structure will appear in the Files panel at left. Click README.MD to see the instructions for getting Django up and running.

4. Per README.MD, open the Shell tab in the right panel and run the following command to generate a secret key.

        python
        import secrets
        secrets.token_urlsafe(32)

![](../images/django-gen-key.png)

5. Copy the generated key, then click the **Secrets** icon in the **Tools** section of the left panel. In the **Secrets** tab that opens in the right panel, type **SECRET_KEY** in the **key** field, paste the generated key in the **value** field, and **Click Add new secret**.

![](../images/dajngo-save-key.png)

6. Click the green **Run** button. Once Django has started, a page with the message "The install worked successfully! Congratulations!" should appear in the **Webview** tab at right. This is your Django app! Click Open in new tab to view it in full screen.

![](../images/django-success.png)


# Create homepage templates

In this section, we'll add HTML templates for our homepage and configure Django to look for our templates in the correct directory.

1. In the **Files** panel, hover over **django_project**, click the 3 dots icon and choose **Add folder**.

![](../images/django-add-folder.png)

2. Type **templates** in the folder name box and press **Enter/Return** to create the folder.

3. Hover over **templates**, click the 3 dots icon and choose **Add file**.

![](../images/django-add-file.png)

4. Type **base.html** in the file name box and press**Enter/Return** to create the file.

5. Copy the template code below and paste it into the **base.html** file. This will be the parent template for all of our other templates.

        {% load static %}
        <!DOCTYPE html>

        <html lang="en">
        <head>
            <meta charset="UTF-8">
            <title>Hello World!</title>
            <meta charset="UTF-8"/>
            <meta name="viewport" content="width=device-width, initial-scale=1"/>
            <link rel="stylesheet" href="{% static "css/style.css" %}">
        </head>
        <body>
            {% block content %}{% endblock content %}
        </body>
        </html>

6. Create another file inside **templates** named **index.html**. Copy the code below and paste it into the **index.html** file. The line between ```{% block content %}``` and ```{% endblock content %}``` will be inserted where ```{% block content %}{% endblock content %}``` is located in the parent base.html template.

        {% extends "base.html" %}

        {% block content %}
        <h1>Hello World!</h1>
        {% endblock content %}

7. We need to tell Django where to look for our template files. In the **Files** panel, click **settings.py** to open it. Scroll to the **TEMPLATES** section and replace the line that begins with **DIR** with the code below.

        'DIRS': [os.path.join(BASE_DIR, 'django_project', 'templates')],

![](../images/django-settings-dirs.png)

# Create homepage view & configure URL

In this section, we'll create a new view that points to the homepage template, and then configure a URL to point to the view.

7. Create a new file inside **django_project** named **views.py** and paste the code below to create a new view that directs to our index.html template.

        from django.shortcuts import render

        def home(request):
            return render(request, 'index.html')

8. Finally, we need to link our view to a URL. In the Files panel, click the **django_project** folder, then click **urls.py**. In the imports section add the line below.

        from . import views.py

9. Add the following line to the url_patterns list to configure a new URL pattern that points to our index view.

        path('', views.index, name='index'),

10. The complete urls.py should look like this:

        from django.contrib import admin
        from django.urls import path
        from . import views

        urlpatterns = [
            path('', views.index, name='index'),
            path('admin/', admin.site.urls),
        ]

11. Open the **Webview** tab (Tools > Webview). You should see a "Hello world!" message from your index template.

![](../images/django-hello-world.png)

# Get the user's IP address

In order to get the user's location, we first need to get their IP address. An IP address identifies the device that a user's request is coming from.

1. Open **views.py** and update the **index(request)** function so that it gets the user's IP address from the built-in Dajngo request object and passes it to the index.html template.

        def index(request):
            x_forwarded_for = request.META.get('HTTP_X_FORWARDED_FOR')
            if x_forwarded_for:
                ip = x_forwarded_for.split(',')[0]
            else:
                ip = request.META.get('REMOTE_ADDR')
            return render(request, 'index.html', {'ip': ip})

2. Open the **index.html** template and add the line below Hello World.

        <p>You are visiting from IP address {{ ip }}</p>

3. Your page should now show the IP address you are visting from!

![](../images/django-ip.png)

# Get the user's location

Now that we have the user's IP, we can get the geolocation information associated with that IP. We'll use [https://ip-api.com](https://ip-api.com) and the requests module for this. IP API is very simple - send a request in format http://ip-api.com/json/24.177.113.128, and it will return a JSON response with the corresponding location data:

        {"status":"success","country":"United States","countryCode":"US","region":"WI","regionName":"Wisconsin","city":"Madison","zip":"53705","lat":43.0725,"lon":-89.4491,"timezone":"America/Chicago","isp":"Charter Communications","org":"Spectrum","as":"AS20115 Charter Communications","query":"24.177.113.128"}

1. Open **views.py** and add the requests module to the list of imports.

        import requests

2. Add a new function to views.py that sends a requests to ip-api.com

        def get_location_from_ip(ip_address):
            response = requests.get("http://ip-api.com/json/" + ip_address)
            return response.json()

3. Call the new function from your **index(request)** function and assign the response to a variable, and pass that data to the template.

        def index(request):
            x_forwarded_for = request.META.get('HTTP_X_FORWARDED_FOR')
            if x_forwarded_for:
                ip = x_forwarded_for.split(',')[0]
            else:
                ip = request.META.get('REMOTE_ADDR')
            location = get_location_from_ip(ip)
            return render(request, 'index.html', {'ip': ip, 'location': location})

4. Add the user's location info to the **index.html** template by pasting the following line beneath "You are visiting from...".

        <p>You are located in {{ location }}</p>

![](../images/django-location-json.png)

5. Oops! The raw response from ip-api.com doesn't look so nice. We can get individual values using the syntax below.

        <p>You are located in {{ location.city }}, {{ location.region }}, {{ location.country_Code }}</p>

![](../images/django-location-pretty.png)


# Configure OpenWeather API key

We'll get weather data from [OpenWeather](https://openweathermap.org) using their [Current Weather API](https://openweathermap.org/current). To use this API, we first need to register for a key and configure that key in our Django settings file.

1. Register for an OpenWeather API key at [https://home.openweathermap.org/users/sign_up](https://home.openweathermap.org/users/sign_up). You do NOT need to tick the checkboxes to receive emails from OpenWeather.

![](../images/django-open-weather.png)

2. Visit the **API Keys** tab in your account and copy the value in the **Key** field. Note! It can take up to 2 hours for a new key to become active.

![](../images/django-open-weather-key.png)

3. In Replit, click the **Secrets** icon in the **Tools** section of the left panel. In the **Secrets** tab that opens in the right panel, type **OPEN_WEATHER_KEY** in the **key** field, paste your OpenWeather API key in the **value** field, and **Click Add new secret**.

![](../images/django-replit-open-weather-secret.png)

4. In the **Files** panel, click **settings.py** to open it. At the bottom of the file, add a new line with the code below.

        OPEN_WEATHER_KEY = os.getenv('OPEN_WEATHER_KEY')

![](../images/django-settings-open-weather-token.png)

*Note: We could paste this key directly into our code, but that presents a security risk. If our code becomes public (either on purpose or accidentally), others could use our key maliciously. Keys should be treated like passwords, they should never be placed directly in code.*

# Get weather data for the user's location

Now that we have the user's location and our OpenWeather API key, we can get the current weather for that location using a request in the format ```https://api.openweathermap.org/data/2.5/weather?lat={lat}&lon={lon}&appid={API key}&units=imperial```.

1. Open **views.py** and import the settings file. This is how we share values between files in Python!

        from . import settings

3. Open **views.py** and add a new function that sends a requests to api.openweathermap.org

        def get_weather_from_location(location):
            token = os.environ.get("OPEN_WEATHER_TOKEN")
            url = "https://api.openweathermap.org/data/2.5/weather?lat=" + location['lat'] + "&lon=" + location['lat'] + "&units=imperial&appid=" + settings.OPEN_WEATHER_KEY
            response = requests.get(url)
            return response.json()

*Note: the url above is a very long string. We can use Pythons built-in string method [.format()](https://docs.python.org/3/library/stdtypes.html#str.format) to shorten it a bit. In place of each ```{}``` in the string, Python will substitute an argument passed to .formt(), in order from left to right.*

        url = "https://api.openweathermap.org/data/2.5/weather?lat={}&lon={}&units=imperial&appid={}".format(location['lat'], location['lon'], settings.OPEN_WEATHER_KEY)

4. Call the new function from your **index(request)** function and assign the response to a variable, and pass that data to the template.

        def index(request):
            x_forwarded_for = request.META.get('HTTP_X_FORWARDED_FOR')
            if x_forwarded_for:
                ip = x_forwarded_for.split(',')[0]
            else:
                ip = request.META.get('REMOTE_ADDR')
            location = get_location_from_ip(ip)
            weather = get_weather_from_location(location)
            return render(request, 'index.html', {'ip': ip, 'location': location, 'weather': weather})

5. Add the weather info for the user's location to the **index.html** template by pasting the following line beneath "You are located in...".

        <p>Current weather: {{ weather }}</p>

![](../images/django-weather-json.png)

6. Like with location, ```weather``` contains the entire OpenWeather JSON response. We can make things prettier by displaying just specific values. Update the "Current weather" line in **index.html** with the code below.

        <p>Current weather:</p>
        <table>
            <tr><td>Temperature</td><td>{{ weather.main.temp }} F</td></tr>
            <tr><td>Wind</td><td>{{ weather.wind.speed }} mph</td></tr>
            <tr><td>Conditions</td><td>{{ weather.weather.0.description }}</td></tr>
        </table>


![](../images/django-weather-pretty.png)

# Next steps



