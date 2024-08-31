const express = require('express');
const axios = require('axios');
const app = express();
const port = 3000;


const WEATHERSTACK_API_KEY = "73f82613a25d3224d0d73b0336db0bf9";
const WEATHERSTACK_API_URL = 'http://api.weatherstack.com/current';


app.use(express.json());


app.get('/weather', async (req, res) => {
    const { city } = req.query;

    if (!city) {
        return res.status(400).send('City name is required');
    }

    try {
        const response = await axios.get(WEATHERSTACK_API_URL, {
            params: {
                access_key: WEATHERSTACK_API_KEY,
                query: city
            }
        });

        const data = response.data;

        if (data.error) {
            return res.status(400).send(data.error.info);
        }

        const weather = {
            location: data.location.name,
            temperature: data.current.temperature,
            weather_description: data.current.weather_descriptions[0],
            humidity: data.current.humidity,
            wind_speed: data.current.wind_speed,
            observation_time: data.current.observation_time
        };

        res.json(weather);
    } catch (error) {
        res.status(500).send('Error fetching weather data');
    }
});

app.listen(port, () => {
    console.log(`Weather service running at http://localhost:${port}`);
});
