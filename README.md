# Busy Towers Flight Tracker

A real-time flight tracking web application that displays aircraft around major airports with live ATC audio, trails, and detailed aircraft information.

## Table of Contents
- [Features](#features)
- [Quick Start](#quick-start)
- [Understanding the Code](#understanding-the-code)
- [How to Modify](#how-to-modify)
- [Troubleshooting](#troubleshooting)

## Features

- **Real-time Aircraft Tracking**: Updates every 2 seconds with live aircraft positions
- **Visual Aircraft Trails**: Color-coded by aircraft type (Commercial, General Aviation, Military, Helicopter)
- **Live ATC Audio**: Stream real-time air traffic control communications
- **Aircraft Details**: Click any aircraft to see altitude, speed, and flight status
- **Multiple Airports**: Switch between major airports (JFK, LAX, ATL, ORD, DFW, DEN, SFO, IAD)
- **Mobile Optimized**: Full-screen experience on mobile devices
- **Follow Mode**: Automatically tracks selected aircraft

## Quick Start

### Running Locally

1. Download the `index.html` file
2. Open it in any modern web browser (Chrome, Firefox, Safari, Edge)
3. That's it! No installation or server required.

### Hosting Online

Upload the `index.html` file to any web hosting service:
- **GitHub Pages**: Free and easy
- **Netlify**: Drag and drop deployment
- **Vercel**: Simple hosting with custom domains

The file is completely self-contained with no external dependencies except the map tiles and aircraft data APIs.

## Understanding the Code

### Overall Structure

The application is divided into three main sections:

#### 1. **Configuration & Setup** (Lines 1-550)
- HTML structure and styling
- Airport configurations
- Map styling and UI elements

#### 2. **Core Functions** (Lines 551-1030)
- Data fetching from aircraft APIs
- Map initialization
- Aircraft rendering and trail drawing
- User interaction handling

#### 3. **Initialization** (Lines 1031-1047)
- Starts the application when the page loads

### Key Components

#### Aircraft Data Source
```javascript
const airport = AIRPORTS[currentAirport];
const response = await fetch(`https://api.airplanes.live/v2/point/${airport.lat}/${airport.lon}/50`);
```
This API provides real-time aircraft data from airplanes.live. It's free and doesn't require authentication. The API fetches aircraft within 50 nautical miles of the airport coordinates.

#### Airport Configuration
```javascript
const AIRPORTS = {
    'JFK': {
        code: 'JFK',
        name: 'New York JFK',
        lat: 40.6413,
        lon: -73.7781,
        zoom: 10,
        audio: 'https://s1-bos.liveatc.net/kjfk_twr'
    },
    // ... more airports
};
```
Each airport has coordinates, zoom level, and an ATC audio stream URL.

#### Update Interval
```javascript
setInterval(fetchAircraft, 2000);
```
Aircraft positions update every 2000 milliseconds (2 seconds).

#### Trail History
```javascript
if (trail.length > 30) trail.shift();
```
Each aircraft trail stores up to 30 position points (about 1 minute of history at 2-second intervals).

## How to Modify

### Adding a New Airport

1. Find the `AIRPORTS` object (around line 446)
2. Add your airport following this format:

```javascript
'YOUR_CODE': {
    code: 'YOUR_CODE',
    name: 'Display Name',
    lat: 40.1234,      // Airport latitude
    lon: -74.5678,     // Airport longitude
    zoom: 10,          // Map zoom level (8-12 works well)
    audio: 'https://your-atc-stream-url'
},
```

3. Add it to the dropdown menu HTML (around line 358):

```html
<option value="YOUR_CODE">YOUR_CODE</option>
```

**Finding Airport Coordinates:**
- Google Maps: Right-click on the airport → Copy coordinates
- Wikipedia: Check the airport's Wikipedia page

**Finding ATC Audio Streams:**
- Visit [LiveATC.net](https://www.liveatc.net)
- Find your airport and copy the stream URL

### Changing Update Speed

Find this line (around line 1027):
```javascript
setInterval(fetchAircraft, 2000);
```

Change `2000` to your desired interval in milliseconds:
- `1000` = 1 second (faster updates, more API calls)
- `5000` = 5 seconds (slower updates, fewer API calls)
- `10000` = 10 seconds (very slow updates)

**Note**: Too frequent updates may cause performance issues or API rate limiting.

### Adjusting Trail Length

Find this line (around line 732):
```javascript
if (trail.length > 30) trail.shift();
```

Change `30` to your desired number of trail points:
- `15` = Shorter trails (30 seconds at 2-second updates)
- `60` = Longer trails (2 minutes)
- `150` = Very long trails (5 minutes)

**Note**: Longer trails use more memory and may slow down the map.

### Changing Map Appearance

Find the map tile URL (around line 590):
```javascript
L.tileLayer('https://{s}.basemaps.cartocdn.com/dark_all/{z}/{x}/{y}{r}.png'
```

Replace with a different tile provider:

**Light Theme:**
```javascript
'https://{s}.basemaps.cartocdn.com/light_all/{z}/{x}/{y}{r}.png'
```

**Street Map:**
```javascript
'https://{s}.tile.openstreetmap.org/{z}/{x}/{y}.png'
```

**Satellite Imagery:**
```javascript
'https://server.arcgisonline.com/ArcGIS/rest/services/World_Imagery/MapServer/tile/{z}/{y}/{x}'
```

### Modifying Aircraft Colors

Find the aircraft type colors (around line 241-301 for icons, 855-861 for trails):

**Icon Colors (CSS):**
```css
.aircraft-comm { fill: #0a84ff; }  /* Commercial - Blue */
.aircraft-ga { fill: #30d158; }    /* General Aviation - Green */
.aircraft-mil { fill: #ff375f; }   /* Military - Red */
.aircraft-heli { fill: #ff9f0a; }  /* Helicopter - Orange */
```

**Trail Colors (JavaScript):**
```javascript
const colors = {
    'trail-comm': '#0a84ff',   // Commercial
    'trail-ga':   '#30d158',   // General Aviation
    'trail-mil':  '#ff375f',   // Military
    'trail-heli': '#ff9f0a',   // Helicopter
    'trail-other':'#a2a2d2'    // Other/Unknown
};
```

Change the hex color codes to any color you want. Use [a color picker](https://htmlcolorcodes.com/) to find hex codes.

### Filtering Aircraft

Find the aircraft filter logic (around line 703):
```javascript
const altitude = ac.alt_baro || ac.alt_geom || 0;
const isFlying = altitude > 100; // Filter out planes on the ground

if (!ac.lat || !ac.lon || ac.seen > 60 || !isFlying) return;
```

**Show Ground Aircraft:**
```javascript
const isFlying = altitude > 0; // Shows all aircraft
```

**Only Show High Altitude Aircraft:**
```javascript
const isFlying = altitude > 10000; // Only shows aircraft above 10,000 ft
```

**Filter by Speed:**
```javascript
const speed = ac.gs || 0; // Ground speed in knots
if (speed < 50) return; // Skip slow-moving aircraft
```

### Changing Fonts

The app uses two font families:

**System Font (UI elements):**
```css
font-family: -apple-system, BlinkMacSystemFont, 'Inter', 'SF Pro Display', sans-serif;
```

**Monospace Font (Data displays):**
```css
font-family: 'Roboto Mono', monospace;
```

To use different fonts:
1. Find a font on [Google Fonts](https://fonts.google.com/)
2. Add the import at the top of the CSS (around line 11):
```css
@import url('https://fonts.googleapis.com/css2?family=Your+Font+Name&display=swap');
```
3. Replace the font-family values

### Adjusting UI Element Positions

All UI elements use `position: fixed` with pixel values:

**Header (around line 48):**
```css
top: 0;
padding: 24px 32px;
```

**Aircraft Info Box (around line 107):**
```css
bottom: 32px;  /* Distance from bottom */
left: 50%;     /* Centered horizontally */
```

**ATC Button (around line 173):**
```css
bottom: 32px;  /* Distance from bottom */
left: 32px;    /* Distance from left */
```

**Legend (around line 211):**
```css
top: 90px;     /* Distance from top */
right: 32px;   /* Distance from right */
```

Change these pixel values to reposition elements.

### Modifying the Initial Trail

When aircraft first appear, the app creates a "fake" trail based on their heading (around line 715):

```javascript
for (let i = 1; i >= 0; i--) {
    const timeBack = i * .5; 
    const distBack = speed * timeBack;
    const lat = ac.lat - (Math.cos(heading) * distBack);
    const lon = ac.lon - (Math.sin(heading) * distBack);
    trail.push([lat, lon]);
}
```

**Longer Initial Trail:**
```javascript
for (let i = 5; i >= 0; i--) {  // Change from 1 to 5
```

**Shorter Initial Trail:**
```javascript
for (let i = 0; i >= 0; i--) {  // Essentially no initial trail
```

## Troubleshooting

### Aircraft Not Appearing

**Check the browser console** (F12 → Console tab):
- Look for red error messages
- Common issue: CORS errors (use a web server, not file://)

**Verify API access:**
- Open https://api.airplanes.live/v2/point/40.6413/-73.7781/50 in your browser
- You should see JSON data with aircraft information

### ATC Audio Not Playing

**Browser autoplay restrictions:**
- Most browsers block audio autoplay
- Click the "Listen to ATC" button manually
- Audio starts muted by default to bypass restrictions

**Stream not working:**
- Some ATC streams go offline temporarily
- Try a different airport
- Check [LiveATC.net](https://www.liveatc.net) to verify the stream is active

### Map Not Loading

**Tile server issues:**
- The map tile provider may be temporarily down
- Try switching to a different tile provider (see "Changing Map Appearance")

**Internet connection:**
- Map tiles require an active internet connection

### Mobile Browser Chrome Showing

**iOS Safari:**
- Scroll down on the page to hide the address bar
- The app is designed to minimize chrome automatically

**Add to Home Screen for full immersion:**
- Tap Share → Add to Home Screen
- Launch from home screen for fullscreen experience

### Performance Issues

**Reduce update frequency:**
```javascript
setInterval(fetchAircraft, 5000); // Update every 5 seconds instead of 2
```

**Shorten trails:**
```javascript
if (trail.length > 15) trail.shift(); // Keep only 15 points instead of 30
```

**Limit visible aircraft:**
```javascript
const altitude = ac.alt_baro || ac.alt_geom || 0;
if (altitude < 5000 || altitude > 40000) return; // Only show aircraft in range
```

### Data Not Updating

**Check update interval:**
- Verify `setInterval(fetchAircraft, 2000)` is present
- Open console to see if fetch requests are happening

**API rate limiting:**
- The airplanes.live API may rate limit excessive requests
- Increase update interval to 5-10 seconds if needed

## Technical Details

### Browser Compatibility

- **Chrome/Edge**: Full support
- **Firefox**: Full support
- **Safari**: Full support (iOS 11+)
- **Mobile browsers**: Optimized for mobile experience

### API Information

**Aircraft Data API:**
- Provider: airplanes.live (community-run ADS-B aggregator)
- Update frequency: Real-time
- Coverage: Global (best in North America and Europe)
- Rate limits: Generally permissive for personal use
- Radius: 50 nautical miles from airport coordinates

**Map Tiles:**
- Provider: CARTO (CartoDB)
- Style: Dark theme optimized for flight tracking
- Free tier: Generous for personal projects

### How Aircraft Data Works

1. **ADS-B Receivers**: Ground stations receive broadcasts from aircraft transponders
2. **Aggregation**: Community receivers feed data to aggregation services
3. **API**: Your app requests data within a radius of the selected airport
4. **Filtering**: Code filters out invalid/grounded aircraft
5. **Rendering**: Aircraft positions are converted to map markers with icons
6. **Trails**: Position history is stored and drawn as colored lines

### Data Update Cycle

```
Initial Load → Build Trails (5 fetches, 2s apart) → Regular Updates (every 2s)
```

On startup, the app fetches data 5 times rapidly to build initial trail history, then continues with regular 2-second updates.

## Credits

- **Map Tiles**: CARTO / OpenStreetMap contributors
- **Aircraft Data**: airplanes.live community and contributors
- **ATC Audio**: LiveATC.net
- **Map Library**: Leaflet.js

## License

This code is provided as-is for educational and personal use. Respect the terms of service for:
- Aircraft data APIs
- Map tile providers
- ATC audio streams

## Need Help?

If you're stuck:
1. Check the browser console (F12) for error messages
2. Verify all URLs are accessible in your browser
3. Make sure you're running from a web server, not opening the file directly
4. Test with the original, unmodified code first

Happy tracking! ✈️
