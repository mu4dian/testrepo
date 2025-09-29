<template>
  <div class="game-container">
    <div class="game-header">
      <h1>🐍 Vue Snake Game</h1>
      <div class="score-board">
        <div class="score">Score: {{ score }}</div>
        <div class="high-score">High Score: {{ highScore }}</div>
      </div>
    </div>
    
    <div class="game-content">
      <div v-if="gameState === 'start'" class="start-screen">
        <h2>Welcome to Snake Game!</h2>
        <p>Use WASD or Arrow keys to control the snake</p>
        <button @click="startGame" class="start-btn">Start Game</button>
      </div>
      
      <div v-else-if="gameState === 'playing'" class="game-board-container">
        <canvas 
          ref="gameCanvas"
          :width="canvasWidth"
          :height="canvasHeight"
          class="game-board"
          @focus="handleFocus"
          tabindex="0"
        ></canvas>
        <div class="game-controls">
          <p>Use WASD or Arrow Keys to move</p>
          <button @click="pauseGame" class="control-btn">{{ isPaused ? 'Resume' : 'Pause' }}</button>
        </div>
      </div>
      
      <div v-else-if="gameState === 'gameOver'" class="game-over-screen">
        <h2>Game Over!</h2>
        <p>Final Score: {{ score }}</p>
        <p v-if="isNewHighScore" class="new-high-score">🎉 New High Score!</p>
        <button @click="startGame" class="restart-btn">Play Again</button>
        <button @click="goToStart" class="menu-btn">Main Menu</button>
      </div>
    </div>
  </div>
</template>

<script>
import { ref, onMounted, onUnmounted, nextTick } from 'vue'

