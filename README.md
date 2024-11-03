document.addEventListener('DOMContentLoaded', function() {
    cargarRanking();
});

function cargarRanking() {
    fetch('http://localhost:5000/api/ranking', {
        method: 'GET',
        headers: {
            'Content-Type': 'application/json',
        }
    })
    .then(response => response.json())
    .then(data => {
        if (data && data.length > 0) {
            // Consolidar puntajes antes de mostrar el ranking
            const rankingConsolidado = consolidarPuntajes(data);
            mostrarRanking(rankingConsolidado);
        } else {
            alert('No se encontraron datos del ranking.');
        }
    })
    .catch(error => {
        console.error('Error al cargar el ranking:', error);
        alert('Hubo un problema al cargar el ranking.');
    });
}

// Función para consolidar puntajes de usuarios duplicados
function consolidarPuntajes(data) {
    const rankingMap = {};

    // Agrupar usuarios y sumar sus puntajes
    data.forEach(jugador => {
        if (rankingMap[jugador.nombre]) {
            rankingMap[jugador.nombre].puntaje_total += jugador.puntaje_total;
        } else {
            rankingMap[jugador.nombre] = { 
                nombre: jugador.nombre, 
                puntaje_total: jugador.puntaje_total 
            };
        }
    });

    // Convertir el mapa a un array, ordenar y devolver
    return Object.values(rankingMap).sort((a, b) => b.puntaje_total - a.puntaje_total);
}

function mostrarRanking(rankingData) {
    const rankingBody = document.getElementById('rankingBody');
    rankingBody.innerHTML = ''; // Limpiar el contenido anterior

    rankingData.forEach((jugador, index) => {
        const fila = document.createElement('tr');

        // Columna del puesto
        const puestoCelda = document.createElement('td');
        puestoCelda.innerHTML = 
            index === 0 ? '🥇 1er' :
            index === 1 ? '🥈 2do' :
            index === 2 ? '🥉 3ro' :
            `${index + 1}º`;

        // Columna del nombre del jugador
        const nombreCelda = document.createElement('td');
        nombreCelda.innerText = jugador.nombre;

        // Columna del puntaje
        const puntajeCelda = document.createElement('td');
        puntajeCelda.innerText = jugador.puntaje_total;

        // Agregar las celdas a la fila
        fila.appendChild(puestoCelda);
        fila.appendChild(nombreCelda);
        fila.appendChild(puntajeCelda);

        // Agregar la fila al cuerpo de la tabla
        rankingBody.appendChild(fila);
    });
}

function volverAlMenu() {
    window.location.href = '../pages/index.html';
}
