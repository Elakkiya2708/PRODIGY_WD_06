const apiKey = '218226592987cb885d5b4196c7841b25'; // Use your API key here

const getWeatherBtn = document.getElementById('getWeatherBtn');
const cityInput = document.getElementById('cityInput');
const weatherResult = document.getElementById('weatherResult');
const bgVideo = document.getElementById('bgVideo');
const bgAudio = document.getElementById('bgAudio');

getWeatherBtn.addEventListener('click', () => {
  const cityName = cityInput.value.trim();
  if (cityName === '') {
    weatherResult.innerHTML = '<p>Please enter a city name.</p>';
    weatherResult.classList.remove('hidden');
    return;
  }
  fetchWeather(cityName);
});

async function fetchWeather(city) {
  weatherResult.innerHTML = '<p>Loading...</p>';
  weatherResult.classList.remove('hidden');

  try {
    const response = await fetch(
      `https://api.openweathermap.org/data/2.5/weather?q=${city}&appid=${apiKey}&units=metric`
    );

    if (!response.ok) throw new Error('City not found');

    const data = await response.json();
    const weatherMain = data.weather[0].main.toLowerCase();
    const description = capitalize(data.weather[0].description);
    const iconCode = data.weather[0].icon;
    const iconUrl = `https://openweathermap.org/img/wn/${iconCode}@2x.png`;

    weatherResult.innerHTML = `
      <h3>${data.name}, ${data.sys.country}</h3>
      <img src="${iconUrl}" alt="${description}" />
      <p><strong>Temperature:</strong> ${data.main.temp} °C</p>
      <p><strong>Weather:</strong> ${description}</p>
      <p><strong>Humidity:</strong> ${data.main.humidity}%</p>
      <p><strong>Wind Speed:</strong> ${data.wind.speed} m/s</p>
    `;

    updateMedia(weatherMain);

  } catch (error) {
    weatherResult.innerHTML = '<p>City not found. Try again.</p>';
    updateMedia('default');
  }
}

function capitalize(text) {
  return text.charAt(0).toUpperCase() + text.slice(1);
}

function updateMedia(weatherMain) {
  let videoFile = 'default.mp4';
  let audioFile = 'default.mp3';

  switch (weatherMain) {
    case 'clear':
      videoFile = 'clear.mp4';
      audioFile = 'clear.mp3';
      break;
    case 'rain':
      videoFile = 'rain.mp4';
      audioFile = 'rain.mp3';
      break;
    case 'thunderstorm':
      videoFile = 'thunderstorm.mp4';
      audioFile = 'thunderstorm.mp3';
      break;
    case 'clouds':
      videoFile = 'clouds.mp4';
      audioFile = 'clouds.mp3';
      break;
    case 'snow':
      videoFile = 'snow.mp4';
      audioFile = 'snow.mp3';
      break;
    case 'mist':
    case 'fog':
      videoFile = 'mist.mp4';
      audioFile = 'mist.mp3';
      break;
  }

  bgVideo.src = `media/${videoFile}`;
  bgAudio.src = `media/${audioFile}`;
  bgAudio.play();
}