export default {
  name: 'SnakeGame',
  setup() {
    const gameCanvas = ref(null)
    const gameState = ref('start') // 'start', 'playing', 'gameOver'
    const score = ref(0)
    const highScore = ref(0)
    const isNewHighScore = ref(false)
    const isPaused = ref(false)
    
    // Game constants
    const canvasWidth = 400
    const canvasHeight = 400
    const gridSize = 20
    const gridWidth = canvasWidth / gridSize
    const gridHeight = canvasHeight / gridSize
    
    // Game state
    const snake = ref([{ x: 10, y: 10 }])
    const food = ref({ x: 15, y: 15 })
    const direction = ref({ x: 0, y: 0 })
    const nextDirection = ref({ x: 0, y: 0 })
    
    let gameLoop = null
    let ctx = null
    
    // Initialize game
    const initGame = () => {
      snake.value = [{ x: 10, y: 10 }]
      direction.value = { x: 0, y: 0 }
      nextDirection.value = { x: 0, y: 0 }
      score.value = 0
      isPaused.value = false
      isNewHighScore.value = false
      generateFood()
    }
    
    // Start game
    const startGame = () => {
      initGame()
      gameState.value = 'playing'
      nextTick(() => {
        if (gameCanvas.value) {
          ctx = gameCanvas.value.getContext('2d')
          gameCanvas.value.focus()
          startGameLoop()
        }
      })
    }
    
    // Go to start screen
    const goToStart = () => {
      stopGameLoop()
      gameState.value = 'start'
    }
    
    // Pause/resume game
    const pauseGame = () => {
      if (gameState.value === 'playing') {
        isPaused.value = !isPaused.value
        if (!isPaused.value) {
          gameCanvas.value.focus()
        }
      }
    }
    
    // Generate food at random position
    const generateFood = () => {
      let newFood
      do {
        newFood = {
          x: Math.floor(Math.random() * gridWidth),
          y: Math.floor(Math.random() * gridHeight)
        }
      } while (snake.value.some(segment => segment.x === newFood.x && segment.y === newFood.y))
      
      food.value = newFood
    }
    
    // Game loop
    const startGameLoop = () => {
      gameLoop = setInterval(() => {
        if (!isPaused.value) {
          updateGame()
          drawGame()
        }
      }, 150) // Game speed
    }
    
    const stopGameLoop = () => {
      if (gameLoop) {
        clearInterval(gameLoop)
        gameLoop = null
      }
    }
    
    // Update game logic
    const updateGame = () => {
      // Update direction
      direction.value = { ...nextDirection.value }
      
      // Don't move if no direction is set
      if (direction.value.x === 0 && direction.value.y === 0) return
      
      // Move snake
      const head = { ...snake.value[0] }
      head.x += direction.value.x
      head.y += direction.value.y
      
      // Check wall collision
      if (head.x < 0 || head.x >= gridWidth || head.y < 0 || head.y >= gridHeight) {
        endGame()
        return
      }
      
      // Check self collision
      if (snake.value.some(segment => segment.x === head.x && segment.y === head.y)) {
        endGame()
        return
      }
      
      snake.value.unshift(head)
      
      // Check food collision
      if (head.x === food.value.x && head.y === food.value.y) {
        score.value += 10
        generateFood()
      } else {
        snake.value.pop()
      }
    }
    
    // Draw game
    const drawGame = () => {
      if (!ctx) return
      
      // Clear canvas
      ctx.fillStyle = '#2c3e50'
      ctx.fillRect(0, 0, canvasWidth, canvasHeight)
      
      // Draw grid
      ctx.strokeStyle = '#34495e'
      ctx.lineWidth = 1
      for (let i = 0; i <= gridWidth; i++) {
        ctx.beginPath()
        ctx.moveTo(i * gridSize, 0)
        ctx.lineTo(i * gridSize, canvasHeight)
        ctx.stroke()
      }
      for (let i = 0; i <= gridHeight; i++) {
        ctx.beginPath()
        ctx.moveTo(0, i * gridSize)
        ctx.lineTo(canvasWidth, i * gridSize)
        ctx.stroke()
      }
      
      // Draw snake
      snake.value.forEach((segment, index) => {
        ctx.fillStyle = index === 0 ? '#27ae60' : '#2ecc71' // Head is darker
        ctx.fillRect(
          segment.x * gridSize + 1,
          segment.y * gridSize + 1,
          gridSize - 2,
          gridSize - 2
        )
        
        // Draw eyes on head
        if (index === 0) {
          ctx.fillStyle = '#000'
          ctx.beginPath()
          ctx.arc(segment.x * gridSize + 6, segment.y * gridSize + 6, 2, 0, Math.PI * 2)
          ctx.fill()
          ctx.beginPath()
          ctx.arc(segment.x * gridSize + 14, segment.y * gridSize + 6, 2, 0, Math.PI * 2)
          ctx.fill()
        }
      })
      
      // Draw food
      ctx.fillStyle = '#e74c3c'
      ctx.beginPath()
      ctx.arc(
        food.value.x * gridSize + gridSize / 2,
        food.value.y * gridSize + gridSize / 2,
        gridSize / 2 - 2,
        0,
        Math.PI * 2
      )
      ctx.fill()
    }
    
    // End game
    const endGame = () => {
      stopGameLoop()
      
      // Check for high score
      const currentHighScore = localStorage.getItem('snakeHighScore') || 0
      if (score.value > currentHighScore) {
        highScore.value = score.value
        localStorage.setItem('snakeHighScore', score.value)
        isNewHighScore.value = true
      }
      
      gameState.value = 'gameOver'
    }
    
    // Handle keyboard input
    const handleKeyPress = (event) => {
      if (gameState.value !== 'playing' || isPaused.value) return
      
      const key = event.key.toLowerCase()
      
      // Prevent reverse direction
      const canChangeDirection = (newDir) => {
        return !(direction.value.x === -newDir.x && direction.value.y === -newDir.y)
      }
      
      switch (key) {
        case 'arrowup':
        case 'w':
          event.preventDefault()
          if (canChangeDirection({ x: 0, y: -1 })) {
            nextDirection.value = { x: 0, y: -1 }
          }
          break
        case 'arrowdown':
        case 's':
          event.preventDefault()
          if (canChangeDirection({ x: 0, y: 1 })) {
            nextDirection.value = { x: 0, y: 1 }
          }
          break
        case 'arrowleft':
        case 'a':
          event.preventDefault()
          if (canChangeDirection({ x: -1, y: 0 })) {
            nextDirection.value = { x: -1, y: 0 }
          }
          break
        case 'arrowright':
        case 'd':
          event.preventDefault()
          if (canChangeDirection({ x: 1, y: 0 })) {
            nextDirection.value = { x: 1, y: 0 }
          }
          break
        case ' ':
        case 'p':
          event.preventDefault()
          pauseGame()
          break
      }
    }
    
    const handleFocus = () => {
      if (gameCanvas.value) {
        gameCanvas.value.focus()
      }
    }
    
    // Lifecycle hooks
    onMounted(() => {
      // Load high score
      highScore.value = parseInt(localStorage.getItem('snakeHighScore')) || 0
      
      // Add event listeners
      window.addEventListener('keydown', handleKeyPress)
    })
    
    onUnmounted(() => {
      stopGameLoop()
      window.removeEventListener('keydown', handleKeyPress)
    })
    
    return {
      gameCanvas,
      gameState,
      score,
      highScore,
      isNewHighScore,
      isPaused,
      canvasWidth,
      canvasHeight,
      startGame,
      goToStart,
      pauseGame,
      handleFocus
    }
  }
}
</script>

