
# Weather App

A simple weather application built using Node.js, Express, and EJS to display current weather information for a given location. The app fetches real-time weather data using the Visual Crossing Weather API.

## Features

- **Weather Search**: Users can input a location, and the app will display the temperature, humidity, and weather description.
- **Error Handling**: The app handles errors such as invalid locations or issues with the API request.
- **Dynamic User Interface**: Utilizes EJS for rendering dynamic HTML content, showing weather data or error messages based on user input.
  
## Technologies Used

- **Node.js**: Backend runtime environment.
- **Express.js**: Web framework for handling routes and HTTP requests.
- **EJS (Embedded JavaScript)**: Template engine for rendering dynamic HTML pages.
- **Visual Crossing Weather API**: Provides weather data based on the user's location.
  
## Project Structure

```
WEATHER-APP
│   ├── node_modules/
│   ├── public/              # Static assets like CSS and images.
│   │   ├── css/             # CSS for styling the app.
│   │   └── img/             # Image assets.
│   ├── views/               # EJS templates.
│   │   └── index.ejs        # Main HTML page rendered for the user.
│   ├── .gitignore           # Git ignore file to avoid unnecessary files.
│   ├── app.js               # Main application logic and server routes.
│   ├── package.json         # Dependencies and project metadata.
│   ├── package-lock.json    # Lockfile for npm dependencies.
│   └── utils.js             # Utility functions for fetching weather data.
```

## Installation

1. Clone the repository:
   ```bash
   git clone https://github.com/yourusername/weather-app.git
   ```

2. Navigate to the project directory:
   ```bash
   cd weather-app
   ```

3. Install the required dependencies:
   ```bash
   npm install
   ```

## Running the Application

1. Start the server:
   ```bash
   node app.js
   ```

2. Open your browser and navigate to:
   `http://localhost:4000`

## Usage

1. Enter a location in the search box (e.g., "New York").
2. The app will display the temperature, humidity, and weather description for that location.
3. If the location is invalid, an error message will be shown.

## Weather API

The app uses the **Visual Crossing Weather API** to fetch weather data. You can view more about the API and its features [here](https://www.visualcrossing.com/).

To use this app with your own API key, replace the API key in `utils.js` with your own:
```javascript
const url = `https://weather.visualcrossing.com/VisualCrossingWebServices/rest/services/timeline/${location}?key=YOUR_API_KEY`;
```
## Screenshots

### Initial State
<img src="image.png" alt="Initial State" width="500"/>

### Displaying Search Results
<img src="image-1.png" alt="Initial State" width="500"/>


## Contributing

Feel free to fork this repository and submit a pull request if you want to contribute to this project.

## License

This project is licensed under the MIT License.

## Contact

If you have any questions, feel free to reach out:

- **Email**: [srikarambula.1924@gmail.com](mailto:srikarambula.1924@gmail.com)
- **LinkedIn**: [Srikar Ambula](https://www.linkedin.com/in/srikar-ambula-66a647277/)
- **GitHub**: [Srikar4510](https://github.com/Srikar4510)

