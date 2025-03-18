# CountriesData App Overview

**Run the App:**:  
https://alkisax.github.io/coutnriesData/

**Source Code:** [GitHub Repository](https://github.com/alkisax/coutnriesData): 
https://github.com/alkisax/coutnriesData

https://github.com/alkisax/FinlandMOOCFullStack/tree/master/part2/countriesdata/src

**Full README.MD:** https://github.com/alkisax/coutnriesData/tree/gh-pages#readme

## Introduction

The CountriesData app was developed as an interactive React-based application that allows users to search and explore detailed information about countries around the world. This tool provides comprehensive country profiles, including demographic data, geographical information, and current weather conditions, all within a responsive and user-friendly interface. This app was created as part of lessons of part 2 Finland mooc Full Stack in january 2025

**Note**: This README was auto-created in large part using generative AI, with careful checking and supervision by the app creator.

## Key Features

### Country Search
Users can search for countries by name, with the app dynamically filtering results as you type.

### Comprehensive Country Profiles
Access detailed information about each country, including official name, capital, population, languages, currencies, and more.

### Weather Integration
View current weather conditions for the selected country through seamless integration with the OpenWeatherMap API.

### Responsive Design
The app features a modern dark theme with responsive styling that adapts beautifully to various screen sizes.

### Smart Result Handling
The app intelligently manages search results, whether showing a single country in detail, listing multiple matches, or helping users refine searches with too many results.

## How It Works

1. **Search Countries**: Enter a country name in the search field to filter results.
2. **Browse Results**: View a list of matching countries when multiple results are found.
3. **Explore Details**: See comprehensive information about a country including flags, maps, and demographics.
4. **Check Weather**: Get current weather information for the selected country.
5. **Refine Search**: Receive helpful prompts when searches return too many or no results.

## Technologies Used

- **React**: Provides the component-based architecture and state management
- **Axios**: Handles API requests to fetch country and weather data
- **Vite**: Powers the development environment for faster builds
- **JavaScript**: Implements the app logic and dynamic features
- **CSS3**: Delivers a modern, accessible user experience with dark theme styling

## App Functionality

The app allows users to:

- Search countries by name.
- View a detailed country profile, including:
  - Official name
  - Capital
  - Region
  - Subregion
  - Population and area
  - Languages, currencies, and timezones
  - Flags and coat of arms
  - Google Maps link
- See current weather for the selected country.

The app also handles edge cases:
- Too many results: If the search query returns more than 10 countries, the app shows a message prompting the user to narrow down the search.
- No results: If no countries match the search, a message is displayed indicating no countries were found.

## Components

### App Component

The App component is the main entry point of the app. It manages state and renders other components conditionally based on the current search results.

```jsx
const App = () => {
  const [newFilter, setNewFilter] = useState('');
  const [filter, setFilter] = useState('');
  const [countries, setCountries] = useState([]);
  const [toggleState, setToggleState] = useState('');
  const [selectedCountry, setSelectedCountry] = useState([]);

  useEffect(() => {
    axios.get(url).then(response => {
      setCountries(response.data);
    });
  }, []);

  const filteredCountries = filter
    ? countries.filter(country => country.name.common.toLowerCase().includes(filter.toLowerCase()))
    : countries;

  useEffect(() => {
    if (filteredCountries.length === 0) {
      setToggleState("noResults");
    } else if (filteredCountries.length === 1) {
      setSelectedCountry(filteredCountries[0]);
      setToggleState("oneResult");
    } else if (filteredCountries.length <= 10) {
      setToggleState("multipleResults");
    } else {
      setToggleState("tooManyResults");
    }
  }, [filteredCountries]);

  return (
    <div>
      <Form newFilter={newFilter} handleFilterChange={handleFilterChange} addFilter={addFilter} />
      {toggleState === "noResults" && <NoCountries />}
      {toggleState === "oneResult" && <OneCountry selectedCountry={selectedCountry} />}
      {toggleState === "multipleResults" && <CountryList filteredCountries={filteredCountries} />}
      {toggleState === "tooManyResults" && <TooMany />}
    </div>
  );
};
```

### Form Component

The Form component is responsible for capturing the user's search input and triggering the filter function.

```jsx
const Form = ({ newFilter, handleFilterChange, addFilter }) => {
  return (
    <form onSubmit={addFilter}>
      <input value={newFilter} onChange={handleFilterChange} />
      <button type="submit">Search</button>
    </form>
  );
};
```

### CountryList Component

This component displays a list of filtered countries and allows the user to view more details about a specific country.

```jsx
const CountryList = ({ filteredCountries, viewBtn }) => {
  return (
    <ul>
      {filteredCountries.map(country => (
        <li key={country.cca3}>
          {country.name.common}
          <button onClick={() => viewBtn(country.cca3)}>View</button>
        </li>
      ))}
    </ul>
  );
};
```

### OneCountry Component

Displays detailed information about a selected country. It also shows a Weather component with the current weather for that country.

```jsx
const OneCountry = ({ selectedCountry }) => {
  return (
    <div>
      <h2>{selectedCountry.name.common}</h2>
      <p><strong>Capital:</strong> {selectedCountry.capital}</p>
      <p><strong>Population:</strong> {selectedCountry.population}</p>
      {/* More country details */}
      <Weather selectedCountry={selectedCountry} />
    </div>
  );
};
```

### TooMany Component

This component displays a message when there are too many matching results for the search query.

```jsx
const TooMany = () => {
  return <p>Too many matches, specify another filter</p>;
};
```

### NoCountries Component

This component is shown when there are no countries that match the search query.

```jsx
const NoCountries = () => {
  return <p>No countries found</p>;
};
```

### Weather Component

The Weather component fetches and displays the weather information for the selected country using the OpenWeatherMap API.

```jsx
const Weather = ({ selectedCountry }) => {
  const [weather, setWeather] = useState(null);

  const urlWeather = 'https://api.openweathermap.org/data/2.5/weather?q=';
  useEffect(() => {
    axios.get(`${urlWeather}${selectedCountry.name.common}&appid=${apiKey}`)
      .then(response => {
        setWeather(response.data);
      });
  }, [selectedCountry]);

  return (
    <div>
      {weather ? (
        <div>
          <img src={`https://openweathermap.org/img/wn/${weather.weather[0].icon}@2x.png`} />
          <p><strong>Temperature:</strong> {(weather.main.temp - 273.15).toFixed(2)}°C</p>
          {/* More weather details */}
        </div>
      ) : (
        <p>Loading weather data...</p>
      )}
    </div>
  );
};
```

## Styling

The app has a modern dark theme with responsive styling for various screen sizes. It uses media queries to adjust layout and styling based on the device.

### Example Styles

```css

@media (max-width: 768px) {
  div {
    margin: 10px;
    padding: 15px;
  }
}
```

## Conclusion

This app is a starting point for learning how to handle API requests, manage state, and display dynamic data in a React app. It also demonstrates best practices in organizing code, component-based design, and responsive styling.
