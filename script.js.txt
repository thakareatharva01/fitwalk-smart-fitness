const statusText = document.getElementById("status");
const networkStatus = document.getElementById("networkStatus");
const canvas = document.getElementById("routeCanvas");
const ctx = canvas.getContext("2d");

let previousCoords = null;

function drawRoute(lat, lng) {
  const x = (lng + 180) * (canvas.width / 360);   
  const y = (90 - lat) * (canvas.height / 180);   

  if (previousCoords) {
    ctx.beginPath();
    ctx.moveTo(previousCoords.x, previousCoords.y);
    ctx.lineTo(x, y);
    ctx.strokeStyle = "blue";
    ctx.lineWidth = 2;
    ctx.stroke();
  }

  previousCoords = { x, y };
}

function updateLocation(position) {
  const { latitude, longitude } = position.coords;
  statusText.textContent = `📍 Current Location: (${latitude.toFixed(4)}, ${longitude.toFixed(4)})`;
  drawRoute(latitude, longitude);
}

function locationError(err) {
  statusText.textContent = "❌ Unable to retrieve location.";
  console.error(err);
}

if ("geolocation" in navigator) {
  navigator.geolocation.watchPosition(updateLocation, locationError, {
    enableHighAccuracy: true,
    maximumAge: 0,
    timeout: 5000,
  });
} else {
  statusText.textContent = "❌ Geolocation not supported by your browser.";
}

// Network Information API
if ("connection" in navigator) {
  const updateNetwork = () => {
    const connection = navigator.connection;
    const type = connection.effectiveType || "unknown";
    networkStatus.textContent = `🌐 Network: ${type}`;
  };

  navigator.connection.addEventListener("change", updateNetwork);
  updateNetwork();
} else {
  networkStatus.textContent = "🌐 Network info not supported.";
}
