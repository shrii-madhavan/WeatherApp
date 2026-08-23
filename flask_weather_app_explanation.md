# Flask Weather App — Code Explanation

This is a **Flask weather application**. It takes a city name, asks the OpenWeatherMap API for weather information, converts the response into Python data, and displays it on an HTML page.

## 1. Importing Flask tools

```python
from flask import Flask, render_template, request
```

This imports three things from Flask:

### `Flask`

Used to create your web application.

```python
app = Flask(__name__)
```

### `render_template`

Used to display an HTML file.

```python
return render_template('index.html', data=data)
```

This means:

> Open `index.html` and send the `data` variable to it.

### `request`

Used to get information sent by the user.

For example, when the user types a city into a form and clicks a button, `request` allows Python to access that submitted value.

---

## 2. Importing JSON

```python
import json
```

JSON is a common format used for sending data between websites and applications.

The weather API might return something like:

```json
{
    "name": "Chennai",
    "main": {
        "temp": 300.15,
        "humidity": 70
    }
}
```

Python needs to convert this JSON into something it can easily work with.

That's what `json.loads()` does later.

---

## 3. Importing `urllib`

```python
import urllib.request
```

`urllib.request` allows Python to **make a request to a URL**.

Your program uses it to contact the weather API:

```python
urllib.request.urlopen(...)
```

Think of it as:

> Python → "Hey OpenWeatherMap, give me the weather for Chennai."

---

## 4. Creating the Flask application

```python
app = Flask(__name__)
```

This creates your Flask application.

Remember:

- `Flask` → the Flask application class
- `__name__` → tells Flask where this application is located
- `app` → stores the Flask application

So you can think of it as:

> "Create my web application and call it `app`."

---

## 5. Creating a URL/route

```python
@app.route('/', methods=['POST', 'GET'])
```

This says:

> "When somebody visits `/`, run the function below."

`/` means the **home page**.

For example:

```text
http://127.0.0.1:5000/
```

The `/` represents the home page.

### What is `@app.route`?

This:

```python
@app.route('/')
```

is a **decorator**.

It connects a URL to a Python function.

Immediately below it you have:

```python
def weather():
```

So Flask understands:

```text
User visits /
       ↓
Flask runs weather()
```

---

## 6. What does `methods=['POST','GET']` mean?

```python
@app.route('/', methods=['POST', 'GET'])
```

Your page can receive two types of HTTP requests.

### GET

Usually means:

> "Give me this webpage."

For example, when you open:

```text
http://127.0.0.1:5000/
```

that's a GET request.

### POST

Usually means:

> "Here is some data from the user."

For example, your HTML form might contain:

```html
<form method="POST">
    <input name="city">
    <button type="submit">Search</button>
</form>
```

If the user enters:

```text
Mumbai
```

and submits the form, Flask receives a POST request containing:

```text
city = Mumbai
```

---

## 7. Creating the `weather()` function

```python
def weather():
```

This function contains almost all of your weather application's logic.

The flow is basically:

```text
User opens website
       ↓
weather() runs
       ↓
Get city
       ↓
Ask weather API
       ↓
Receive JSON
       ↓
Convert JSON → Python dictionary
       ↓
Extract weather information
       ↓
Send information to index.html
```

---

## 8. Checking whether the user submitted the form

```python
if request.method == 'POST':
```

Remember that we allowed:

```python
methods=['POST', 'GET']
```

So now we check:

> "Was this request a POST request?"

If yes, the user probably submitted the city form.

---

## 9. Getting the city

```python
city = request.form['city']
```

This gets the value from the HTML form.

Suppose the HTML contains:

```html
<input name="city">
```

and the user types:

```text
Mumbai
```

Then:

```python
request.form['city']
```

gives:

```text
Mumbai
```

So:

```python
city = request.form['city']
```

means:

> "Take the city submitted by the user and store it in `city`."

---

## 10. What happens if it's NOT POST?