<style scoped>
.game-container {
  max-width: 600px;
  margin: 0 auto;
  padding: 20px;
  background: rgba(255, 255, 255, 0.1);
  border-radius: 20px;
  backdrop-filter: blur(10px);
  box-shadow: 0 8px 32px rgba(0, 0, 0, 0.3);
  text-align: center;
}

.game-header {
  margin-bottom: 20px;
}

.game-header h1 {
  color: white;
  font-size: 2.5rem;
  margin-bottom: 15px;
  text-shadow: 2px 2px 4px rgba(0, 0, 0, 0.3);
}

.score-board {
  display: flex;
  justify-content: space-around;
  color: white;
  font-size: 1.2rem;
  font-weight: bold;
}

.start-screen, .game-over-screen {
  color: white;
  padding: 40px 20px;
}

.start-screen h2, .game-over-screen h2 {
  font-size: 2rem;
  margin-bottom: 20px;
}

.start-screen p {
  font-size: 1.1rem;
  margin-bottom: 30px;
  opacity: 0.9;
}

.start-btn, .restart-btn, .menu-btn, .control-btn {
  background: linear-gradient(45deg, #3498db, #2980b9);
  color: white;
  border: none;
  padding: 12px 24px;
  font-size: 1.1rem;
  border-radius: 25px;
  cursor: pointer;
  transition: all 0.3s ease;
  margin: 10px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.2);
}

.start-btn:hover, .restart-btn:hover, .menu-btn:hover, .control-btn:hover {
  transform: translateY(-2px);
  box-shadow: 0 6px 20px rgba(0, 0, 0, 0.3);
}

.restart-btn {
  background: linear-gradient(45deg, #27ae60, #2ecc71);
}

.menu-btn {
  background: linear-gradient(45deg, #e74c3c, #c0392b);
}

.game-board-container {
  display: flex;
  flex-direction: column;
  align-items: center;
}

.game-board {
  border: 3px solid #34495e;
  border-radius: 10px;
  background: #2c3e50;
  margin-bottom: 20px;
  box-shadow: 0 4px 15px rgba(0, 0, 0, 0.3);
}

.game-board:focus {
  outline: 3px solid #3498db;
  outline-offset: 2px;
}

.game-controls {
  color: white;
}

.game-controls p {
  margin-bottom: 15px;
  opacity: 0.8;
}

.new-high-score {
  color: #f1c40f;
  font-weight: bold;
  font-size: 1.3rem;
  margin: 15px 0;
  animation: glow 1s infinite alternate;
}

@keyframes glow {
  from { text-shadow: 0 0 5px #f1c40f; }
  to { text-shadow: 0 0 20px #f1c40f, 0 0 30px #f1c40f; }
}

@media (max-width: 480px) {
  .game-container {
    margin: 10px;
    padding: 15px;
  }
  
  .game-header h1 {
    font-size: 2rem;
  }
  
  .score-board {
    font-size: 1rem;
  }
}
</style>