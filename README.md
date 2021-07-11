<h1 style="font-size: 32px;">Playing Around (Django)</h1>
              <p>Trying Python’s Django framework to create dynamic applications that generates HTML and CSS.</p>
                <div class="case_content">
                  <h1>Description</h1>
                  <hr>
                  <p>
                    The goal is to practice not only the framework but databases and SQL skills. So I've created an Airline app which we can add new airports and flights information, and users can log in/out to check their booking details - all saved into a database.
                    <br><br>
                    Django projects are split into one or more applications. 
                    <br><br>
                    <b>Basic files and directories:</b>
                    <br><br>
                    Project:
                    <ul>
                      <li><code>manage.py</code> is what we use to execute commands on our terminal;</li>
                      <li><code>settings.py</code> contains some important configuration settings for our new project;</li>
                      <li><code>urls.py</code>contains directions for where users should be routed after navigating to a certain URL.</li>
                    </ul>
                    App:
                    <ul> 
                      <li><code>views.py.</code>contains a number of different views, and we can think of a view as one page the user might like to see. To create the view, I write a function that takes in a <code>request</code> rendering layouts or returning <code>HttpResponses</code>;</li>
                      <li><code>templates</code> directory allow us to write HTML and CSS in separate files and render those files using Django. In this context, we can provide information that we would like to have available within our HTML files. Django’s templating language gives us a way to eliminate poor design: the template inheritance. This allows us to create and extend a <code>layout.html</code> file that will contain the general structure of our page and the <code>{% block body %}</code> we want to render.</li>
                    </ul>
                    <br>
                    <b>Styling</b>
                    <br><br>
                    <p>If we want to add a CSS static file because it doesn’t change, we’ll first create a folder called <code>static</code>, then create the airline apps folders within that, and then a <code>styles.css</code> file within that. Now, to include this styling in our HTML file, we add the line <code>{% load static %}</code> to the top of our HTML template, which signals to Django that we wish to have access to the files in our static folder.</p>
                    <br>
                    <b>SQL and database</b>
                    <br><br>
                    <p>Now, if we run <code>sqlite3 flights.sql</code> in the terminal, we’ll be brought to a SQLite prompt where we can run SQL commands:</p>
                    <br>
                    <code>

                    # Entering into the SQLite Prompt
                    (base) % sqlite3 flights.sql

                    # Creating a new Table
                    sqlite> CREATE TABLE flights(
                      ...>     id INTEGER PRIMARY KEY AUTOINCREMENT,
                      ...>     origin TEXT NOT NULL,
                      ...>     destination TEXT NOT NULL,
                      ...>     duration INTEGER NOT NULL
                      ...> );

                    # Listing all current tables (Just flights for now)
                    sqlite> .tables
                    flights

                    # Querying for everything within flights (Which is now empty)
                    sqlite> SELECT * FROM flights;

                    # Adding one flight
                    sqlite> INSERT INTO flights
                      ...>     (origin, destination, duration)
                      ...>     VALUES ("New York", "London", 415);

                    # Checking for new information, which we can now see
                    sqlite> SELECT * FROM flights;
                    1|New York|London|415

                    # Adding some more flights
                    sqlite> INSERT INTO flights (origin, destination, duration) VALUES ("Shanghai", "Paris", 760);
                    sqlite> INSERT INTO flights (origin, destination, duration) VALUES ("Istanbul", "Tokyo", 700);
                    sqlite> INSERT INTO flights (origin, destination, duration) VALUES ("New York", "Paris", 435);
                    sqlite> INSERT INTO flights (origin, destination, duration) VALUES ("Moscow", "Paris", 245);
                    sqlite> INSERT INTO flights (origin, destination, duration) VALUES ("Lima", "New York", 455);

                    # Querying this new information
                    sqlite> SELECT * FROM flights;
                    1|New York|London|415
                    2|Shanghai|Paris|760
                    3|Istanbul|Tokyo|700
                    4|New York|Paris|435
                    5|Moscow|Paris|245
                    6|Lima|New York|455

                    # Changing the settings to make output more readable
                    sqlite> .mode columns
                    sqlite> .headers yes

                    # Querying all information again
                    sqlite> SELECT * FROM flights;
                    id         origin     destination duration
                    ---------- ---------- ----------- ----------
                    1          New York   London      415
                    2          Shanghai   Paris       760
                    3          Istanbul   Tokyo       700
                    4          New York   Paris       435
                    5          Moscow     Paris       245
                    6          Lima       New York    455

                    # Searching for just those flights originating in New York
                    sqlite> SELECT * FROM flights WHERE origin = "New York";
                    id         origin     destination duration
                    ---------- ---------- ----------- ----------
                    1          New York   London      415
                    4          New York   Paris       435
                    
