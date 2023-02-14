# Tutorial: Build a web app with Django & Replit

# Setup

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