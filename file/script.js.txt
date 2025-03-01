// Lấy phần tử canvas và tạo ngữ cảnh vẽ
const canvas = document.getElementById('gameCanvas');
const ctx = canvas.getContext('2d');

// Kích thước mỗi ô trong game
const gridSize = 20;

// Tạo rắn ban đầu
let snake = [{ x: 160, y: 160 }];
let snakeDirection = 'RIGHT';

// Tạo thức ăn
let food = { x: 80, y: 80 };

// Điểm số
let score = 0;

// Thực thi game mỗi 100ms
setInterval(gameLoop, 100);

function gameLoop() {
    // Di chuyển rắn
    moveSnake();
    
    // Kiểm tra va chạm với tường
    if (checkWallCollision()) {
        resetGame();
    }

    // Kiểm tra va chạm với chính mình
    if (checkSelfCollision()) {
        resetGame();
    }

    // Kiểm tra ăn thức ăn
    if (checkFoodCollision()) {
        score++;
        generateFood();
    }

    // Vẽ lại màn hình
    drawGame();
}

function moveSnake() {
    const head = { ...snake[0] };

    if (snakeDirection === 'RIGHT') head.x += gridSize;
    if (snakeDirection === 'LEFT') head.x -= gridSize;
    if (snakeDirection === 'UP') head.y -= gridSize;
    if (snakeDirection === 'DOWN') head.y += gridSize;

    snake.unshift(head);
    snake.pop();
}

function checkWallCollision() {
    const head = snake[0];
    return head.x < 0 || head.x >= canvas.width || head.y < 0 || head.y >= canvas.height;
}

function checkSelfCollision() {
    const head = snake[0];
    return snake.slice(1).some(segment => segment.x === head.x && segment.y === head.y);
}

function checkFoodCollision() {
    const head = snake[0];
    return head.x === food.x && head.y === food.y;
}

function generateFood() {
    const x = Math.floor(Math.random() * (canvas.width / gridSize)) * gridSize;
    const y = Math.floor(Math.random() * (canvas.height / gridSize)) * gridSize;
    food = { x, y };
}

function drawGame() {
    ctx.clearRect(0, 0, canvas.width, canvas.height);
    
    // Vẽ rắn
    snake.forEach((segment, index) => {
        ctx.fillStyle = index === 0 ? 'green' : 'black';
        ctx.fillRect(segment.x, segment.y, gridSize, gridSize);
    });

    // Vẽ thức ăn
    ctx.fillStyle = 'red';
    ctx.fillRect(food.x, food.y, gridSize, gridSize);

    // Vẽ điểm số
    ctx.fillStyle = 'black';
    ctx.font = '20px Arial';
    ctx.fillText(`Score: ${score}`, 10, 20);
}

// Điều khiển rắn bằng các phím mũi tên
document.addEventListener('keydown', (e) => {
    if (e.key === 'ArrowUp' && snakeDirection !== 'DOWN') {
        snakeDirection = 'UP';
    } else if (e.key === 'ArrowDown' && snakeDirection !== 'UP') {
        snakeDirection = 'DOWN';
    } else if (e.key === 'ArrowLeft' && snakeDirection !== 'RIGHT') {
        snakeDirection = 'LEFT';
    } else if (e.key === 'ArrowRight' && snakeDirection !== 'LEFT') {
        snakeDirection = 'RIGHT';
    }
});

function resetGame() {
    snake = [{ x: 160, y: 160 }];
    snakeDirection = 'RIGHT';
    score = 0;
    generateFood();
}

