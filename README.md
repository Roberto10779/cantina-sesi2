<!DOCTYPE html>
<html lang="pt-BR">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>SESI Lanches - Pedidos Antecipados</title>
    <!-- Tailwind CSS CDN -->
    <script src="https://cdn.tailwindcss.com"></script>
    <!-- FontAwesome Icons -->
    <link rel="stylesheet" href="https://cdnjs.cloudflare.com/ajax/libs/font-awesome/6.4.0/css/all.min.css">
    <!-- Google Fonts Inter -->
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@300;400;500;600;700;800&display=swap" rel="stylesheet">

    <script>
        tailwind.config = {
            theme: {
                extend: {
                    colors: {
                        sesi: {
                            red: '#E30613',
                            blue: '#005CA9',
                            darkBlue: '#003A6C',
                            gray: '#F3F4F6'
                        }
                    },
                    fontFamily: {
                        sans: ['Inter', 'sans-serif'],
                    }
                }
            }
        }
    </script>
    <style>
        /* Custom styling for sleek UI elements */
        .glass-header {
            background: rgba(255, 255, 255, 0.95);
            backdrop-filter: blur(8px);
        }
        .bottom-sheet {
            transition: transform 0.3s ease-in-out;
        }
        .no-scrollbar::-webkit-scrollbar {
            display: none;
        }
        .no-scrollbar {
            -ms-overflow-style: none;
            scrollbar-width: none;
        }
        .pulse-subtle {
            animation: pulse-border 2s infinite;
        }
        @keyframes pulse-border {
            0% { border-color: rgba(227, 6, 19, 0.4); }
            50% { border-color: rgba(227, 6, 19, 1); }
            100% { border-color: rgba(227, 6, 19, 0.4); }
        }
    </style>
