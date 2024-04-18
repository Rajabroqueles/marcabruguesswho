# marcabruguesswho

<style>
/* Estilos CSS para la interfaz del juego */
.game-container {
    text-align: center;
    margin: 0 auto;
    max-width: 600px; /* Ajusta el ancho máximo según tus preferencias */
    position: relative; /* Para posicionar elementos relativos */
}

.game-container * {
    color: white;
}

.image-container {
    position: relative;
}

.difficulty-points {
    position: absolute;
    top: 10px; /* Espacio desde arriba */
    left: 50%; /* Centrado horizontalmente */
    transform: translateX(-50%); /* Ajuste para centrar */
}

/* Estilos para los modales */
.modal {
    display: none; /* Por defecto, oculto */
    position: fixed; /* Fijo en la ventana */
    z-index: 1; /* Encima de todo */
    left: 0;
    top: 0;
    width: 100%; /* Tamaño completo */
    height: 100%;
    overflow: auto; /* Desplazamiento si es necesario */
    background-color: rgba(0, 0, 0, 0.5); /* Fondo oscuro */
}

.modal-content {
    background-color: #333; /* Fondo del contenido */
    margin: 15% auto; /* Margen superior e inferior */
    padding: 20px;
    border: 1px solid #888;
    width: 80%; /* Ancho del contenido */
    max-width: 400px; /* Ancho máximo del modal */
    border-radius: 10px; /* Bordes redondeados */
    box-shadow: 0 4px 8px rgba(0, 0, 0, 0.3); /* Sombra */
}

.close {
    color: #aaa;
    float: right;
    font-size: 28px;
    font-weight: bold;
}

.close:hover,
.close:focus {
    color: #fff;
    text-decoration: none;
    cursor: pointer;
}

/* Estilos adicionales para centrar el botón en los modales */
.modal-content button {
    display: block;
    margin: 0 auto; /* Centro horizontalmente */
}

/* Estilos adicionales para mejorar la legibilidad */
.modal p {
    margin: 10px 0;
    font-size: 18px;
    color: #fff;
    text-align: center;
}

.modal button {
    background-color: #4CAF50; /* Color de fondo verde */
    color: white; /* Texto en blanco */
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-size: 16px;
    transition: background-color 0.3s; /* Transición suave */
}

.modal button:hover {
    background-color: #45a049; /* Cambio de color al pasar el ratón */
}

.submit-button {
    background-color: #4CAF50; /* Color de fondo verde */
    color: white; /* Texto en blanco */
    padding: 10px 20px;
    border: none;
    border-radius: 5px;
    cursor: pointer;
    font-size: 16px;
    transition: background-color 0.3s; /* Transición suave */
}

.submit-button:hover {
    background-color: #45a049; /* Cambio de color al pasar el ratón */
}

/* Transición suave y efecto de escala para las imágenes */
.image-container img {
    opacity: 0;
    transform: scale(0.8); /* Empieza un poco más pequeña */
    transition: opacity 1s ease-in-out, transform 1s cubic-bezier(0.68, -0.55, 0.27, 1.55); /* Transición más larga y suave con efecto de escala */
}

.image-container img.loaded {
    opacity: 1;
    transform: scale(1); /* Termina en su tamaño normal */
}

/* Estilos para el botón de música */
.music-button {
    position: absolute;
    top: 10px;
    right: 10px;
    cursor: pointer;
}

.music-button img {
    width: 30px;
    height: auto;
}

</style>

<div class="game-container">
    <div class="image-container">
        <img id="character-image" alt="Personaje de dibujos animados" class="loaded">
    </div>
    <div class="input-container">
        <div>
            <label for="character">Nombre:</label>
            <input type="text" id="character" name="character">
        </div>
        <div>
            <label for="series">Conocido por:</label> <!-- Cambio de "Serie" a "Conocido por" -->
            <input type="text" id="series" name="series">
        </div>
        <div>
            <button id="submit" class="submit-button">Enviar respuesta</button>
        </div>
    </div>
    <div class="score-container">
        Tu puntuación: <span id="score">0</span>
    </div>
    <div class="timer-container">
        Tiempo restante: <span id="timer">90</span> segundos
    </div>
    <div class="music-button" id="musicButton">
        <img id="musicIcon" src="https://marcabru.com/wp-content/uploads/2024/04/icono-unmuted.png" alt="Music Icon">
    </div>
</div>

<!-- Modal de victoria -->
<div id="victoryModal" class="modal">
    <div class="modal-content">
        <span class="close">&times;</span>
        <p>¡Felicidades! Has completado todas las imágenes.</p>
        <p>Tu puntuación: <span id="victoryScore">0</span></p>
        <button id="startNewGameVictory">Empezar nueva partida</button>
    </div>
