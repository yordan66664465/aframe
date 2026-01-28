<!DOCTYPE html>
<html>
  <head>
    <title>Snake VR - A-Frame</title>
    <script src="https://aframe.io/releases/1.4.0/aframe.min.js"></script>
  </head>
  <body>
    <script>
      // Lógica del Juego
      AFRAME.registerComponent('snake-game', {
        init: function () {
          this.snake = [{x: 0, y: 1, z: -5}]; // Posición inicial
          this.direction = {x: 1, y: 0, z: 0}; // Dirección inicial (derecha)
          this.food = {x: 3, y: 1, z: -8};
          this.tickRate = 300; // Velocidad en milisegundos
          this.lastTick = 0;
          this.score = 0;

          // Controles de teclado
          window.addEventListener('keydown', (e) => {
            if (e.key === 'ArrowUp' && this.direction.z !== 1) this.direction = {x: 0, y: 0, z: -1};
            if (e.key === 'ArrowDown' && this.direction.z !== -1) this.direction = {x: 0, y: 0, z: 1};
            if (e.key === 'ArrowLeft' && this.direction.x !== 1) this.direction = {x: -1, y: 0, z: 0};
            if (e.key === 'ArrowRight' && this.direction.x !== -1) this.direction = {x: 1, y: 0, z: 0};
          });

          this.createFood();
        },

        createFood: function () {
          let foodEl = document.querySelector('#food');
          this.food = {
            x: Math.floor(Math.random() * 10) - 5,
            y: 1,
            z: Math.floor(Math.random() * 10) - 10
          };
          foodEl.setAttribute('position', `${this.food.x} ${this.food.y} ${this.food.z}`);
        },

        tick: function (time, timeDelta) {
          if (time - this.lastTick < this.tickRate) return;
          this.lastTick = time;

          // Calcular nueva cabeza
          let head = { 
            x: this.snake[0].x + this.direction.x, 
            y: this.snake[0].y, 
            z: this.snake[0].z + this.direction.z 
          };

          // Colisión con comida
          if (head.x === this.food.x && head.z === this.food.z) {
            this.score++;
            this.createFood();
            // No quitamos la cola para que crezca
          } else {
            this.snake.pop(); // Quitar último segmento
          }

          // Colisión con paredes (opcional, aquí reinicia)
          if (Math.abs(head.x) > 10 || head.z > 0 || head.z < -20) {
            alert("Game Over! Puntuación: " + this.score);
            location.reload();
          }

          this.snake.unshift(head); // Añadir nueva cabeza
          this.renderSnake();
        },

        renderSnake: function () {
          let container = document.querySelector('#snake-container');
          container.innerHTML = ''; // Limpiar render previo
          
          this.snake.forEach((segment, index) => {
            let box = document.createElement('a-box');
            box.setAttribute('position', `${segment.x} ${segment.y} ${segment.z}`);
            box.setAttribute('color', index === 0 ? '#4CAF50' : '#8BC34A'); // Cabeza más oscura
            box.setAttribute('scale', '0.9 0.9 0.9');
            container.appendChild(box);
          });
        }
      });
    </script>

    <a-scene snake-game>
      <a-sky color="#ECECEC"></a-sky>
      <a-plane position="0 0 -10" rotation="-90 0 0" width="22" height="22" color="#7BC8A4"></a-plane>

      <a-entity id="snake-container"></a-entity>

      <a-sphere id="food" radius="0.4" color="#FF5722"></a-sphere>

      <a-light type="ambient" color="#BBB"></a-light>
      <a-light type="directional" color="#FFF" intensity="0.6" position="-1 1 1"></a-light>
      
      <a-entity position="0 5 5" rotation="-30 0 0">
        <a-camera></a-camera>
      </a-entity>
    </a-scene>
  </body>
</html>