</head>
<body class="bg-gray-100 font-sans text-gray-800 min-h-screen flex flex-col">

    <!-- Top Navigation Bar & View Switcher -->
    <header class="bg-sesi-darkBlue text-white sticky top-0 z-40 shadow-md">
        <div class="max-w-6xl mx-auto px-4 py-3 flex flex-wrap justify-between items-center gap-3">
            <!-- Logo & Title -->
            <div class="flex items-center space-x-3">
                <div class="bg-sesi-red text-white font-extrabold text-xl px-3 py-1 rounded-lg tracking-wider shadow">
                    SESI
                </div>
                <div>
                    <h1 class="font-bold text-lg leading-tight">Cantina Express</h1>
                    <p class="text-xs text-blue-200">Intervalo 15:30 às 15:50</p>
                </div>
            </div>

            <!-- View Switcher Tabs -->
            <div class="bg-blue-950 p-1 rounded-xl flex items-center space-x-1 border border-blue-800">
                <button id="btn-view-student" onclick="switchView('student')" 
                    class="px-4 py-2 rounded-lg text-xs font-semibold flex items-center space-x-2 transition-all bg-sesi-red text-white shadow">
                    <i class="fa-solid fa-user-graduate"></i>
                    <span>Visão Aluno</span>
                </button>
                <button id="btn-view-canteen" onclick="switchView('canteen')" 
                    class="px-4 py-2 rounded-lg text-xs font-semibold flex items-center space-x-2 transition-all text-blue-200 hover:text-white">
                    <i class="fa-solid fa-store"></i>
                    <span>Painel Cantina (KDS)</span>
                    <span id="badge-pending" class="bg-sesi-red text-white text-[10px] px-1.5 py-0.5 rounded-full font-bold ml-1 hidden">0</span>
                </button>
            </div>
        </div>
    </header>

    <!-- Global App Notice (Cut-off Time Status) -->
    <div id="cutoff-banner" class="bg-amber-500 text-amber-950 px-4 py-2 text-xs font-medium text-center shadow-inner flex items-center justify-center space-x-2">
        <i class="fa-solid fa-clock"></i>
        <span>Horário limite para pedidos no intervalo das 15:30: <strong class="underline">Até as 15:00</strong>. Faltam <span id="cutoff-timer" class="font-bold">35 min</span> para o fechamento da cozinha!</span>
    </div>

    <main class="flex-1 max-w-6xl w-full mx-auto p-3 sm:p-6">

        <!-- ==================== VISÃO DO ALUNO ==================== -->
        <section id="view-student" class="block max-w-md mx-auto space-y-4">
            
            <!-- User Profile & Balance Card -->
            <div class="bg-white rounded-2xl p-4 shadow-sm border border-gray-200 flex justify-between items-center">
                <div class="flex items-center space-x-3">
                    <div class="w-11 h-11 rounded-full bg-blue-100 text-sesi-blue flex items-center justify-center font-bold text-lg border border-blue-200">
                        EA
                    </div>
                    <div>
                        <h2 class="font-bold text-gray-900 leading-tight">Erick Alves</h2>
                        <p class="text-xs text-gray-500">SESI High School • 3º Ano B</p>
                    </div>
                </div>
                <div class="text-right">
                    <span class="text-[10px] uppercase font-bold text-gray-400 block tracking-wider">Saldo Expresso</span>
                    <span class="text-lg font-extrabold text-emerald-600">R$ <span id="student-balance">35.50</span></span>
                </div>
            </div>

            <!-- Active Order Card (If any active) -->
            <div id="active-order-banner" class="hidden">
                <!-- Rendered dynamically via JS -->
            </div>

            <!-- Menu Category Filter -->
            <div class="flex space-x-2 overflow-x-auto no-scrollbar py-1">
                <button onclick="filterCategory('all')" class="cat-btn active px-4 py-2 bg-sesi-blue text-white rounded-xl text-xs font-semibold whitespace-nowrap shadow-sm transition">
                    🔥 Todos
                </button>
                <button onclick="filterCategory('salgados')" class="cat-btn px-4 py-2 bg-white text-gray-600 hover:bg-gray-100 rounded-xl text-xs font-semibold whitespace-nowrap shadow-sm transition border border-gray-200">
                    🥐 Salgados
                </button>
                <button onclick="filterCategory('bebidas')" class="cat-btn px-4 py-2 bg-white text-gray-600 hover:bg-gray-100 rounded-xl text-xs font-semibold whitespace-nowrap shadow-sm transition border border-gray-200">
                    🧃 Bebidas
                </button>
                <button onclick="filterCategory('saudavel')" class="cat-btn px-4 py-2 bg-white text-gray-600 hover:bg-gray-100 rounded-xl text-xs font-semibold whitespace-nowrap shadow-sm transition border border-gray-200">
                    🍎 Saudável
                </button>
                <button onclick="filterCategory('doces')" class="cat-btn px-4 py-2 bg-white text-gray-600 hover:bg-gray-100 rounded-xl text-xs font-semibold whitespace-nowrap shadow-sm transition border border-gray-200">
                    🍫 Doces
                </button>
            </div>

            <!-- Products Grid -->
            <div id="products-grid" class="grid grid-cols-1 gap-3">
                <!-- Products dynamically inserted via JavaScript -->
            </div>

            <!-- Floating Cart Trigger Button -->
            <div id="floating-cart-bar" class="fixed bottom-4 left-0 right-0 max-w-md mx-auto px-4 z-30 transition-transform duration-300 transform translate-y-32">
                <button onclick="toggleCartModal(true)" class="w-full bg-sesi-red text-white p-4 rounded-2xl shadow-xl flex justify-between items-center font-semibold hover:bg-red-700 transition">
                    <div class="flex items-center space-x-3">
                        <div class="bg-white/20 px-2.5 py-1 rounded-lg text-sm">
                            <span id="cart-count">0</span> itens
                        </div>
                        <span class="text-sm">Ver Carrinho</span>
                    </div>
                    <div class="text-base font-bold">
                        R$ <span id="cart-total-floating">0.00</span>
                        <i class="fa-solid fa-chevron-right ml-2 text-xs"></i>
                    </div>
                </button>
            </div>

        </section>

        <!-- ==================== VISÃO DA CANTINA (KDS) ==================== -->
        <section id="view-canteen" class="hidden space-y-6">
            
            <!-- Dashboard Metrics -->
            <div class="grid grid-cols-2 md:grid-cols-4 gap-4">
                <div class="bg-white p-4 rounded-2xl border border-gray-200 shadow-sm flex items-center space-x-3">
                    <div class="w-12 h-12 bg-amber-100 text-amber-600 rounded-xl flex items-center justify-center font-bold text-xl">
                        <i class="fa-solid fa-receipt"></i>
                    </div>
                    <div>
                        <p class="text-xs text-gray-500 font-semibold uppercase">Pendentes</p>
                        <h3 id="metric-pending" class="text-2xl font-black text-gray-800">0</h3>
                    </div>
                </div>

                <div class="bg-white p-4 rounded-2xl border border-gray-200 shadow-sm flex items-center space-x-3">
                    <div class="w-12 h-12 bg-blue-100 text-sesi-blue rounded-xl flex items-center justify-center font-bold text-xl">
                        <i class="fa-solid fa-fire"></i>
                    </div>
                    <div>
                        <p class="text-xs text-gray-500 font-semibold uppercase">Em Preparo</p>
                        <h3 id="metric-preparing" class="text-2xl font-black text-gray-800">0</h3>
                    </div>
                </div>

                <div class="bg-white p-4 rounded-2xl border border-gray-200 shadow-sm flex items-center space-x-3">
                    <div class="w-12 h-12 bg-emerald-100 text-emerald-600 rounded-xl flex items-center justify-center font-bold text-xl">
                        <i class="fa-solid fa-circle-check"></i>
                    </div>
                    <div>
                        <p class="text-xs text-gray-500 font-semibold uppercase">Prontos</p>
                        <h3 id="metric-ready" class="text-2xl font-black text-gray-800">0</h3>
                    </div>
                </div>

                <div class="bg-white p-4 rounded-2xl border border-gray-200 shadow-sm flex items-center space-x-3">
                    <div class="w-12 h-12 bg-purple-100 text-purple-600 rounded-xl flex items-center justify-center font-bold text-xl">
                        <i class="fa-solid fa-bag-shopping"></i>
                    </div>
                    <div>
                        <p class="text-xs text-gray-500 font-semibold uppercase">Entregues</p>
                        <h3 id="metric-completed" class="text-2xl font-black text-gray-800">0</h3>
                    </div>
                </div>
            </div>

            <!-- Orders KDS Columns -->
            <div>
                <div class="flex justify-between items-center mb-4">
                    <h2 class="text-xl font-bold text-gray-900 flex items-center space-x-2">
                        <i class="fa-solid fa-list-check text-sesi-red"></i>
                        <span>Fila de Produção - Retirada 15:30</span>
                    </h2>
                    <button onclick="clearCompletedOrders()" class="text-xs text-gray-500 hover:text-red-600 font-medium">
                        <i class="fa-solid fa-trash-can mr-1"></i> Limpar Concluídos
                    </button>
                </div>

                <div id="kds-orders-container" class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-3 gap-4">
                    <!-- Orders cards rendered dynamically -->
                </div>
            </div>

        </section>

    </main>

    <!-- Modal / Drawer Carrinho -->
    <div id="cart-modal" class="fixed inset-0 z-50 hidden">
        <!-- Backdrop -->
        <div onclick="toggleCartModal(false)" class="absolute inset-0 bg-black/60 backdrop-blur-sm"></div>

        <!-- Sliding Bottom Sheet / Modal Box -->
        <div class="absolute bottom-0 left-0 right-0 md:relative md:top-1/2 md:-translate-y-1/2 max-w-lg mx-auto bg-white rounded-t-3xl md:rounded-3xl p-5 shadow-2xl flex flex-col max-h-[90vh]">
            
            <!-- Modal Header -->
            <div class="flex justify-between items-center pb-3 border-b border-gray-100">
                <div class="flex items-center space-x-2">
                    <i class="fa-solid fa-basket-shopping text-sesi-red text-xl"></i>
                    <h3 class="font-bold text-lg text-gray-800">Seu Pedido Prévia</h3>
                </div>
                <button onclick="toggleCartModal(false)" class="text-gray-400 hover:text-gray-600 w-8 h-8 rounded-full bg-gray-100 flex items-center justify-center">
                    <i class="fa-solid fa-xmark"></i>
                </button>
            </div>

            <!-- Cart Items Scroll List -->
            <div id="cart-items-list" class="overflow-y-auto py-4 space-y-3 flex-1 max-h-60 no-scrollbar">
                <!-- Cart items inserted via JS -->
            </div>

            <!-- Payment Method Selection -->
            <div class="pt-3 border-t border-gray-100 space-y-3">
                <span class="text-xs font-bold text-gray-500 uppercase tracking-wider block">Forma de Pagamento</span>
                <div class="grid grid-cols-3 gap-2">
                    <label class="border border-gray-200 rounded-xl p-2.5 flex flex-col items-center justify-center text-center cursor-pointer hover:border-sesi-blue bg-gray-50 text-xs font-medium space-y-1">
                        <input type="radio" name="payment" value="saldo" checked class="hidden peer">
                        <i class="fa-solid fa-wallet text-emerald-600 text-lg"></i>
                        <span class="peer-checked:font-bold">Saldo Aluno</span>
                    </label>
                    <label class="border border-gray-200 rounded-xl p-2.5 flex flex-col items-center justify-center text-center cursor-pointer hover:border-sesi-blue bg-gray-50 text-xs font-medium space-y-1">
                        <input type="radio" name="payment" value="pix" class="hidden peer">
                        <i class="fa-brands fa-pix text-teal-600 text-lg"></i>
                        <span class="peer-checked:font-bold">Pix Instantâneo</span>
                    </label>
                    <label class="border border-gray-200 rounded-xl p-2.5 flex flex-col items-center justify-center text-center cursor-pointer hover:border-sesi-blue bg-gray-50 text-xs font-medium space-y-1">
                        <input type="radio" name="payment" value="cartao" class="hidden peer">
                        <i class="fa-solid fa-credit-card text-blue-600 text-lg"></i>
                        <span class="peer-checked:font-bold">Cartão Cantina</span>
                    </label>
                </div>

                <!-- Summary Total -->
                <div class="flex justify-between items-center py-2 text-sm font-bold border-t border-gray-100">
                    <span class="text-gray-600">Total a pagar:</span>
                    <span class="text-xl text-sesi-red">R$ <span id="cart-total-modal">0.00</span></span>
                </div>

                <!-- Submit Button -->
                <button onclick="confirmOrder()" class="w-full bg-sesi-red text-white py-3.5 rounded-xl font-bold text-base hover:bg-red-700 shadow-lg transition flex items-center justify-center space-x-2">
                    <i class="fa-solid fa-check-circle"></i>
                    <span>Confirmar e Reservar Lanche</span>
                </button>
            </div>

        </div>
    </div>

    <!-- QR Code / Pickup Ticket Modal -->
    <div id="ticket-modal" class="fixed inset-0 z-50 hidden flex items-center justify-center p-4">
        <div onclick="toggleTicketModal(false)" class="absolute inset-0 bg-black/70 backdrop-blur-sm"></div>
        
        <div class="relative bg-white rounded-3xl p-6 max-w-sm w-full shadow-2xl text-center space-y-4">
            <button onclick="toggleTicketModal(false)" class="absolute top-4 right-4 text-gray-400 hover:text-gray-600">
                <i class="fa-solid fa-xmark text-lg"></i>
            </button>

            <div class="inline-block p-3 bg-emerald-100 text-emerald-600 rounded-full">
                <i class="fa-solid fa-qrcode text-3xl"></i>
            </div>

            <div>
                <span class="text-xs font-bold text-gray-400 uppercase tracking-widest">Comprovante de Retirada</span>
                <h3 id="ticket-number" class="text-3xl font-black text-gray-900">#1042</h3>
                <p class="text-xs text-gray-500 mt-1">Apresente este código no Balcão Expresso às 15:30</p>
            </div>

            <!-- Simulated QR Code -->
            <div class="bg-gray-50 p-4 rounded-2xl border-2 border-dashed border-gray-200 inline-block">
                <img src="https://api.qrserver.com/v1/create-qr-code/?size=150x150&data=SESI-RETIRADA-1042" alt="QR Code Retirada" class="w-36 h-36 mx-auto rounded-lg">
            </div>

            <div class="bg-blue-50 p-3 rounded-xl border border-blue-100 text-left text-xs space-y-1">
                <div class="flex justify-between text-gray-600">
                    <span>Aluno:</span>
                    <strong class="text-gray-800">Erick Alves</strong>
                </div>
                <div class="flex justify-between text-gray-600">
                    <span>Horário Retirada:</span>
                    <strong class="text-sesi-blue">15:30 - Balcão 02</strong>
                </div>
            </div>

            <button onclick="toggleTicketModal(false)" class="w-full bg-gray-900 text-white py-2.5 rounded-xl text-sm font-semibold hover:bg-gray-800">
                Fechar Ficha
            </button>
        </div>
    </div>

    <script>
        // ==================== APP STATE & DATA ====================
        const PRODUCTS = [
            { id: 1, name: 'Folhado de Frango c/ Catupiry', category: 'salgados', price: 7.50, prepTime: 'Pronto', img: 'https://images.unsplash.com/photo-1621263764928-df1444c5e859?auto=format&fit=crop&w=400&q=80' },
            { id: 2, name: 'Pão de Queijo Recheado', category: 'salgados', price: 5.00, prepTime: 'Pronto', img: 'https://images.unsplash.com/photo-1598103442097-8b74394b95c6?auto=format&fit=crop&w=400&q=80' },
            { id: 3, name: 'Assado de Presunto e Queijo', category: 'salgados', price: 6.50, prepTime: 'Pronto', img: 'https://images.unsplash.com/photo-1509722747041-616f39b57569?auto=format&fit=crop&w=400&q=80' },
            { id: 4, name: 'Suco Natural Laranja (400ml)', category: 'bebidas', price: 6.00, prepTime: 'Fresco', img: 'https://images.unsplash.com/photo-1613478223719-2ab802602423?auto=format&fit=crop&w=400&q=80' },
            { id: 5, name: 'Achocolatado Toddynho', category: 'bebidas', price: 4.00, prepTime: 'Gelado', img: 'https://images.unsplash.com/photo-1550583724-b2692b85b150?auto=format&fit=crop&w=400&q=80' },
            { id: 6, name: 'Salada de Frutas c/ Mel', category: 'saudavel', price: 7.00, prepTime: 'Fresco', img: 'https://images.unsplash.com/photo-1568158879083-c44f89252488?auto=format&fit=crop&w=400&q=80' },
            { id: 7, name: 'Sanduíche Natural de Peito de Peru', category: 'saudavel', price: 8.50, prepTime: 'Fresco', img: 'https://images.unsplash.com/photo-1528735602780-2552fd46c7af?auto=format&fit=crop&w=400&q=80' },
            { id: 8, name: 'Brownie de Chocolate', category: 'doces', price: 5.50, prepTime: 'Pronto', img: 'https://images.unsplash.com/photo-1606313564200-e75d5e30476c?auto=format&fit=crop&w=400&q=80' }
        ];

        let state = {
            currentView: 'student', // 'student' | 'canteen'
            studentBalance: 35.50,
            cart: [], // { productId, qty }
            orders: [
                {
                    id: 1041,
                    studentName: 'Mariana Lima',
                    studentClass: '2º Ano A',
                    items: [
                        { name: 'Pão de Queijo Recheado', qty: 2, price: 5.00 },
                        { name: 'Suco Natural Laranja', qty: 1, price: 6.00 }
                    ],
                    total: 16.00,
                    status: 'preparing', // 'received' | 'preparing' | 'ready' | 'completed'
                    time: '14:22',
                    pickupTime: '15:30'
                }
            ],
            selectedCategory: 'all'
        };

        // Init App on Page Load
        window.onload = function() {
            loadLocalState();
            renderProducts();
            renderCart();
            renderKDS();
            updateMetrics();
        };

        // State Persistence in LocalStorage
        function saveLocalState() {
            localStorage.setItem('sesi_canteen_orders', JSON.stringify(state.orders));
            localStorage.setItem('sesi_canteen_balance', state.studentBalance.toString());
        }

        function loadLocalState() {
            const savedOrders = localStorage.getItem('sesi_canteen_orders');
            const savedBalance = localStorage.getItem('sesi_canteen_balance');
            
            if (savedOrders) {
                try { state.orders = JSON.parse(savedOrders); } catch(e) {}
            }
            if (savedBalance) {
                state.studentBalance = parseFloat(savedBalance);
                document.getElementById('student-balance').innerText = state.studentBalance.toFixed(2);
            }
        }

        // View Switcher logic
        function switchView(view) {
            state.currentView = view;
            const studentSec = document.getElementById('view-student');
            const canteenSec = document.getElementById('view-canteen');
            const btnStudent = document.getElementById('btn-view-student');
            const btnCanteen = document.getElementById('btn-view-canteen');

            if (view === 'student') {
                studentSec.classList.remove('hidden');
                canteenSec.classList.add('hidden');
                
                btnStudent.className = "px-4 py-2 rounded-lg text-xs font-semibold flex items-center space-x-2 transition-all bg-sesi-red text-white shadow";
                btnCanteen.className = "px-4 py-2 rounded-lg text-xs font-semibold flex items-center space-x-2 transition-all text-blue-200 hover:text-white";
                renderActiveStudentOrder();
            } else {
                studentSec.classList.add('hidden');
                canteenSec.classList.remove('hidden');

                btnCanteen.className = "px-4 py-2 rounded-lg text-xs font-semibold flex items-center space-x-2 transition-all bg-sesi-red text-white shadow";
                btnStudent.className = "px-4 py-2 rounded-lg text-xs font-semibold flex items-center space-x-2 transition-all text-blue-200 hover:text-white";
                renderKDS();
            }
        }

        // Category Filter
        function filterCategory(cat) {
            state.selectedCategory = cat;
            
            // Highlight active filter button
            document.querySelectorAll('.cat-btn').forEach(btn => {
                btn.className = "cat-btn px-4 py-2 bg-white text-gray-600 hover:bg-gray-100 rounded-xl text-xs font-semibold whitespace-nowrap shadow-sm transition border border-gray-200";
            });
            event.currentTarget.className = "cat-btn active px-4 py-2 bg-sesi-blue text-white rounded-xl text-xs font-semibold whitespace-nowrap shadow-sm transition";

            renderProducts();
        }

        // Render Products List
        function renderProducts() {
            const container = document.getElementById('products-grid');
            container.innerHTML = '';

            const filtered = state.selectedCategory === 'all' 
                ? PRODUCTS 
                : PRODUCTS.filter(p => p.category === state.selectedCategory);

            filtered.forEach(p => {
                const inCart = state.cart.find(item => item.productId === p.id);
                const qty = inCart ? inCart.qty : 0;

                const card = document.createElement('div');
                card.className = "bg-white rounded-2xl p-3 shadow-sm border border-gray-200 flex items-center space-x-3 hover:border-gray-300 transition";
                card.innerHTML = `
                    <img src="${p.img}" alt="${p.name}" class="w-20 h-20 rounded-xl object-cover bg-gray-100 flex-shrink-0">
                    <div class="flex-1 min-w-0">
                        <span class="text-[10px] bg-blue-50 text-sesi-blue px-2 py-0.5 rounded-full font-semibold inline-block mb-1">
                            ⏱️ ${p.prepTime}
                        </span>
                        <h3 class="font-bold text-gray-800 text-sm leading-snug truncate">${p.name}</h3>
                        <p class="text-sesi-red font-extrabold text-sm mt-1">R$ ${p.price.toFixed(2)}</p>
                    </div>
                    <div class="flex items-center space-x-1 bg-gray-50 p-1 rounded-xl border border-gray-200">
                        ${qty > 0 ? `
                            <button onclick="updateCart(${p.id}, -1)" class="w-7 h-7 bg-white text-gray-700 rounded-lg shadow-sm font-bold flex items-center justify-center hover:bg-gray-100">-</button>
                            <span class="w-6 text-center text-xs font-bold text-gray-800">${qty}</span>
                        ` : ''}
                        <button onclick="updateCart(${p.id}, 1)" class="w-7 h-7 bg-sesi-blue text-white rounded-lg shadow-sm font-bold flex items-center justify-center hover:bg-blue-800">+</button>
                    </div>
                `;
                container.appendChild(card);
            });
        }

        // Cart State Modification
        function updateCart(productId, delta) {
            const itemIndex = state.cart.findIndex(i => i.productId === productId);

            if (itemIndex > -1) {
                state.cart[itemIndex].qty += delta;
                if (state.cart[itemIndex].qty <= 0) {
                    state.cart.splice(itemIndex, 1);
                }
            } else if (delta > 0) {
                state.cart.push({ productId, qty: 1 });
            }

            renderProducts();
            renderCart();
        }

        // Render Cart Drawer
        function renderCart() {
            const cartList = document.getElementById('cart-items-list');
            const floatingBar = document.getElementById('floating-cart-bar');
            
            let total = 0;
            let totalCount = 0;

            cartList.innerHTML = '';

            if (state.cart.length === 0) {
                cartList.innerHTML = `
                    <div class="text-center py-8 text-gray-400">
                        <i class="fa-solid fa-basket-shopping text-4xl mb-2 text-gray-300"></i>
                        <p class="text-xs">Seu carrinho está vazio.</p>
                    </div>
                `;
                floatingBar.classList.add('translate-y-32');
            } else {
                floatingBar.classList.remove('translate-y-32');

                state.cart.forEach(item => {
                    const prod = PRODUCTS.find(p => p.id === item.productId);
                    const subtotal = prod.price * item.qty;
                    total += subtotal;
                    totalCount += item.qty;

                    const row = document.createElement('div');
                    row.className = "flex justify-between items-center bg-gray-50 p-3 rounded-xl";
                    row.innerHTML = `
                        <div class="flex-1 min-w-0 pr-2">
                            <h4 class="font-bold text-xs text-gray-800 truncate">${prod.name}</h4>
                            <span class="text-[11px] text-gray-500">R$ ${prod.price.toFixed(2)} cada</span>
                        </div>
                        <div class="flex items-center space-x-3">
                            <div class="flex items-center space-x-1.5 bg-white border border-gray-200 rounded-lg p-0.5">
                                <button onclick="updateCart(${prod.id}, -1)" class="w-5 h-5 bg-gray-100 rounded text-xs font-bold text-gray-700 flex items-center justify-center">-</button>
                                <span class="text-xs font-bold text-gray-800 px-1">${item.qty}</span>
                                <button onclick="updateCart(${prod.id}, 1)" class="w-5 h-5 bg-sesi-blue text-white rounded text-xs font-bold flex items-center justify-center">+</button>
                            </div>
                            <span class="font-bold text-xs text-gray-900 min-w-[50px] text-right">R$ ${subtotal.toFixed(2)}</span>
                        </div>
                    `;
                    cartList.appendChild(row);
                });
            }

            document.getElementById('cart-count').innerText = totalCount;
            document.getElementById('cart-total-floating').innerText = total.toFixed(2);
            document.getElementById('cart-total-modal').innerText = total.toFixed(2);
        }

        function toggleCartModal(show) {
            const modal = document.getElementById('cart-modal');
            if (show) {
                if (state.cart.length === 0) return;
                modal.classList.remove('hidden');
            } else {
                modal.classList.add('hidden');
            }
        }

        function toggleTicketModal(show) {
            const modal = document.getElementById('ticket-modal');
            if (show) {
                modal.classList.remove('hidden');
            } else {
                modal.classList.add('hidden');
            }
        }

        // Confirm Order (Student)
        function confirmOrder() {
            let total = 0;
            const items = state.cart.map(i => {
                const p = PRODUCTS.find(prod => prod.id === i.productId);
                total += p.price * i.qty;
                return { name: p.name, qty: i.qty, price: p.price };
            });

            if (state.studentBalance < total) {
                alert("Saldo insuficiente na carteira do aluno! Recarregue no balcão ou use Pix.");
                return;
            }

            // Deduct balance
            state.studentBalance -= total;
            document.getElementById('student-balance').innerText = state.studentBalance.toFixed(2);

            // Create new Order
            const newOrderId = Math.floor(1000 + Math.random() * 9000);
            const now = new Date();
            const timeStr = `${String(now.getHours()).padStart(2,'0')}:${String(now.getMinutes()).padStart(2,'0')}`;

            const newOrder = {
                id: newOrderId,
                studentName: 'Erick Alves',
                studentClass: '3º Ano B',
                items: items,
                total: total,
                status: 'received',
                time: timeStr,
                pickupTime: '15:30'
            };

            state.orders.unshift(newOrder);
            state.cart = [];

            saveLocalState();
            renderCart();
            renderProducts();
            toggleCartModal(false);

            // Show confirmation & open active ticket banner
            renderActiveStudentOrder();
            
            // Show QR Ticket modal directly
            document.getElementById('ticket-number').innerText = `#${newOrderId}`;
            toggleTicketModal(true);
            updateMetrics();
        }

        // Render Active Order Status for Student
        function renderActiveStudentOrder() {
            const container = document.getElementById('active-order-banner');
            
            // Get last order created by Erick Alves
            const activeOrder = state.orders.find(o => o.studentName === 'Erick Alves' && o.status !== 'completed');

            if (!activeOrder) {
                container.classList.add('hidden');
                return;
            }

            container.classList.remove('hidden');

            const statusConfig = {
                'received': { text: 'Pedido Recebido', color: 'bg-amber-500', step: 1 },
                'preparing': { text: 'Em Preparação', color: 'bg-blue-600', step: 2 },
                'ready': { text: 'Pronto p/ Retirada no Balcão 02!', color: 'bg-emerald-600', step: 3 }
            };

            const current = statusConfig[activeOrder.status];

            container.innerHTML = `
                <div class="bg-gradient-to-r from-gray-900 to-blue-950 text-white rounded-2xl p-4 shadow-lg border border-blue-800 space-y-3">
                    <div class="flex justify-between items-start">
                        <div>
                            <span class="text-[10px] uppercase font-bold text-blue-300 tracking-wider">Ficha Ativa</span>
                            <h3 class="text-xl font-black">Pedido #${activeOrder.id}</h3>
                        </div>
                        <button onclick="toggleTicketModal(true)" class="bg-white/10 hover:bg-white/20 text-xs px-3 py-1.5 rounded-xl border border-white/20 flex items-center space-x-1.5">
                            <i class="fa-solid fa-qrcode"></i>
                            <span>Ver QR Code</span>
                        </button>
                    </div>

                    <!-- Status Progress Line -->
                    <div class="space-y-1">
                        <div class="flex justify-between text-xs font-semibold">
                            <span class="${current.step >= 1 ? 'text-amber-400' : 'text-gray-500'}">Recebido</span>
                            <span class="${current.step >= 2 ? 'text-blue-400' : 'text-gray-500'}">Em Preparo</span>
                            <span class="${current.step >= 3 ? 'text-emerald-400 font-bold' : 'text-gray-500'}">Pronto!</span>
                        </div>
                        <div class="w-full bg-gray-800 h-2 rounded-full overflow-hidden flex">
                            <div class="h-full bg-emerald-500 transition-all duration-500" style="width: ${current.step === 1 ? '33%' : current.step === 2 ? '66%' : '100%'}"></div>
                        </div>
                    </div>

                    <div class="bg-white/5 p-2.5 rounded-xl flex justify-between items-center text-xs">
                        <span class="text-gray-300">Retirada Prevista: <strong>15:30</strong></span>
                        <span class="${current.color} px-2.5 py-1 rounded-lg text-[11px] font-bold shadow-sm">
                            ${current.text}
                        </span>
                    </div>
                </div>
            `;
        }

        // Render KDS (Canteen Kitchen View)
        function renderKDS() {
            const container = document.getElementById('kds-orders-container');
            container.innerHTML = '';

            if (state.orders.length === 0) {
                container.innerHTML = `
                    <div class="col-span-full text-center py-12 bg-white rounded-2xl border border-gray-200">
                        <i class="fa-solid fa-bell-concierge text-4xl text-gray-300 mb-2"></i>
                        <p class="text-sm font-medium text-gray-500">Nenhum pedido recebido ainda.</p>
                    </div>
                `;
                return;
            }

            state.orders.forEach(order => {
                const card = document.createElement('div');
                card.className = `bg-white rounded-2xl p-4 shadow-sm border ${order.status === 'ready' ? 'border-emerald-400 ring-2 ring-emerald-100' : 'border-gray-200'} space-y-3 flex flex-col justify-between`;

                let statusBadge = '';
                let actionButtons = '';

                if (order.status === 'received') {
                    statusBadge = `<span class="bg-amber-100 text-amber-700 text-xs px-2.5 py-1 rounded-lg font-bold">Pendente</span>`;
                    actionButtons = `
                        <button onclick="updateOrderStatus(${order.id}, 'preparing')" class="w-full bg-sesi-blue text-white py-2 rounded-xl text-xs font-bold hover:bg-blue-800 flex items-center justify-center space-x-1">
                            <i class="fa-solid fa-fire"></i>
                            <span>Iniciar Preparo</span>
                        </button>
                    `;
                } else if (order.status === 'preparing') {
                    statusBadge = `<span class="bg-blue-100 text-sesi-blue text-xs px-2.5 py-1 rounded-lg font-bold">Em Preparação</span>`;
                    actionButtons = `
                        <button onclick="updateOrderStatus(${order.id}, 'ready')" class="w-full bg-emerald-600 text-white py-2 rounded-xl text-xs font-bold hover:bg-emerald-700 flex items-center justify-center space-x-1">
                            <i class="fa-solid fa-check"></i>
                            <span>Marcar como Pronto</span>
                        </button>
                    `;
                } else if (order.status === 'ready') {
                    statusBadge = `<span class="bg-emerald-100 text-emerald-700 text-xs px-2.5 py-1 rounded-lg font-bold animate-pulse">Pronto p/ Retirada</span>`;
                    actionButtons = `
                        <button onclick="updateOrderStatus(${order.id}, 'completed')" class="w-full bg-gray-900 text-white py-2 rounded-xl text-xs font-bold hover:bg-gray-800 flex items-center justify-center space-x-1">
                            <i class="fa-solid fa-box-archive"></i>
                            <span>Finalizar / Entregue</span>
                        </button>
                    `;
                } else {
                    statusBadge = `<span class="bg-gray-100 text-gray-500 text-xs px-2.5 py-1 rounded-lg font-bold">Concluído</span>`;
                    actionButtons = `<div class="text-center text-xs font-bold text-gray-400 py-1">Entregue ao Aluno</div>`;
                }

                card.innerHTML = `
                    <div>
                        <!-- Header Card -->
                        <div class="flex justify-between items-center pb-2 border-b border-gray-100">
                            <div>
                                <span class="text-lg font-extrabold text-gray-900">#${order.id}</span>
                                <span class="text-xs text-gray-400 block">${order.time} hs</span>
                            </div>
                            ${statusBadge}
                        </div>

                        <!-- Student Info -->
                        <div class="py-2">
                            <h4 class="font-bold text-sm text-gray-800">${order.studentName}</h4>
                            <p class="text-xs text-gray-500">${order.studentClass} • Retirada: <strong class="text-sesi-blue">${order.pickupTime}</strong></p>
                        </div>

                        <!-- Item List -->
                        <div class="bg-gray-50 p-2.5 rounded-xl space-y-1 my-2">
                            ${order.items.map(item => `
                                <div class="flex justify-between text-xs">
                                    <span class="font-medium text-gray-800"><strong>${item.qty}x</strong> ${item.name}</span>
                                    <span class="text-gray-500">R$ ${(item.price * item.qty).toFixed(2)}</span>
                                </div>
                            `).join('')}
                        </div>
                    </div>

                    <!-- Card Actions -->
                    <div class="pt-2 border-t border-gray-100">
                        ${actionButtons}
                    </div>
                `;

                container.appendChild(card);
            });
        }

        // Update Order Status in Real Time
        function updateOrderStatus(orderId, newStatus) {
            const order = state.orders.find(o => o.id === orderId);
            if (order) {
                order.status = newStatus;
                saveLocalState();
                renderKDS();
                updateMetrics();
                
                // If student view active, refresh status banner
                renderActiveStudentOrder();
            }
        }

        function clearCompletedOrders() {
            state.orders = state.orders.filter(o => o.status !== 'completed');
            saveLocalState();
            renderKDS();
            updateMetrics();
        }

        // Update Dashboard Metrics
        function updateMetrics() {
            const pending = state.orders.filter(o => o.status === 'received').length;
            const preparing = state.orders.filter(o => o.status === 'preparing').length;
            const ready = state.orders.filter(o => o.status === 'ready').length;
            const completed = state.orders.filter(o => o.status === 'completed').length;

            document.getElementById('metric-pending').innerText = pending;
            document.getElementById('metric-preparing').innerText = preparing;
            document.getElementById('metric-ready').innerText = ready;
            document.getElementById('metric-completed').innerText = completed;

            const badge = document.getElementById('badge-pending');
            if (pending > 0) {
                badge.innerText = pending;
                badge.classList.remove('hidden');
            } else {
                badge.classList.add('hidden');
            }
        }
    </script>
</body>
</html>