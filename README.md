# BadSanta
Lfg
(function() {
    // Настройки
    const CLICKS_PER_SECOND = 3;
    const MOUSE_MOVE_RADIUS = 30; // Пиксели вокруг начальной позиции
    
    let isRunning = true;
    let clickCount = 0;
    let initialX = null;
    let initialY = null;

    // Фиксируем начальную позицию курсора
    const updateInitialPosition = (e) => {
        initialX = e.clientX;
        initialY = e.clientY;
    };
    
    // Попытка получить текущие координаты
    document.addEventListener('mousemove', updateInitialPosition);
    setTimeout(() => {
        document.removeEventListener('mousemove', updateInitialPosition);
        if (initialX === null || initialY === null) {
            initialX = window.innerWidth / 2;
            initialY = window.innerHeight / 2;
            console.log('Не удалось определить позицию мыши. Начинаем от центра экрана');
        }
    }, 50);

    // Случайное смещение относительно начальной точки
    function getRandomOffset() {
        return (Math.random() * MOUSE_MOVE_RADIUS * 2) - MOUSE_MOVE_RADIUS;
    }

    // Имитация человеческого движения
    function humanMove(x, y) {
        const steps = 3;
        const stepX = (x - initialX) / steps;
        const stepY = (y - initialY) / steps;
        
        for (let i = 1; i <= steps; i++) {
            setTimeout(() => {
                const newX = initialX + stepX * i;
                const newY = initialY + stepY * i;
                document.dispatchEvent(new MouseEvent('mousemove', {
                    clientX: newX,
                    clientY: newY,
                    view: window
                }));
            }, i * 20);
        }
    }

    // Основной цикл кликов
    function autoClick() {
        if (!isRunning) return;

        const targetX = initialX + getRandomOffset();
        const targetY = initialY + getRandomOffset();
        
        humanMove(targetX, targetY);
        
        setTimeout(() => {
            const element = document.elementFromPoint(targetX, targetY);
            if (element) {
                element.dispatchEvent(new MouseEvent('click', {
                    clientX: targetX,
                    clientY: targetY,
                    bubbles: true,
                    view: window
                }));
                clickCount++;
                console.log(`Клик #${clickCount} на [${Math.round(targetX)}, ${Math.round(targetY)}]`);
            }
        }, 100);
    }

    // Запуск
    const interval = setInterval(autoClick, 1000 / CLICKS_PER_SECOND);
    
    console.log('Кликер активирован от позиции:', 
                `X: ${Math.round(initialX || window.innerWidth/2)},`,
                `Y: ${Math.round(initialY || window.innerHeight/2)}`);
    
    window.stopClicker = () => {
        clearInterval(interval);
        isRunning = false;
        console.log('Кликер остановлен');
    };
})();