</div>

<!-- Modal de derrota -->
<div id="loseModal" class="modal">
    <div class="modal-content">
        <span class="close">&times;</span>
        <p>¡Lo siento! Has fallado.</p>
        <p>Tu puntuación: <span id="loseScore">0</span></p>
        <button id="startNewGameLose">Empezar nueva partida</button>
    </div>
</div>

<audio id="backgroundMusic" loop>
    <source src="" type="audio/mpeg">
    Your browser does not support the audio element.
</audio>
<audio id="correctSound">
    <source src="https://marcabru.com/wp-content/uploads/2024/04/acierto-sonido.mp3" type="audio/mpeg">
    Your browser does not support the audio element.
</audio>
<audio id="failSound">
    <source src="https://marcabru.com/wp-content/uploads/2024/04/fallo-sonido.mp3" type="audio/mpeg">
    Your browser does not support the audio element.
</audio>
<audio id="victorySound">
    <source src="https://marcabru.com/wp-content/uploads/2024/04/victoria-sonido.mp3" type="audio/mpeg">
    Your browser does not support the audio element.
</audio>

<script>
document.addEventListener('DOMContentLoaded', function() {
    var characters = [
        {
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/goku-dragon-ball.jpg",
            characterAnswers: ["goku", "son goku", "kakarot", "kakaroto"],
            seriesAnswers: ["dragon Ball", "bola de dragon", "bola de drac"],
            difficulty: "Fácil",
            points: 10
        },
        {
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/finn-hora-de-aventuras.jpg",
            characterAnswers: ["finn el Humano", "finn", "finn mertens", "finn the human", "finn y jake", "finn and jake", "jake y finn", "jake and finn", "jake el perro y finn el humano", "jake the dog and finn the human", "jake the dog", "jake"],
            seriesAnswers: ["hora de aventuras", "adventure time", "hora de aventura"],
            difficulty: "Avanzado",
            points: 30
        },
        {
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/luffy-one-piece.jpg",
            characterAnswers: ["monkey d. luffy", "luffy"],
            seriesAnswers: ["one piece"],
            difficulty: "Fácil",
            points: 10
        },
		{
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/nobita-doraemon.jpg",
            characterAnswers: ["nobita", "Nobita Nobi", "Noby"],
            seriesAnswers: ["doraemon"],
            difficulty: "Medio",
            points: 20
        },
		{
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/rick-rick-and-morty.jpg",
            characterAnswers: ["rick", "Rick Sanchez", "Richard Daniel Sanchez", "richard"],
            seriesAnswers: ["Rick and morty", "Rick y Morty"],
            difficulty: "Medio",
            points: 20
        },
		  {
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/cartman-shouth-park-v2.jpg",
            characterAnswers: ["Eric Cartman", "cartman", "Eric Theodore Cartman", "Eric P. Cartman"],
            seriesAnswers: ["south park"],
            difficulty: "Medio",
            points: 20
        },
        {
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/homer-simpsons-v2.jpg",
            characterAnswers: ["homer", "homero", "homer simpson", "homero simpson"],
            seriesAnswers: ["the simpsons", "simpsons", "los simpsons"],
            difficulty: "Fácil",
            points: 10
        },
        {
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/johnny-bravo-v3.jpg",
            characterAnswers: ["johnny bravo", "bravo", "johnny", "johny bravo", "jhonny bravo", "jony bravo", "jonny bravo"],
            seriesAnswers: ["johnny bravo", "johny bravo", "jhonny bravo", "jhony bravo", "jony bravo", "jonny bravo"],
            difficulty: "Medio",
            points: 20
        },
		{
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/dexter-laboratorio.jpg",
            characterAnswers: ["Dexter"],
            seriesAnswers: ["Dexter's Laboratory", "Dexters Laboratory", "el laboratorio de dexter", "laboratorio de dexter"],
            difficulty: "Medio",
            points: 20
        },
		{
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/popeye.jpg",
            characterAnswers: ["popeye", "popeye el marino", "Popeye the Sailor"],
            seriesAnswers: ["popeye", "popeye el marino", "Popeye the Sailor"],
            difficulty: "Fácil",
            points: 10
        },
		{
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/peter-griffin-padre-de-familia.jpg",
            characterAnswers: ["peter griffin", "peter griffing", "Peter Löwenbrau McFinnigan Griffin", "Peter Lowenbrau McFinnigan Griffin", "Peter Löwenbräu Griffin Sr.", "Peter Lowenbrau Griffin Sr"],
            seriesAnswers: ["Padre de familia", "Family Guy"],
            difficulty: "Medio",
            points: 20
        },
        {
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/saitama-one-punch.jpg",
            characterAnswers: ["saitama"],
            seriesAnswers: ["One-Punch Man", "One Punch Man", "OnePunch Man", "Wanpanman", "one punch"],
            difficulty: "Medio",
            points: 20
        },
		{
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/marco-rossi-metal-slug.jpg",
            characterAnswers: ["marco", "marco rossi"],
            seriesAnswers: ["metal slug"],
            difficulty: "Difícil",
            points: 40
        },
		{
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/light-yagami-death-note.jpg",
            characterAnswers: ["Light", "Light Yagami", "kira"],
            seriesAnswers: ["death note"],
            difficulty: "Avanzado",
            points: 30
        },
		  {
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/sakura-naruto.jpg",
            characterAnswers: ["Sakura", "Sakura Haruno", "Haruno Sakura"],
            seriesAnswers: ["naruto"],
            difficulty: "Avanzado",
            points: 30
        },
        {
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/chihiro.jpg",
            characterAnswers: ["chihiro", "Chihiro Ogino", "sen", "Ogino Chihiro"],
            seriesAnswers: ["el viaje de chihiro", "spirited away", "Sen to Chihiro no Kamikakushi"],
            difficulty: "Avanzado",
            points: 30
        },
        {
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/ash-pokemon.jpg",
            characterAnswers: ["ash", "Ash Ketchum", "Satoshi"],
            seriesAnswers: ["pokemon", "the pokemon", "the poket monster", "the poket monsters"],
            difficulty: "Fácil",
            points: 10
        },
		{
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/nami-one-piede.jpg",
            characterAnswers: ["Nami", "la gata ladrona"],
            seriesAnswers: ["one piece"],
            difficulty: "Medio",
            points: 20
        },
		{
            imageUrl: "https://marcabru.com/wp-content/uploads/2024/04/chicho-terremoto.jpg",
            characterAnswers: ["chicho terremoto", "Dash Kappei", "Kappei", "Gigi el chico terremoto", "Gigi", "chicho"],
            seriesAnswers: ["chicho terremoto", "Dash Kappei", "Kappei", "Gigi el chico terremoto", "Gigi", "chicho"],
            difficulty: "Difícil",
            points: 40
        }
						  
        // Puedes agregar más objetos para cada personaje de la misma manera
    ];

    var timer = document.getElementById('timer');
    var submitButton = document.getElementById('submit');
    var characterImage = document.getElementById('character-image');
    var scoreSpan = document.getElementById('score');
    var victoryModal = document.getElementById('victoryModal');
    var victoryScore = document.getElementById('victoryScore');
    var loseModal = document.getElementById('loseModal');
    var loseScore = document.getElementById('loseScore');
    var countdown = 90;
    var currentCharacter;
    var answeredCharacters = [];

    // Elementos de audio
    var backgroundMusic = document.getElementById('backgroundMusic');
    var correctSound = document.getElementById('correctSound');
    var failSound = document.getElementById('failSound');
    var victorySound = document.getElementById('victorySound');

    // Botón de música
    var musicButton = document.getElementById('musicButton');
    var musicIcon = document.getElementById('musicIcon');
    var isMusicPlaying = true;

    // Función para eliminar acentos de una cadena
    function removeAccents(str) {
        return str.normalize("NFD").replace(/[\u0300-\u036f]/g, "");
    }

    // Función para verificar si una respuesta es correcta (insensible a mayúsculas/minúsculas y a acentos)
    function isCorrectAnswer(userAnswer, correctAnswers) {
        userAnswer = removeAccents(userAnswer.toLowerCase());
        correctAnswers = correctAnswers.map(answer => removeAccents(answer.toLowerCase()));
        return correctAnswers.includes(userAnswer);
    }

    // Función para actualizar el temporizador
    function updateTimer() {
        if (countdown > 0) {
            countdown--;
            timer.textContent = countdown;
            setTimeout(updateTimer, 1000);
        } else {
            endGame();
        }
    }

    // Función para reproducir música de fondo
    function playBackgroundMusic() {
        if (isMusicPlaying) {
            backgroundMusic.play();
        }
    }

    // Función para pausar música de fondo
    function pauseBackgroundMusic() {
        backgroundMusic.pause();
    }

    // Función para mostrar un nuevo personaje
    function showNewCharacter() {
        var availableCharacters = characters.filter(character => !answeredCharacters.includes(character));
        if (availableCharacters.length === 0) {
            showVictoryModal();
            return;
        }
        var randomIndex = Math.floor(Math.random() * availableCharacters.length);
        currentCharacter = availableCharacters[randomIndex];
        characterImage.src = currentCharacter.imageUrl;
        characterImage.classList.remove('loaded');
        setTimeout(function() {
            characterImage.classList.add('loaded');
        }, 100);

        playBackgroundMusic(); // Iniciar música de fondo al mostrar un nuevo personaje
    }

    // Función para finalizar el juego cuando se acaba el tiempo
    function endGame() {
        pauseBackgroundMusic(); // Pausar música de fondo al finalizar el juego
        showLoseModal();
    }

    // Función para reproducir sonido de acierto
    function playCorrectSound() {
        correctSound.play();
    }

    // Función para reproducir sonido de fallo
    function playFailSound() {
        failSound.play();
    }

    // Función para reproducir sonido de victoria
    function playVictorySound() {
        victorySound.play();
    }

    // Función para mostrar el modal de victoria
    function showVictoryModal() {
        playVictorySound(); // Reproducir sonido de victoria al mostrar el modal
        victoryScore.textContent = scoreSpan.textContent;
        victoryModal.style.display = 'block';
        pauseBackgroundMusic(); // Pausar música de fondo al mostrar el modal
    }

    // Función para mostrar el modal de derrota
    function showLoseModal() {
        playFailSound(); // Reproducir sonido de fallo al mostrar el modal
        loseScore.textContent = scoreSpan.textContent;
        loseModal.style.display = 'block';
        pauseBackgroundMusic(); // Pausar música de fondo al mostrar el modal
    }

    // Iniciar el temporizador y mostrar el primer personaje al cargar la página
    updateTimer();
    showNewCharacter();

    // Manejar el evento de clic en el botón de enviar respuesta
    submitButton.addEventListener('click', function() {
        submitAnswer();
    });

    // Manejar el evento de tecla presionada en los campos de nombre y serie
    document.getElementById('character').addEventListener('keydown', function(event) {
        if (event.key === 'Enter' && document.getElementById('series').value.trim() !== '') {
            submitAnswer();
        }
    });
    document.getElementById('series').addEventListener('keydown', function(event) {
        if (event.key === 'Enter' && document.getElementById('character').value.trim() !== '') {
            submitAnswer();
        }
    });

    // Función para enviar la respuesta
    function submitAnswer() {
        // Obtener las respuestas proporcionadas por el usuario
        var characterInput = document.getElementById('character').value.trim();
        var seriesInput = document.getElementById('series').value.trim();

        // Verificar si las respuestas son correctas
        var characterCorrect = isCorrectAnswer(characterInput, currentCharacter.characterAnswers);
        var seriesCorrect = isCorrectAnswer(seriesInput, currentCharacter.seriesAnswers);

        // Calcular la puntuación y actualizar la interfaz
        if (characterCorrect && seriesCorrect) {
            // Incrementar la puntuación del jugador
            var score = parseInt(scoreSpan.textContent);
            score += currentCharacter.points;
            scoreSpan.textContent = score;
            answeredCharacters.push(currentCharacter);
            playCorrectSound(); // Reproducir sonido de acierto al responder correctamente
        } else {
            // Reiniciar la puntuación del jugador en caso de fallo
            endGame();
        }

        // Mostrar un nuevo personaje
        showNewCharacter();

        // Reiniciar el temporizador
        countdown = 90;
        timer.textContent = countdown;

        // Limpiar los campos de entrada
        document.getElementById('character').value = '';
        document.getElementById('series').value = '';
    }

    // Manejar el cierre de los modales
    var closeButtons = document.getElementsByClassName("close");
    for (var i = 0; i < closeButtons.length; i++) {
        closeButtons[i].addEventListener('click', function() {
            victoryModal.style.display = 'none';
            loseModal.style.display = 'none';
        });
    }

    // Manejar el inicio de una nueva partida desde el modal de victoria
    document.getElementById('startNewGameVictory').addEventListener('click', function() {
        victoryModal.style.display = 'none';
        resetGame();
    });

    // Manejar el inicio de una nueva partida desde el modal de derrota
    document.getElementById('startNewGameLose').addEventListener('click', function() {
        loseModal.style.display = 'none';
        resetGame();
    });

    // Función para reiniciar el juego
    function resetGame() {
        scoreSpan.textContent = '0';
        answeredCharacters = [];
        updateTimer();
        showNewCharacter();
    }

    // Manejar el clic en el botón de música
    musicButton.addEventListener('click', function() {
        if (isMusicPlaying) {
            backgroundMusic.pause();
            musicIcon.src = 'https://marcabru.com/wp-content/uploads/2024/04/icono-muted.png'; // Cambiar el icono a muteado
        } else {
            backgroundMusic.play();
            musicIcon.src = 'https://marcabru.com/wp-content/uploads/2024/04/icono-unmuted.png'; // Cambiar el icono a no muteado
        }
        isMusicPlaying = !isMusicPlaying; // Cambiar el estado de reproducción de la música
    });
});
</script>