```python
else:
    city = 'Chennai'
```

If the request is GET, the program doesn't have a city submitted by a form.

So you give it a default city:

```text
Chennai
```

Therefore:

### First visit

```text
GET request
    ↓
city = "Chennai"
```

### User searches

```text
POST request
    ↓
city = whatever user entered
```

---

## 11. Your API key

```python
api = "YOUR_API_KEY"
```

This is your **OpenWeatherMap API key**.

It identifies/authenticates your application when you make the API request.

### Security warning

API keys should not normally be hard-coded into code that you publish or share publicly.

A better approach is to store the key in an environment variable.

For example:

```python
import os

api = os.environ.get("OPENWEATHER_API_KEY")
```

---

## 12. Requesting weather information

This is the most complicated-looking line:

```python
source = urllib.request.urlopen(
    'https://api.openweathermap.org/data/2.5/weather?q='
    + city +
    '&appid=' + api
).read()
```

Let's simplify what it's building.

Suppose:

```python
city = "Chennai"
```

and:

```python
api = "ABC123"
```

Python creates a URL similar to:

```text
https://api.openweathermap.org/data/2.5/weather?q=Chennai&appid=ABC123
```

Then:

```python
urllib.request.urlopen(...)
```

sends a request to that URL.

The OpenWeatherMap server sends weather information back.

Finally:

```python
.read()
```

reads the response.

So conceptually:

```text
Your Flask app
      |
      | Request weather for Chennai
      ↓
OpenWeatherMap API
      |
      | Weather data
      ↓
Your Flask app
```

---

## 13. The API sends JSON

The API response might look roughly like:

```json
{
    "coord": {
        "lon": 80.27,
        "lat": 13.08
    },
    "sys": {
        "country": "IN"
    },
    "main": {
        "temp": 301.15,
        "pressure": 1008,
        "humidity": 70
    }
}
```

That's JSON.

---

## 14. Convert JSON into a Python dictionary

```python
list_of_data = json.loads(source)
```

`json.loads()` means:

> Convert JSON data into Python data.

So now you can do things like:

```python
list_of_data['main']['humidity']
```

to get:

```text
70
```

### A small naming improvement

The name `list_of_data` is slightly confusing because this isn't actually a Python list. It's a **dictionary**.

A clearer name would be:

```python
weather_data = json.loads(source)
```

---

## 15. Creating your own dictionary

Now you create:

```python
data = {
    "country_code": str(list_of_data['sys']['country']),
    "coordinate": str(list_of_data['coord']['lat']) + ', ' + str(list_of_data['coord']['lon']),
    "temp": str(list_of_data['main']['temp']) + 'K',
    "pressure": str(list_of_data['main']['pressure']),
    "humidity": str(list_of_data['main']['humidity']),
}
```

You're taking only the information you want from the API.

Think of the API response as a huge box:

```text
OpenWeatherMap response
┌─────────────────────────────┐
│ coordinates                 │
│ country                     │
│ temperature                 │
│ pressure                    │
│ humidity                    │
│ wind                        │
│ clouds                      │
│ sunrise                     │
│ sunset                      │
│ etc...                      │
└─────────────────────────────┘
```

You're taking out only:

```text
country
coordinates
temperature
pressure
humidity
```

---

## 16. Getting the country

```python
"country_code": str(list_of_data['sys']['country'])
```

Suppose the API gives:

```python
list_of_data['sys']['country']
```

which is:

```text
IN
```

Then:

```python
str(...)
```

converts it into a string.

So:

```python
"country_code": "IN"
```

---

## 17. Getting coordinates

```python
"coordinate": str(list_of_data['coord']['lat']) + ', ' + str(list_of_data['coord']['lon'])
```

The API might give:

```text
lat = 13.08
lon = 80.27
```

Your code combines them:

```text
13.08, 80.27
```

So:

```python
"coordinate": "13.08, 80.27"
```