</code>
                    
                    
                    <b>Project and apps</b>
                    <br><br>
                    <p><b>Django Models</b> are a level of abstraction on top of SQL that allow us to work with databases using Python classes and objects rather than direct SQL queries.
                    <br>
                    Now, rather than creating actual paths and getting started on <code>views.py</code>, we’ll create some models in the <code>models.py</code> file. In this file, we’ll outline what data we want to store in our application and create a new model that extends Django’s model class. Then, Django will determine the SQL syntax necessary to store information on each of our models.</p>
                    <p><code>
                    class Airport(models.Model):<br>
                    &nbsp;&nbsp;&nbsp;code = models.CharField(max_length=3)<br>
                    &nbsp;&nbsp;&nbsp;city = models.CharField(max_length=64)<br><br>

                    &nbsp;&nbsp;&nbsp;def __str__(self):<br>
                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return f"{self.city} ({self.code})"<br><br>

                    class Flight(models.Model):<br>
                    &nbsp;&nbsp;&nbsp;origin = models.ForeignKey(Airport, on_delete=models.CASCADE, related_name="departures")<br>
                    &nbsp;&nbsp;&nbsp;destination = models.ForeignKey(Airport, on_delete=models.CASCADE, related_name="arrivals")<br>
                    &nbsp;&nbsp;&nbsp;duration = models.IntegerField()<br><br>

                    &nbsp;&nbsp;&nbsp;def __str__(self):<br>
                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return f"{self.id}: {self.origin} to {self.destination}"
                    </code></p>
                    <p>To create a database from our models, we navigate to the main directory of our project and run the command.</p>
                    <p><code>
                    python manage.py makemigrations<br>
                    python manage.py migrate
                    </code></p>
                    <p>We can enter Django’s shell to run Python commands within our project.</p>
                    <p><code>
                    python manage.py shell<br><br>

                    # Import all models<br>
                    In [1]: from flights.models import *<br><br>

                    # Create some new airports<br>
                    In [2]: jfk = Airport(code="JFK", city="New York")<br>
                    In [4]: lhr = Airport(code="LHR", city="London")<br>
                    In [6]: cdg = Airport(code="CDG", city="Paris")<br>
                    In [9]: nrt = Airport(code="NRT", city="Tokyo")<br><br>

                    # Save the airports to the database<br>
                    In [3]: jfk.save()<br>
                    In [5]: lhr.save()<br>
                    In [8]: cdg.save()<br>
                    In [10]: nrt.save()<br><br>

                    # Add a flight and save it to the database<br>
                    f = Flight(origin=jfk, destination=lhr, duration=414)<br>
                    f.save()<br><br>

                    # Display some info about the flight<br>
                    In [14]: f<br>
                    Out[14]: &lt;Flight: 1: New York (JFK) to London (LHR)&gt;<br>
                    In [15]: f.origin<br>
                    Out[15]: &lt;Airport: New York (JFK)&gt;<br><br>

                    # Using the related name to query by airport of arrival:<br>
                    In [17]: lhr.arrivals.all()<br>
                    Out[17]: &lt;QuerySet [&lt;Flight: 1: New York (JFK) to London (LHR)&gt;]&gt;<br>
                    </code></p>
                    <br>
                    <b>Booking </b>
                    <br><br>
                    <p>Now, let’s work on giving visitors to our site the ability to book a flight. We’ll do this by adding a booking route in <code>urls.py</code>:</p>
                    <p><code>path("&lt;int:flight_id&gt;/book", views.book, name="book")</code></p>
                    <p>Now, we’ll add a book function to <code>views.py</code> that adds a passenger to a flight:</p>
                    <p><code>
                    def book(request, flight_id):<br><br>

                    &nbsp;&nbsp;&nbsp;# For a post request, add a new flight<br>
                    &nbsp;&nbsp;&nbsp;if request.method == "POST":<br><br>

                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# Accessing the flight<br>
                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;flight = Flight.objects.get(pk=flight_id)<br><br>

                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# Finding the passenger id from the submitted form data<br>
                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;passenger_id = int(request.POST["passenger"])<br><br>

                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# Finding the passenger based on the id<br>
                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;passenger = Passenger.objects.get(pk=passenger_id)<br><br>

                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# Add passenger to the flight<br>
                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;passenger.flights.add(flight)<br><br>

                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# Redirect user to flight page<br>
                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return HttpResponseRedirect(reverse("flight", args=(flight.id,)))<br><br><br>

                    def flight(request, flight_id):<br><br>
                    &nbsp;&nbsp;&nbsp;flight = Flight.objects.get(id=flight_id)<br>
                    &nbsp;&nbsp;&nbsp;passengers = flight.passengers.all()<br>
                    &nbsp;&nbsp;&nbsp;non_passengers = Passenger.objects.exclude(flights=flight).all()<br>
                    &nbsp;&nbsp;&nbsp;return render(request, "flights/flight.html", {<br>
                      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"flight": flight,<br>
                      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"passengers": passengers,<br>
                      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"non_passengers": non_passengers<br>
                        })
                    </code></p>
                    <br>
                    <b>Django Admin</b>
                    <br><br>
                    <p>Django comes with a default admin interface that allows us to do the operations in the database above more easily than through command lines or accessing <code>http://127.0.0.1:8000/admin</code>. To begin using this tool, we must first create an administrative user, and we'll have something like this:</p>
                    <img src="/media/django_admin.png"> 
                    <br><br>
                    <b>Users</b>
                    <br><br>
                    <p>>The last thing I've done in this project was creating a form of authentication, allowing users to log in and out of a website.</p>
                    <p><code>
                    {% extends "users/layout.html" %}<br><br>

                    {% block body %}<br>
                    &nbsp;&nbsp;&nbsp;{% if message %}<br>
                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;div>{{ message }}&lt;/div><br>
                    &nbsp;&nbsp;&nbsp;{% endif %}<br><br>

                    &nbsp;&nbsp;&nbsp;&lt;form action="{% url 'login' %}" method="post"><br>
                      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;{% csrf_token %}<br>
                      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;input type="text", name="username", placeholder="Username"><br>
                      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;input type="password", name="password", placeholder="Password"><br>
                      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&lt;input type="submit", value="Login"><br>
                    &nbsp;&nbsp;&nbsp;&lt;/form><br>
                    {% endblock %}
                    </code></p>
                        <form>
                            <input type="text", name="username", placeholder="Username">
                            <input type="password", name="password", placeholder="Password">
                            <input type="submit", value="Login">
                        </form>
                    <br>
                    <p>Now, in <code>views.py</code>, we’ll add three functions:</p>
                    <p><code>
                    def index(request):<br>
                    &nbsp;&nbsp;&nbsp;# If no user is signed in, return to login page:<br>
                    &nbsp;&nbsp;&nbsp;if not request.user.is_authenticated:<br>
                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return HttpResponseRedirect(reverse("login"))<br>
                    &nbsp;&nbsp;&nbsp;return render(request, "users/user.html")<br><br>

                    def login_view(request):<br>
                    &nbsp;&nbsp;&nbsp;if request.method == "POST":<br>
                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# Accessing username and password from form data<br>
                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;username = request.POST["username"]<br>
                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;password = request.POST["password"]<br><br>

                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# Check if username and password are correct, returning User object if so<br>
                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;user = authenticate(request, username=username, password=password)<br><br>

                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# If user object is returned, log in and route to index page:<br>
                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;if user:<br>
                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;login(request, user)<br>
                    &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return HttpResponseRedirect(reverse("index"))<br>
                                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;# Otherwise, return login page again with new context<br>
                                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;else:<br>
                                &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;return render(request, "users/login.html", {<br>
                                  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"message": "Invalid Credentials"<br>
                                  &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;})<br>
                                &nbsp;&nbsp;&nbsp;return render(request, "users/login.html")<br><br>

                    def logout_view(request):<br>
                    &nbsp;&nbsp;&nbsp;logout(request)<br>
                    &nbsp;&nbsp;&nbsp;return render(request, "users/login.html", {<br>
                      &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;"message": "Logged Out"<br>
                      &nbsp;&nbsp;&nbsp;})
                    </code></p>
                    And that's it.
                  </p>