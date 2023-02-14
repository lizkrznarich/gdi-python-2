# Tutorial: Build a web app with Django & Replit

In this tutorial, we'll create a basic Django app in Replit with 1 view and 1 URL.

1. Django project setup
2. Create homepage templates
3. Create homepage view & configure URL

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

11. Open the Webview tab (Tools > Webview). You should see a "Hello world!" message from your index template.

![](../images/django-hello-world.png)