---

## 18. Getting temperature

```python
"temp": str(list_of_data['main']['temp']) + 'K'
```

The API's default temperature unit here is **Kelvin**.

Suppose:

```python
list_of_data['main']['temp']
```

returns:

```text
301.15
```

Your code turns it into:

```text
301.15K
```

### Converting Kelvin to Celsius

This code does **not** convert Kelvin to Celsius.

The conversion is:

```text
Celsius = Kelvin - 273.15
```

So:

```text
301.15 K ≈ 28°C
```

---

## 19. Getting pressure

```python
"pressure": str(list_of_data['main']['pressure'])
```

For example:

```text
1008
```

So your dictionary contains:

```python
"pressure": "1008"
```

---

## 20. Getting humidity

```python
"humidity": str(list_of_data['main']['humidity'])
```

For example:

```text
70
```

means:

```text
70% humidity
```

---

## 21. Printing the data

```python
print(data)
```

This prints the dictionary to your terminal.

You might see:

```text
{
    'country_code': 'IN',
    'coordinate': '13.08, 80.27',
    'temp': '301.15K',
    'pressure': '1008',
    'humidity': '70'
}
```

This is useful for debugging.

---

## 22. Sending the data to HTML

```python
return render_template('index.html', data=data)
```

This tells Flask:

> "Show `index.html` and give it my `data` dictionary."

So you have:

```text
Python
   |
   | data
   ↓
index.html
```

Your HTML can then access the values.

For example, using Jinja syntax:

```html
<h1>{{ data.temp }}</h1>
<p>{{ data.humidity }}</p>
<p>{{ data.pressure }}</p>
```

If Python sends:

```python
data = {
    "temp": "301.15K",
    "humidity": "70",
    "pressure": "1008"
}
```

the webpage can display:

```text
301.15K
70
1008
```

---

## 23. The last part

```python
if __name__ == '__main__':
    app.run(debug=True, port=5000)
```

This is another very important Python pattern.

It means:

> "Only start the Flask server if this file is being run directly."

When you run:

```bash
python app.py
```

Python sets:

```python
__name__ = '__main__'
```

Therefore:

```python
app.run(...)
```

gets executed.

---

## 24. `app.run()`

```python
app.run(debug=True, port=5000)
```

starts your Flask web server.

### `port=5000`

Your application will be available at approximately:

```text
http://127.0.0.1:5000/
```

### `debug=True`

Debug mode helps during development.

It can automatically reload the application when you change your code and provides useful error information.

**Do not use `debug=True` in production**, because the debugger can expose sensitive information.

---

# Complete Application Flow

The easiest way to understand your application is this:

```text
                  USER
                   │
                   │ enters "Mumbai"
                   ↓
             ┌─────────────┐
             │   Flask     │
             │    app      │
             └──────┬──────┘
                    │
                    │ city = Mumbai
                    ↓
           ┌──────────────────┐
           │ OpenWeatherMap    │
           │      API          │
           └────────┬─────────┘
                    │
                    │ JSON weather data
                    ↓
             ┌─────────────┐
             │    json     │
             │  → Python   │
             │  dictionary │
             └──────┬──────┘
                    │
                    │ extract temperature,
                    │ humidity, pressure...
                    ↓
             ┌─────────────┐
             │    data     │
             │  dictionary │
             └──────┬──────┘
                    │
                    ↓
             ┌─────────────┐
             │ index.html  │
             └──────┬──────┘
                    │
                    ↓
              WEATHER PAGE
```

## In one sentence

**Your program takes a city from the user → sends that city to OpenWeatherMap → receives weather data → extracts the useful information → sends it to an HTML page → displays the weather.**

## What to learn next

A very useful next step is understanding how:

```python
request.form['city']
```

connects to:

```html
<form method="POST">
    <input name="city">
</form>
```

That connection explains how the city travels from the webpage into your Python/Flask code.
