# GAP

GAP

## Recreation Steps

### Setting Up Django & Wagtail

1. Create a folder called "GAP"
2. Inside GAP/ create a new Python virtual environment (i.e. venv should most likely be the name)
3. Activate the virtual environment
4. Pip install Wagtail
5. Create a new Wagtail site by calling the wagtail start command inside the GAP/ folder:
   `wagtail start GAP . `
6. Migrate the default Django and Wagtail models over to the default "db.sqlite3" database. It doesn't exist yet but
   will be create automatically:
   `python manage.py migrate`
7. Create a superuser so you can access CMS and Django admin site:
   `python manage.py createsuperuser`
8. Enter your desired username, email, and password
9. Run the development server:
   `python manage.py runserver`
10. Navigate to "http://localhost:8000" to view the default Wagtail site

Setting Up NPM

1. Install desired Javascript and CSS libraries to the GAP/GAP/static/ folder:
    * Bootstrap - `npm install --prefix=GAP/static --save bootstrap`
    * Angular - `npm install --prefix=GAP/static --save angular`
    * JQuery - `npm install --prefix=GAP/static --save jquery`
    * USWDS - `npm install --prefix=GAP/static --save uswds`

### Adding Third Party Support Libraries

1. Install the Python libraries in your virtual environment
    * `pip install mozilla-django-oidc`
    * `pip install django-import-export`
    * `pip install psycopg2`
    * `pip install django-environ`
2. If you haven't yet install the requirments.txt that comes with Wagtail:
    * `pip install -r requirements.txt`
3. Save your requirements.txt:
   `pip freeze > requirements.txt` - This should have all the libraries and any others you want now

#### Alternatively install deps with pyenv and poetry
1. Install pyenv and desired version of python
   ```
   # MacOS instructions
   brew install pyenv poetry
   pyenv init # add snippet to your *shrc and restart shell
   pyenv install 3.10.4 # or whatever version you want
   pyenv local 3.10.4 # now you're using python 3.10.4
   ```
2. Use poetry's `pyproject.toml` (and/or `poetry.lock`) to install deps and create a virtualenv like wagtail-template-ziKsGQp0-py3.10
   ```
   poetry install
   poetry shell # starts up the virtualenv
   ```
   Add more deps with
   ```
   poetry add lib # same as pip install, all deps stored in the pyproject.toml
   poetry show    # same as pip freeze
   ```
3. In the poetry shell, run python commands as desired, i.e. `python manage runserver` or `pip freeze` 

## Creating Custom UI/UX

Wagtail comes with a default "home" application when we created the Wagtail project. Inside that "home" folder you will
find the following files and directories:

* home/migrations
* home/static
* home/templates
* home/__init__.py
* home/models.py

These are the building blocks for the entire site. The migrations' folder contains the first and second migrations
needed to create the home page. Don't delete these ever. You can delete any migrations that come after, but you want
these to remain untouched. The static folder contains custom HTML and CSS that you can delete if you don't need it. The
templates folder contains the template for the home page. This template is going to be the "body" of the home page.
The __init__.py file makes the folder a module. The models.py file holds the model for the home page. BLUF, the model is
an abstract model of the Wagtail page model. It inherits a bunch of cool features but most importantly has the home
route (i.e. the URL) and the home page template (i.e. home/templates/home_page.html) baked into because Wagtail is
smart. The default HomePage model has no extra classes, fields, or functions. It's simply passing a basic Page model to
to Django. You can customize this later.

1. Delete the home/static folder if you want
2. Delete the welcome_page.html if you want. It's simply there to show that you can insert templates into templates.
3. Open /home/templates/home_page.html and delete all the content inside but keep the file itself.

Next we are going to set up the header and footer of the website. For our use case, we want to use the USWDS standard
header and footer classes they offer. But first we have to add that CSS and JS to our base template. In Django,
JavaScript, CSS, images, and documents are considered "static" or "media" data. Django and wagtail will not serve this
data in production because of security but to summarize we have to collect all the static data in the project and put it
in one place. We do this by using the "collectstatic" function. There are variables in the settings file that tell what
and where these datas should go when the function is called but that's too much for now:

1. `python manage.py collectstatic` - this will create a "staticfiles" directory at the root of your project. Inside it
   will have all the custom and third party static data we need in on place. In production you will most likely have to
   set up an NGINX server but it's been done before so no worries.

