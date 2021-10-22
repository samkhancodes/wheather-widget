<template>
  <div>
    <main
      :class="
        weather && weather.main && weather.main.temp > 23 ? 'warm' : 'cold'
      "
    >
      <div class="search-box">
        <input
          type="text"
          class="search-bar"
          placeholder="Please Enter Your Location"
          v-model="query"
          @keypress="fetchWeather"
        />
      </div>

      <div class="weather-app" v-if="weather && weather.main">
        <div class="location-box">
          <div class="location">
            {{ weather.name }}, {{ weather.sys.country }}
          </div>
          <div class="date">{{ dateBuilder() }}</div>
        </div>

        <div class="weather-box">
          <div class="temp">
            {{ Math.round(weather.main.temp) }}ºc
            <img
              v-bind:src="
                'http://openweathermap.org/img/w/' +
                weather.weather[0].icon +
                '.png'
              "
            />
            <div class="feels">
              {{ weather.weather[0].main }} feels like
              {{ weather.main.feels_like }}
            </div>
          </div>
        </div>
      </div>
      <div class="extra" v-if="weather && weather.main">
        <div info-box>
          <p>Minimum: {{ Math.round(weather.main.temp_min) }}º</p>
          <p>Maximum: {{ Math.round(weather.main.temp_max) }}º</p>
          <p>
            Thermal Sensation of Current location:
            {{ Math.round(weather.main.feels_like) }}º
          </p>
          <p>Humidity: {{ weather.main.humidity }}%</p>
        </div>
      </div>

      <div class="error-box" v-if="notFound">
        <div info-box>
          <p>Above city or country not found, please try again.</p>
        </div>
      </div>
    </main>
  </div>
</template>

<script>
export default {
  name: "weather-widget",
  data() {
    return {
      api_key: "1e98200976dfc0825963c51093ac8a88", // should be in .env but just put it here for testing purpose
      url_base: "https://api.openweathermap.org/data/2.5/",
      query: "",
      weather: {},
      notFound: false,
    };
  },
  methods: {
    fetchWeather(e) {
      if (e.key == "Enter") {
        fetch(
          `${this.url_base}weather?q=${this.query}&units=metric&appid=${this.api_key}&lang=pt_br`
        )
          .then((res) => {
            // set errror if nothings found from api
            if (res.status === 404) {
              this.notFound = true;
              return;
            }
            return res.json();
          })
          .then(this.setResults);
      }
    },
    onChange() {
      this.notFound = false;
    },
    setResults(results) {
      this.weather = results;
      this.notFound = results ? false : true;
    },
    dateBuilder() {
      let d = new Date();
      let months = [
        "January",
        "February",
        "March",
        "April",
        "May",
        "June",
        "July",
        "August",
        "September",
        "October",
        "November",
        "December",
      ];
      let days = [
        "Monday",
        "Tuesday",
        "Wednesday",
        "Thursday",
        "Friday",
        "Saturday",
        "Sunday",
      ];
      let day = days[d.getDay()];
      let date = d.getDate();
      let month = months[d.getMonth()];
      let year = d.getFullYear();
      return `${day} ${date} ${month} ${year}`;
    },
  },
};
</script>

<style>
* {
  margin: 0;
  padding: 0;
  box-sizing: border-box;
}

body {
  font-family: "montserrat", sans-serif;
}

.cold {
  background-image: url("../assets/cold-bg.jpg");
  background-size: cover;
  background-position: bottom;
  transition: 0.4s;
}

.warm {
  background-image: url("../assets/warm-bg.jpg");
}

main {
  display: flex;
  align-items: center;
  flex-direction: column;
  height: 100vh;
  padding: 25px;
  background-image: linear-gradient(
    to bottom,
    rgba(0, 0, 0, 0.25),
    rgba(0, 0, 0, 0.75)
  );
}

.search-box {
  margin-bottom: 30px;
  padding: 15px;
}

.search-bar {
  display: block;
  width: 100%;
  padding: 15px;
  color: #313131;
  font-size: 20px;

  appearance: none;
  border: none;
  outline: none;
  background: none;

  box-shadow: 0px 0px 8px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.5);
  border-radius: 0px 16px 0px 16px;
  transition: 0.4s;
}

.search-box .search-bar:focus {
  box-shadow: 0px 0px 16px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.75);
  border-radius: 16px 0px 16px 0px;
}

.location-box .location {
  color: #fff;
  font-size: 32px;
  font-weight: 500;
  text-align: center;
  text-shadow: 1px 3px rgba(0, 0, 0, 0.25);
}

.location-box,
.date {
  color: #fff;
  font-size: 20px;
  font-weight: 300;
  font-style: italic;
  text-align: center;
}

.weather-box {
  text-align: center;
}

.weather-box .temp {
  display: inline-block;
  padding: 10px 25px;
  color: #fff;
  font-size: 50px;
  width: 320px;
  font-weight: 900;

  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  background-color: rgba(255, 255, 255, 0.25);
  border-radius: 16px;
  margin: 30px 0px;

  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.weather-box .feels {
  font-size: 13px;
  text-shadow: none;
}

.weather-box,
.weather-box {
  color: #fff;
  font-size: 20px;
  font-weight: 700;
  font-style: italic;
  text-shadow: 3px 6px rgba(0, 0, 0, 0.25);
}
.error-box {
  text-align: center;
  padding: 10px;
  background-color: rgba(221, 28, 28, 0.35);
  border-radius: 4px;
  color: #fff;
  width: 280px;
  font-size: 11px;
  font-weight: bold;
}
.extra {
  text-align: center;
  margin-top: 10px;
  padding: 10px;
  background-color: rgba(255, 255, 255, 0.35);
  box-shadow: 3px 6px rgba(0, 0, 0, 0.25);
  border-radius: 16px;
  color: #fff;
  width: 280px;
}
.extra p {
  font-size: 13px;
  font-style: italic;
}
</style>
