<!DOCTYPE html>
<html lang="ca">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title >Recomanacions de Cançons</title>
    <style>
        body {
            font-family: Arial, sans-serif;
            margin: 20px;
            background-color: aquamarine;
        }
        .results {
            margin-top: 20px;
        }

    </style>
</head>
<body>

<img style="width: 5%; height: 5%; text-align: left;" src="https://amipaiessantmarcal.org/wp-content/uploads/2020/01/IES-1024x614.jpg">
<img style="text-align: right;" scr="https://i.pinimg.com/originals/8d/c5/a3/8dc5a359f69211ccbd63b83522752aec.png">
<h1 style="text-align: center;">Recomanacions de cançons</h1>
<h2 style="text-align: center; font-size: small; color: rgb(0, 3, 47);">Lin Su Pingel Payeras</h2>
<p style="font-size: large;">Introdueix el nom d'una cançó per obtenir recomanacions similars:</p>


<!-- Formulario para ingresar la canción -->
<input type="text" id="searchInput" placeholder="Escriu una cançó" style="font-size: large;">
<button onclick="searchSong()" style="font-size: large;">Cerca</button>

<!-- Espacio para mostrar los resultados -->
<div class="results" id="results"></div>

<script>
    const clientId = "039018ef518e43d68d5289989fb3139e"; // Sustituye por tu propio client_id
    const clientSecret = "89aa13c20f4747a7affb9dd5c3e872a7"; // Sustituye por tu propio client_secret
    let accessToken = '';

    // Autenticación con la API de Spotify
    async function getAccessToken() {
        const result = await fetch('https://accounts.spotify.com/api/token', {
            method: 'POST',
            headers: {
                'Content-Type': 'application/x-www-form-urlencoded',
                'Authorization': 'Basic ' + btoa(clientId + ':' + clientSecret)
            },
            body: 'grant_type=client_credentials'
        });
        const data = await result.json();
        accessToken = data.access_token;
    }

    // Función para buscar una canción y obtener recomendaciones
    async function searchSong() {
        const query = document.getElementById('searchInput').value;
        if (!accessToken) {
            await getAccessToken(); // Obtener el token si no está disponible
        }

        // Buscar la canción por nombre
        const searchResult = await fetch(`https://api.spotify.com/v1/search?q=${query}&type=track&limit=1`, {
            method: 'GET',
            headers: { 'Authorization': 'Bearer ' + accessToken }
        });
        const data = await searchResult.json();
        
        if (data.tracks.items.length > 0) {
            const songId = data.tracks.items[0].id; // Obtener el ID de la canción
            const songName = data.tracks.items[0].name;
            const artistName = data.tracks.items[0].artists[0].name;

            // Obtener recomendaciones basadas en la canción
            const recommendations = await fetch(`https://api.spotify.com/v1/recommendations?seed_tracks=${songId}&limit=5`, {
                method: 'GET',
                headers: { 'Authorization': 'Bearer ' + accessToken }
            });

            const recommendationsData = await recommendations.json();
            displayRecommendations(songName, artistName, recommendationsData.tracks);
        } else {
            document.getElementById('results').innerHTML = '<p>No s\'han trobat resultats per la cançó.</p>';
        }
    }

    // Mostrar las recomendaciones en la página
    function displayRecommendations(songName, artistName, tracks) {
        const resultsDiv = document.getElementById('results');
        resultsDiv.innerHTML = `<h3>Recomanacions per a la cançó: "${songName}" de ${artistName}</h3>`;

        if (tracks.length > 0) {
            const list = document.createElement('ul');
            tracks.forEach(track => {
                const listItem = document.createElement('li');
                listItem.textContent = `${track.name} - ${track.artists[0].name}`;
                list.appendChild(listItem);
            });
            resultsDiv.appendChild(list);
        } else {
            resultsDiv.innerHTML = '<p>No s\'han trobat recomanacions.</p>';
        }
    }
</script>

</body>
</html>