Once the static files have been collected, open the GAP/templates/base.html file. This file is going to be used to have
our headers and footers but also the navigation bar and any content you want to be seen across every page on the site.

2. Add the NPM modules CSS you installed to the top of the file. There is a section tagged "(% Global stylesheets %}" below
   that and above the `<link rel="stylesheet" type="text/css" href="{% static 'css/GAP.css' %}">` add the following:
    * `<link rel="stylesheet" type="text/css" href="{% static 'node_modules/bootstrap/dist/css/bootstrap.css' %}">`
    * `<link rel="stylesheet" type="text/css" href="{% static 'node_modules/uswds/dist/css/uswds.css' %}">`
   
   You can put custom CSS in the css/GAP.css file if you want.

3. Add the NPM modules JS you installed to body of the file.  There is a section tagged "{% Global javascript %}" bleow
   that and above the `<script type="text/javascript" src="{% static 'js/GAP.js' %}"></script>` add the following:
    * `<script type="text/javascript" src="{% static 'node_modules/jquery/dist/jquery.js' %}"><script>`
    * `<script type="text/javascript" src="{% static 'node_modules/angular/angular.js' %}"><script>`
    * `<script type="text/javascript" src="{% static 'node_modules/bootstrap/dist/js/bootstrap.js' %}"><script>`
    * `<script type="text/javascript" src="{% static 'node_modules/uswds/dist/js/uswds.js' %}"><script>`
   
   You can put custom JS in the js/GAP.js file if you want. 

You'll notice some weird syntax like "{% stuff here %}" or "{{ stuff here }}".  These are called tags, and they help us
load Django or Wagtail contexts or static things.  Next we are going to add the header and footer from USWDS to the
base.html file and then add a tag to tell Django that when the base.html file is called to put a block of code in it from
the home page. It'll make more sense soon.  

Once this is set up you can copy over the templates https://designsystem.digital.gov/templates/ for what ever template
you want.  For example, the landing page template was used for the home page. Other components can be used such as cards
and collections for applications and articles. 

## Create Custom Login Page

1. Create a login.html file in home/templates directory
2. Copy and paste over USWDS authentication template from website
3. Change the fields to match Django authentication fields (i.e username and password from email password)
4. Add the Wagtail authentication function to the form:
   * `<form class="usa-form" method="post" action="{% url 'wagtailcore_login' %}">` - This tells the site to call the
   login URL
5. Add the CSRF tag
   * `{% csrf_token %}`
6. Add SSO URL from mozilla-django-oidc
   * `<a class="usa-button usa-button--outline" href="{% url 'oidc_authentication_init' %}">Launch secondary SSO</a>`
   This calls the authentication function from our secondary authentication backend
7. Add the following variables to your settings file:

```
WAGTAIL_FRONTEND_LOGIN_TEMPLATE = 'home/login.html'
LOGIN_URL = '/'
LOGIN_REDIRECT_URL = '/'
LOGOUT_REDIRECT_URL = '/'
CSRF_TRUSTED_ORIGINS = ['http://localhost']
AUTHENTICATION_BACKENDS = (
    'mozilla_django_oidc.auth.OIDCAuthenticationBackend',
    'django.contrib.auth.backends.ModelBackend',
)
OIDC_RP_CLIENT_ID = ''
OIDC_RP_CLIENT_SECRET = ''
OIDC_OP_AUTHORIZATION_ENDPOINT = ''
OIDC_OP_TOKEN_ENDPOINT = ''
OIDC_OP_JWKS_ENDPOINT = ''
OIDC_OP_USER_ENDPOINT = ''
OIDC_VERIFY_SSL = False
OIDC_RP_SCOPES = 'openid'
OIDC_RP_SIGN_ALGO = 'RS256'
```
These variables are for SSO and telling where to redirect users to login, upon login, and upon logout. Also, what our
authentication backends are. The system will use both Django's authentication backend and the SSO authentication backend.

Adding Applications

To create an application, API, or new page we first have to create an new Django application.  We do this by running the following command:
1. `python manage.py startapp application`
2. Adding the application to the INSTALLED_APPS variable in settings file, in our case the name is "applications"

From here you would develop out the applications page, model, and templates.