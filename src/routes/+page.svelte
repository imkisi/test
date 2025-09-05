<!--
                
| . _.| _ |_  _ 
|(|_)||(_||_)_) 
                
-->
<script lang="ts">
    import { onMount } from 'svelte';

    // --- Time Counter ---
    const startDate = new Date('August 28, 2025 10:30:00');
    let timeElapsed = '';
    let interval: ReturnType<typeof setInterval>;

    function updateTime() {
        const now = new Date();
        const elapsed = now.getTime() - startDate.getTime();
        const seconds = Math.floor(elapsed / 1000);
        const minutes = Math.floor(seconds / 60);
        const hours = Math.floor(minutes / 60);
        const microseconds = (elapsed % 1000) * 1000;

        const format = (num: number, digits: number) => num.toString().padStart(digits, '0');
        timeElapsed = `${format(hours, 2)}:${format(minutes % 60, 2)}:${format(seconds % 60, 2)}:${format(Math.floor(microseconds / 10000), 2)}`;
    }

    // --- Random Character Animation for About ---
    let aboutMeText = '';
    let aboutInterval: ReturnType<typeof setInterval>;
    const charset = 'ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789';

    function randomizeAboutText(length: number) {
        let text = '';
        for (let i = 0; i < length; i++) {
            text += charset[Math.floor(Math.random() * charset.length)];
        }
        return text;
    }

    function animateAboutText() {
        let currentLength = 1;
        aboutMeText = randomizeAboutText(currentLength);
        aboutInterval = setInterval(() => {
            currentLength++;
            if (currentLength > 5) {
                clearInterval(aboutInterval);
                aboutMeText = 'About';
            } else {
                aboutMeText = randomizeAboutText(currentLength);
            }
        }, 300);
    }

    onMount(() => {
        updateTime();
        interval = setInterval(updateTime, 10);
        animateAboutText();

        const canvas = document.getElementById('bg-canvas') as HTMLCanvasElement;
        if (canvas) {
            const ctx = canvas.getContext('2d');
            function resizeCanvas() {
                canvas.width = window.innerWidth;
                canvas.height = window.innerHeight;
            }
            resizeCanvas();
            window.addEventListener('resize', resizeCanvas);

            const squareSize = 40;
            const cols = Math.ceil(canvas.width / squareSize);
            const rows = Math.ceil(canvas.height / squareSize);

            let squares: {x: number, y: number, gone: boolean}[] = [];
            for (let y = 0; y < rows; y++) {
                for (let x = 0; x < cols; x++) {
                    squares.push({x, y, gone: false});
                }
            }

            function drawSquares() {
                if (!ctx) return; // Fix for ctx possibly null
                ctx.clearRect(0, 0, canvas.width, canvas.height);
                for (const sq of squares) {
                    ctx.fillStyle = sq.gone ? "rgba(252,252,252,0)" : "#3F4BEE";
                    ctx.fillRect(sq.x * squareSize, sq.y * squareSize, squareSize, squareSize);
                }
            }

            drawSquares();

            let animationFrame: number;
            let start = performance.now();
            function animateSquares(now: number) {
                if (now - start < 1000) {
                    for (let i = 0; i < Math.floor(squares.length / 10); i++) {
                        const idx = Math.floor(Math.random() * squares.length);
                        squares[idx].gone = true;
                    }
                    drawSquares();
                    animationFrame = requestAnimationFrame(animateSquares);
                } else {
                    for (const sq of squares) sq.gone = true;
                    drawSquares();
                    setTimeout(() => {
                        canvas.style.display = "none";
                    }, 200);
                }
            }
            animationFrame = requestAnimationFrame(animateSquares);

            // Cleanup function
            return () => {
                window.removeEventListener('resize', resizeCanvas);
                cancelAnimationFrame(animationFrame);
                clearInterval(interval);
                clearInterval(aboutInterval);
            };
        } else {
            // Cleanup for intervals if canvas not found
            return () => {
                clearInterval(interval);
                clearInterval(aboutInterval);
            };
        }
    });
</script>

<canvas id="bg-canvas"></canvas>
<div class="container">
    <h1 class="title">workspace/kisilabs</h1>
    <p class="time">
        Something in development<br>
        <span id="time-elapsed">{timeElapsed}</span> time exceeded
    </p>
    <p class="about" id="about-me">{aboutMeText}</p>
    <p class="bio">
        I am Bagas Dwi Anggoro known as KiSI, a computer science student specializing in <span>illustration</span>, <span>front-end development</span>, <span>motion graphics</span>, and <span>visual design</span>. I am driven by a desire to expand my knowledge and explore creative solutions.
    </p>
    <a href="mailto:hello@kisilabs.space" class="email">hello@kisilabs.space</a>
</div>

<div class="nav">
    <a href="https://kisi.is-a.dev" class="nav-link">see the past</a>
</div>

<style>
    @import url('https://fonts.googleapis.com/css2?family=JetBrains+Mono:wght@200;400;700&family=Poppins:wght@200;400;700&display=swap');

    :global(body) {
        margin: 0;
        padding: 0;
        font-family: 'Fira Code', monospace;
        background-color: #fcfcfc;
        /* background-image: url("data:image/svg+xml,%3Csvg xmlns='http://www.w3.org/2000/svg' viewBox='0 0 80 80' width='80' height='80'%3E%3Cg opacity='.2' fill='%23BDBDBD' stroke-width='1'%3E%3Cpath fill-rule='evenodd' d='M0 0h40v40H0V0zm40 40h40v40H40V40z'/%3E%3C/g%3E%3C/svg%3E");
        */
        background-repeat: repeat;
        color: #000;
        display: flex;
        justify-content: center;
        align-items: center;
        min-height: 100vh;
        box-sizing: border-box;
        text-align: center;
    }

    #bg-canvas {
        position: fixed;
        top: 0;
        left: 0;
        z-index: 0;
        width: 100vw;
        height: 100vh;
        pointer-events: none;
        background: transparent;
    }

    .container {
        width: 100%;
        max-width: 435px;
        padding: 24px;
        display: flex;
        flex-direction: column;
        text-align: start;
        justify-content: start;
        gap: 18px;
        box-sizing: border-box;
    }

    h1, p {
        margin: 0;
        line-height: 1.5;
    }

    span {
        color: #000 !important;
    }

    .title {
        font-size: 16px;
        color: #3913e8;
    }

    .time {
        font-size: 12px;
        color: #636363;
    }

    .about {
        font-size: 12px;
        color: #636363;
    }

    .bio {
        font-size: 12px;
        color: #636363;
        margin-top: -12px;
    }

    .email {
        font-size: 16px;
        color: #3913e8;
        text-decoration: none;
    }

    .email:hover {
        text-decoration: underline;
    }

    .nav {
        position: fixed;
        bottom: 0;
        left: 50%;
        transform: translateX(-50%);
        width: 100%;
        max-width: 435px;
        padding: 24px;
        text-align: start;
        box-sizing: border-box;
    }

    .nav-link {
        font-size: 12px;
        color: #3913e8;
        text-decoration: underline;
        cursor: pointer;
    }

    .nav-link:hover {
        text-decoration: underline;
        color: #7356f2;
    }
</style>