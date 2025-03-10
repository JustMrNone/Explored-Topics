Below is a detailed, step-by-step guide for setting up **Cloudflare Turnstile CAPTCHA** in your Django project. This will cover everything from the initial setup, configuring the API keys, integrating CAPTCHA into your login and registration views, and handling responses correctly.

### Steps to Set Up Cloudflare Turnstile CAPTCHA in Django:

### 1\. **Create an Account on Cloudflare**

If you haven't already, start by creating an account on Cloudflare.

-   Go to [Cloudflare's website](https://www.cloudflare.com/) and create an account.
-   After logging in, navigate to the **Turnstile** page (Cloudflare's CAPTCHA solution).

### 2\. **Create a Turnstile Site Key and Secret Key**

After logging into your Cloudflare account:

1.  Go to the **Turnstile** settings page.
2.  Click on **Create a Site** or **Add Site** to get your **site key** and **secret key**.
3.  During setup, you will be asked to specify the domains where your CAPTCHA will be used (for example, `localhost` for development or your actual domain for production).
4.  Cloudflare will generate two keys:
    -   **Site Key**: This key is used on the frontend (in your HTML).
    -   **Secret Key**: This key is used in the backend to validate the CAPTCHA response.

**Make sure to copy both the **site key** and **secret key**.** You will need them later for the frontend and backend configuration.

### 3\. **Store Keys in `.env` File**

For security and organization, it's best practice to store sensitive keys (like your Turnstile keys) in an environment file (e.g., `.env`). Here's how to do it:

1.  Create a `.env` file in the root directory of your Django project if you don't have it already.

2.  Add your **Turnstile site key** and **secret key** to the `.env` file like this:

    plaintext

    Copy code

    `TURNSTILE_SITE_KEY="your-cloudflare-site-key-here"
    TURNSTILE_SECRET_KEY="your-cloudflare-secret-key-here"`

3.  Use the `django-environ` package (or any other method you prefer) to load these values into your Django settings. Install `django-environ` if you haven't already:

    bash

    Copy code

    `pip install django-environ`

4.  In `settings.py`, load the environment variables:

    python

    Copy code

    `import environ
    env = environ.Env()
    environ.Env.read_env()  # This reads the values from the .env file

    CF_TURNSTILE_SITE_KEY = env('TURNSTILE_SITE_KEY')
    CF_TURNSTILE_SECRET_KEY = env('TURNSTILE_SECRET_KEY')`

This will securely store the keys for later use in your Django application.

### 4\. **Add the Turnstile Widget to the HTML Forms**

In the **Login** and **Registration** forms, you need to add the Turnstile CAPTCHA widget. You can include the widget in the form and display it using the **site key** you generated from Cloudflare.

Update the `login.html` and `register.html` templates to include the CAPTCHA widget:

**For `login.html`:**

html

Copy code

`{% extends 'shared/base.html' %}
{% load static %}

{% block head %}
    <link rel="stylesheet" href="{% static 'accounts/css/login.css' %}">
    <script src="https://challenges.cloudflare.com/turnstile/v0/api.js" async defer></script>
{% endblock head %}

{% block body %}
<div class="login-container">
    <h2 class="login-title">Login</h2>
    <form method="post" class="login-form">
        {% csrf_token %}
        {{ form.as_p }}
        <input type="hidden" name="next" value="{{ next }}">
        <!-- Turnstile Widget -->
        <div class="cf-turnstile"
             data-sitekey="{{ CF_TURNSTILE_SITE_KEY }}"
             data-theme="dark"
             data-size="normal"></div>

        <button type="submit">Login</button>
    </form>

    {% if messages %}
    <ul class="login-messages">
        {% for message in messages %}
            <li>{{ message }}</li>
        {% endfor %}
    </ul>
    {% endif %}
</div>
{% endblock body %}`

**For `register.html`:**

html

Copy code

`{% extends 'shared/base.html' %}
{% load static %}

{% block head %}
    <link rel="stylesheet" href="{% static 'accounts/css/register.css' %}">
    <script src="https://challenges.cloudflare.com/turnstile/v0/api.js" async defer></script>
{% endblock head %}

{% block body %}
<div class="register-container">
    <h2>Register</h2>
    <form method="post" class="register-form">
        {% csrf_token %}
        {{ form.as_p }}
        <!-- Turnstile Widget -->
        <div class="cf-turnstile"
             data-sitekey="{{ CF_TURNSTILE_SITE_KEY }}"
             data-theme="dark"
             data-size="normal"></div>
        <button type="submit">Register</button>
    </form>

    {% if messages %}
    <ul class="messages">
        {% for message in messages %}
            <li>{{ message }}</li>
        {% endfor %}
    </ul>
    {% endif %}
</div>
{% endblock body %}`

**Explanation**:

-   The `<script>` tag loads the Turnstile JavaScript.
-   The `<div class="cf-turnstile">` is where the CAPTCHA widget appears. The **data-sitekey** is dynamically injected into this div from your Django settings.

### 5\. **Handle CAPTCHA Verification in Views**

Now, in the backend (i.e., Django views), you need to verify the CAPTCHA response when the user submits the form. This is done by sending a request to the **Cloudflare Turnstile verification API** with the **secret key** and the CAPTCHA response.

Add the following method to your `views.py` to handle CAPTCHA verification:

python

Copy code

`import requests
from django.conf import settings

def verify_captcha(self, captcha_response):
    """
    Verifies the Turnstile CAPTCHA response using Cloudflare's API.
    """
    if not captcha_response:
        return False

    secret_key = settings.CF_TURNSTILE_SECRET_KEY
    url = "https://challenges.cloudflare.com/turnstile/v0/siteverify"
    data = {
        'secret': secret_key,
        'response': captcha_response,
        'remoteip': self.get_client_ip()  # Optional but recommended to include the user's IP
    }

    try:
        response = requests.post(url, data=data)
        result = response.json()
        return result.get('success', False)
    except requests.exceptions.RequestException:
        return False`

**Explanation**:

-   `verify_captcha` sends a POST request to the Turnstile verification endpoint.
-   If the response from Turnstile is successful, it returns `True`; otherwise, it returns `False`.

In the `Register` and `Login` views, you'll need to call this method to validate the CAPTCHA response:

python

Copy code

`captcha_response = request.POST.get('cf-turnstile-response')
if not self.verify_captcha(captcha_response):
    messages.error(request, "CAPTCHA verification failed. Please try again.")
    return render(request, 'accounts/login.html', {'form': form, 'next': next_url})`

This ensures that the form will not be processed unless the CAPTCHA is successfully solved.

### 6\. **Integrate CAPTCHA with Login and Registration Views**

Finally, integrate the CAPTCHA verification in the **Login** and **Registration** views.

Here's how you can do this:

**For the `Register` view**:

python

Copy code

`class Register(View):
    def post(self, request):
        # Verify Turnstile CAPTCHA
        captcha_response = request.POST.get('cf-turnstile-response')
        if not self.verify_captcha(captcha_response):
            messages.error(request, "CAPTCHA verification failed. Please try again.")
            return render(request, 'accounts/register.html', {'form': RegistrationForm(request.POST)})

        form = RegistrationForm(request.POST)
        if form.is_valid():
            user = form.save(commit=False)
            user.set_password(form.cleaned_data['password'])
            user.save()
            messages.success(request, "Registration successful. You can now log in.")
            return redirect('accounts:login')
        else:
            messages.error(request, "There was an error with your registration.")
            return render(request, 'accounts/register.html', {'form': form})`

**For the `Login` view**:

python

Copy code

`class Login(View):
    def post(self, request):
        # Get the 'next' URL parameter
        next_url = request.POST.get('next', 'main:index')

        # Verify Turnstile CAPTCHA
        captcha_response = request.POST.get('cf-turnstile-response')
        if not self.verify_captcha(captcha_response):
            messages.error(request, "CAPTCHA verification failed. Please try again.")
            return render(request, 'accounts/login.html', {'form': AuthenticationForm(data=request.POST), 'next': next_url})

        form = AuthenticationForm(data=request.POST)
        if form.is_valid():
            # Successful login
            login(request, form.get_user())
            return redirect(next_url)
        else:
            messages.error(request, "Invalid username or password.")
            return render(request, 'accounts/login.html', {'form': form, 'next': next_url})`

### 7\. **Testing in Development**

In development, using localhost, you might run into issues where the CAPTCHA doesn't load due to domain restrictions. Here's how to troubleshoot:

1.  **Check your Cloudflare settings** and make sure that `localhost` is added to the allowed domains for your site key.
2.  Use a **tunneling service** like `ngrok` to expose your local server to the internet if needed.

bash

Copy code

`ngrok http 8000  # if your Django server runs on port 8000`

After that, you will have a publicly accessible URL (e.g., `http://12345678.ngrok.io`) which you can configure in Cloudflare to accept the CAPTCHA responses.

* * * * *

### Final Thoughts:

Once you follow all these steps, you should have a working CAPTCHA setup with Cloudflare Turnstile in your Django project for both **registration** and **login** forms. Make sure to test thoroughly on both local and production environments!