<script>
import axios from "axios";

export default {
  data() {
    return {
      city: "",
      error: "",
      info: null,
      ip: null,
      cityByIp: null
    }
  },
  computed: {
    cityName() {
      return "«" + this.city + "»"
    },
    showTemp() {
      return "Температура: " + this.info.main.temp + "℃"
    },
    showFeelsLike() {
      return "Ощущается как: " + this.info.main.feels_like + "℃"
    },
    showMinTemp() {
      return "Минимальная температура: " + this.info.main.temp_min + "℃"
    },
    showMaxTemp() {
      return "Максимальная температура: " + this.info.main.temp_max + "℃"
    },
  },
  methods: {
    getWeather() {
      if (this.city.trim().length < 2) {
        this.error = "Нужно название, состоящее более, чем из одного символа: "
        return false
      }
      this.error = ""

      axios.get(`https://api.openweathermap.org/data/2.5/weather?q=${this.city}&units=metric&appid=793278a765dd29caf3266f1b1881b74a`)
          .then(res => (this.info = res.data))
    },
    getUserIp() {
      axios.get('https://api.ipify.org/?format=json')
          .then(res => (this.ip = res.ip))
    },
    async getCityByUserIp() {
      try {
        // Получение IP-адреса
        const ipResponse = await axios.get('https://api.ipify.org/?format=json');
        const ip = ipResponse.data.ip;

        // Получение геолокации по IP
        const geoResponse = await axios.get(`https://ipinfo.io/${ip}/geo?token=49da726b4d2934`);
        const city = geoResponse.data.city;

        // Присваивание полученного города
        this.city = city;
      } catch (error) {
        console.error('Ошибка при получении города:', error.message);
      }
    }

  }
}
</script>

<template>
  <div class="wrapper">
      <h1>Погодное приложение</h1>
      <p>Узнать погоду в {{ city === "" ? "вашем городе" : cityName }}</p>
      <input type="text" v-model="city" @keyup.enter="getWeather" placeholder="Введите город">
      <img class="locator-button" src="@/assets/locator.svg" @click="getCityByUserIp()" title="Мое местоположение"
           alt="Мое местоположение">

      <button v-if="city !== ''" @click="getWeather()">Получить погоду</button>
      <button disabled v-else>Введите название города</button>
      <p class="error">{{ error }}</p>

      <div v-if="info != null">
        <p>{{ showTemp }}</p>
        <p>{{ showFeelsLike }}</p>
        <p>{{ showMinTemp }}</p>
        <p>{{ showMaxTemp }}</p>
      </div>
    </div>
</template>

<style scoped>
.wrapper {
  width: 900px;
  height: 500px;
  border-radius: 50px;
  padding: 20px;
  background: #1f0f24;
  text-align: center;
  color: #fff;
}

.wrapper h1 {
  margin-top: 50px;
}

.wrapper p {
  margin-top: 20px;
}

.wrapper input {
  margin-top: 30px;
  background: transparent;
  border: 0;
  border-bottom: 2px solid #110813;
  color: #fcfcfc;
  font-size: 14px;
  padding: 5px 8px;
  outline: none;
}

.wrapper input:focus {
  border-bottom-color: #6e2d7d;
}

.wrapper input:hover {
  border-bottom-color: #6e2d7d;
}

.wrapper button {
  background: #e3bc4b;
  color: #fff;
  border-radius: 10px;
  border: 2px solid #b99935;
  padding: 10px 15px;
  margin-left: 20px;
  cursor: pointer;
  transition: transform 500ms ease;
}

.wrapper button:disabled {
  border-bottom-color: #6e2d7d;
  cursor: not-allowed;
}

.wrapper button:hover {
  transform: scale(1.1) translateY(-5px);
}

.error {
  color: #d03939;
}

.locator-button {
  width: 35px;
  margin-left: 10px;
  cursor: pointer;
  justify-content: center;
  transition: transform 500ms ease;
}

.locator-button:hover {
  transform: scale(1.1) translateY(-5px);
}

</style>
