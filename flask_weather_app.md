# Flask Weather App

```python
from flask import Flask, render_template, request

# import json to load JSON data to a python dictionary
import json

# urllib request to make a request to the API
import urllib.request

app = Flask(__name__)

@app.route('/', methods=['POST', 'GET'])
def weather():
    if request.method == 'POST':
        city = request.form['city']
    else:
        city = 'Chennai'

    # your API key will come here
    api = "41caecdf0109fb5d698d60c6875515ef"

    # source contains JSON data from API
    source = urllib.request.urlopen(
        'https://api.openweathermap.org/data/2.5/weather?q='
        + city + '&appid=' + api
    ).read()

    # converting JSON data to a dictionary
    list_of_data = json.loads(source)

    # data for variable list_of_data
    data = {
        "country_code": str(list_of_data['sys']['country']),
        "coordinate": str(list_of_data['coord']['lat']) + ', ' + str(list_of_data['coord']['lon']),
        "temp": str(list_of_data['main']['temp']) + 'K',
        "pressure": str(list_of_data['main']['pressure']),
        "humidity": str(list_of_data['main']['humidity']),
    }

    print(data)
    return render_template('index.html', data=data)


if __name__ == '__main__':
    app.run(debug=True, port=5000)
```

## Description

This Flask application fetches weather information for a city using the OpenWeatherMap API and displays the results through an HTML template named `index.html`.

### Features

- Uses **Flask** to create a web application.
- Accepts a city name through a POST request.
- Uses **Chennai** as the default city for GET requests.
- Fetches weather data from the **OpenWeatherMap API**.
- Converts the JSON response into a Python dictionary.
- Displays:
  - Country code
  - Coordinates
  - Temperature in Kelvin
  - Atmospheric pressure
  - Humidity

## Important

The API key is currently written directly in the source code. For a real project, store the API key in an environment variable instead of exposing it in your code.
