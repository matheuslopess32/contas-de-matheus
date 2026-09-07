# contas-de-matheus
Contas de Matheus
<!DOCTYPE html>
<html lang="pt-br">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>💰 Finanças do Matheus</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        @import url('https://fonts.googleapis.com/css2?family=Inter:wght@300;400;600;700&display=swap');
        body { font-family: 'Inter', sans-serif; }
        .modal { transition: opacity 0.25s ease; }
        body.modal-active { overflow: hidden; }
        .card-hover:hover { transform: translateY(-4px); transition: all 0.3s ease; }
        .progress-bar { transition: width 1s ease-in-out; }
        @keyframes fadeIn { from { opacity:0; transform:translateY(6px); } to { opacity:1; transform:translateY(0); } }
        .fade-in { animation: fadeIn 0.5s ease; }
    </style>
</head>
<body class="bg-gray-50 min-h-screen text-gray-800">

<div class="max-w-6xl mx-auto px-4 py-8">

    <!-- Banner Motivacional -->
    <div class="mb-5 p-4 rounded-xl bg-blue-50 border border-blue-100 flex items-start gap-4">
        <div class="bg-blue-500 p-2 rounded-lg text-white flex-shrink-0 mt-0.5">
            <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M13 10V3L4 14h7v7l9-11h-7z"/>
            </svg>
        </div>
        <div class="flex-grow">
            <p class="text-xs font-bold text-blue-400 uppercase tracking-wider mb-1">Dica do Dia</p>
            <p id="motivation-text" class="text-blue-800 font-medium italic text-sm fade-in"></p>
        </div>
    </div>

    <!-- Alertas -->
    <div id="alert-section" class="mb-5 flex flex-col gap-2"></div>

    <!-- Header -->
    <header class="mb-8 flex flex-col md:flex-row justify-between items-center gap-4 bg-white p-6 rounded-2xl shadow-sm border border-gray-100">
        <div>
            <h1 class="text-2xl font-bold text-gray-900">Controle da Conta de Matheus</h1>
            <p class="text-gray-500 text-sm">Acompanhe suas metas e reduza despesas</p>
        </div>
        <div class="flex flex-col md:flex-row items-center gap-3">
            <div id="health-indicator" class="px-4 py-2 rounded-full text-xs font-bold uppercase tracking-wider bg-gray-100 text-gray-500">
                Calculando...
            </div>
            <select id="monthSelect" class="bg-gray-50 border border-gray-200 text-gray-900 text-sm rounded-lg focus:ring-blue-500 focus:border-blue-500 block p-2.5">
                <option>Janeiro</option><option>Fevereiro</option><option selected>Março</option>
                <option>Abril</option><option>Maio</option><option>Junho</option>
                <option>Julho</option><option>Agosto</option><option>Setembro</option>
                <option>Outubro</option><option>Novembro</option><option>Dezembro</option>
            </select>
            <button onclick="openAddModal()" class="bg-blue-600 hover:bg-blue-700 text-white px-4 py-2.5 rounded-lg text-sm font-semibold flex items-center gap-2 transition-all active:scale-95 shadow-md shadow-blue-200">
                <svg xmlns="http://www.w3.org/2000/svg" class="h-5 w-5" viewBox="0 0 20 20" fill="currentColor">
                    <path fill-rule="evenodd" d="M10 3a1 1 0 011 1v5h5a1 1 0 110 2h-5v5a1 1 0 11-2 0v-5H4a1 1 0 110-2h5V4a1 1 0 011-1z" clip-rule="evenodd"/>
                </svg>
                Novo Item
            </button>
        </div>
    </header>

    <!-- KPI Cards -->
    <div class="grid grid-cols-1 md:grid-cols-2 lg:grid-cols-4 gap-6 mb-8">
        <div class="bg-white p-6 rounded-2xl shadow-sm border-l-4 border-blue-500 card-hover">
            <p class="text-xs text-gray-400 font-semibold uppercase mb-1">Total Consolidado</p>
            <h2 class="text-2xl font-bold text-gray-900" id="kpi-total-geral">R$ 0,00</h2>
            <p class="text-xs text-gray-400 mt-1">Soma de todas as dívidas</p>
        </div>
        <div class="bg-white p-6 rounded-2xl shadow-sm border-l-4 border-yellow-400 card-hover">
            <p class="text-xs text-gray-400 font-semibold uppercase mb-1">A Pagar Este Mês</p>
            <h2 class="text-2xl font-bold text-yellow-500" id="kpi-total-mes">R$ 0,00</h2>
            <p class="text-xs text-gray-400 mt-1">Soma das parcelas do mês</p>
        </div>
        <div class="bg-white p-6 rounded-2xl shadow-sm border-l-4 border-red-400 card-hover">
            <p class="text-xs text-gray-400 font-semibold uppercase mb-1">Saldo Devedor Futuro</p>
            <h2 class="text-2xl font-bold text-red-500" id="kpi-devedor">R$ 0,00</h2>
            <p class="text-xs text-gray-400 mt-1">Parcelas ainda a pagar</p>
        </div>
        <div class="bg-white p-6 rounded-2xl shadow-sm border-l-4 border-purple-500 card-hover">
            <p class="text-xs text-gray-400 font-semibold uppercase mb-1">Contas Ativas</p>
            <h2 class="text-2xl font-bold text-gray-900" id="kpi-itens">0</h2>
            <p class="text-xs text-gray-400 mt-1">Compromissos em aberto</p>
        </div>
    </div>

    <!-- Meta de Quitação -->
    <div class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100 mb-8">
        <div class="flex justify-between items-center mb-3">
            <div>
                <h3 class="font-bold text-gray-800">🏆 Meta de Quitação</h3>
                <p class="text-xs text-gray-400 mt-0.5">Percentual do total já quitado</p>
            </div>
            <span id="goal-percent" class="text-2xl font-bold text-green-500">0%</span>
        </div>
        <div class="w-full bg-gray-100 rounded-full h-3">
            <div id="goal-bar" class="progress-bar h-3 rounded-full bg-gradient-to-r from-blue-400 to-green-400" style="width:0%"></div>
        </div>
        <div class="flex justify-between mt-2">
            <span class="text-xs text-gray-400">Pago: <span id="goal-pago" class="text-green-600 font-semibold">R$ 0,00</span></span>
            <span class="text-xs text-gray-400">Restante: <span id="goal-rest" class="text-red-500 font-semibold">R$ 0,00</span></span>
        </div>
    </div>

    <div class="grid grid-cols-1 lg:grid-cols-3 gap-8 mb-8">

        <!-- Tabela -->
        <div class="lg:col-span-2 bg-white rounded-2xl shadow-sm overflow-hidden border border-gray-100">
            <div class="p-6 border-b border-gray-100 flex justify-between items-center">
                <h3 class="font-bold text-lg text-gray-800">Detalhamento</h3>
                <span class="text-xs text-blue-500 font-medium px-2 py-1 bg-blue-50 rounded">Clique para gerenciar</span>
            </div>
            <div class="overflow-x-auto">
                <table class="w-full text-left">
                    <thead class="bg-gray-50 border-b border-gray-100">
                        <tr>
                            <th class="px-6 py-4 text-xs font-semibold text-gray-500 uppercase">Descrição</th>
                            <th class="px-6 py-4 text-xs font-semibold text-gray-500 uppercase">Total</th>
                            <th class="px-6 py-4 text-xs font-semibold text-gray-500 uppercase">Progresso</th>
                            <th class="px-6 py-4 text-xs font-semibold text-gray-500 uppercase text-right">Parc./Mês</th>
                        </tr>
                    </thead>
                    <tbody class="divide-y divide-gray-100" id="table-body"></tbody>
                </table>
            </div>
        </div>

        <!-- Lado direito -->
        <div class="flex flex-col gap-6">

            <!-- Gráfico -->
            <div class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100">
                <h3 class="font-bold text-lg text-gray-800 mb-4">Distribuição Mensal</h3>
                <div class="relative h-48">
                    <canvas id="expensesChart"></canvas>
                </div>
                <div class="mt-4 space-y-2 max-h-40 overflow-y-auto pr-1" id="legend-container"></div>
            </div>

            <!-- Dicas de Economia -->
            <div class="bg-white p-6 rounded-2xl shadow-sm border border-gray-100">
                <h3 class="font-bold text-gray-800 mb-1">💡 Como Economizar</h3>
                <p class="text-xs text-gray-400 mb-4">Dicas práticas para aplicar hoje</p>
                <div class="space-y-3" id="economy-tips"></div>
            </div>
        </div>
    </div>

</div>

<!-- MODAL -->
<div id="modal" class="modal opacity-0 pointer-events-none fixed w-full h-full top-0 left-0 flex items-center justify-center z-50">
    <div class="modal-overlay absolute w-full h-full bg-gray-900 opacity-60 backdrop-blur-sm"></div>
    <div class="modal-container bg-white w-11/12 md:max-w-md mx-auto rounded-2xl shadow-2xl z-50 overflow-y-auto p-6 transform transition-transform scale-95">
        <div class="flex justify-between items-center pb-4 border-b border-gray-100 mb-4">
            <p class="text-xl font-bold text-gray-800" id="modal-title">Novo Lançamento</p>
            <button onclick="toggleModal()" class="text-gray-400 hover:text-gray-600 transition-colors">
                <svg class="h-6 w-6" fill="none" viewBox="0 0 24 24" stroke="currentColor">
                    <path stroke-linecap="round" stroke-linejoin="round" stroke-width="2" d="M6 18L18 6M6 6l12 12"/>
                </svg>
            </button>
        </div>

        <!-- Aviso no modal -->
        <div class="mb-4 p-3 bg-amber-50 border border-amber-200 rounded-xl text-xs text-amber-700 font-medium">
            ⚠️ <strong>Antes de adicionar:</strong> isso é uma necessidade ou um desejo? Priorize o essencial.
        </div>

        <form id="edit-form" class="space-y-4">
            <input type="hidden" id="edit-id">
            <div>
                <label class="block text-xs font-bold text-gray-500 uppercase mb-1">Nome do Gasto</label>
                <input type="text" id="edit-nome" placeholder="Ex: Conta de luz, Mercado…" class="w-full p-3 bg-gray-50 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 outline-none transition-all">
            </div>
            <div>
                <label class="block text-xs font-bold text-gray-500 uppercase mb-1">Valor Total (R$)</label>
                <input type="number" step="0.01" id="edit-valor" placeholder="0,00" class="w-full p-3 bg-gray-50 border border-gray-200 rounded-xl focus:ring-2 focus:ring-blue-500 outline-none transition-all">
            </div>
            <div class="grid grid-cols-2 gap-4">
                <div>
                    <label class="block text-xs font-bold text-gray-500 uppercase mb-1">Total Parcelas</label>
                    <input type="number" id="edit-total-parc" value="1" min="1" class="w-full p-3 bg-gray-50 border border-gray-200 rounded-xl outline-none">
                </div>
                <div>
                    <label class="block text-xs font-bold text-gray-500 uppercase mb-1">Pagas</label>
                    <input type="number" id="edit-atual-parc" value="0" min="0" class="w-full p-3 bg-gray-50 border border-gray-200 rounded-xl outline-none">
                </div>
            </div>
            <div class="pt-2 flex flex-col gap-2">
                <button type="button" onclick="saveChanges()" class="w-full bg-blue-600 text-white py-3 rounded-xl font-bold hover:bg-blue-700 transition-all shadow-lg shadow-blue-200">Salvar</button>
                <button type="button" id="delete-btn" onclick="deleteItem()" class="w-full text-red-500 py-3 rounded-xl text-sm font-bold hover:bg-red-50 hidden transition-colors">🗑 Excluir Registro</button>
                <button type="button" onclick="toggleModal()" class="w-full text-gray-400 py-2 text-sm font-medium">Cancelar</button>
            </div>
        </form>
    </div>
</div>

<script>
const MOTIVATIONAL = [
    "Pequenas economias hoje geram grandes liberdades amanhã.",
    "Antes de comprar, pergunte-se: eu preciso disso ou só quero?",
    "Cuidar do seu dinheiro é cuidar da sua paz de espírito.",
    "O segredo não é ganhar mais — é gastar com inteligência.",
    "Cada real economizado é um tijolo na sua liberdade financeira.",
    "Você tem o poder de mudar sua história financeira. Comece agora!",
    "Um orçamento não te aprisiona — ele te liberta para escolher.",
    "Quitar uma dívida é a melhor aplicação financeira que existe.",
];

const ECONOMY_TIPS = [
    { icon: "🛒", text: "Faça uma lista antes de ir ao mercado. Quem vai sem lista, volta com o dobro do planejado." },
    { icon: "💳", text: "Cartão de crédito não é renda extra — é dívida antecipada. Use só o que já tem no bolso." },
    { icon: "📱", text: "Revise suas assinaturas mensais. Cancele as que você não usa toda semana." },
];

let db = JSON.parse(localStorage.getItem('matheus_fin_v3')) || [
    { id: 101, produto: "Notebook",            valor: 3612.40, parcelas: 10, paga: 6, color: '#F59E0B' },
    { id: 102, produto: "Empréstimo Agibank",  valor: 3811.68, parcelas: 18, paga: 8, color: '#8B5CF6' },
    { id: 103, produto: "Cartão de Crédito",   valor: 208.08,  parcelas: 1,  paga: 1, color: '#EF4444' },
    { id: 104, produto: "Nubank Empréstimo",   valor: 571.00,  parcelas: 7,  paga: 5, color: '#8B5CF6' },
    { id: 105, produto: "Nubank Reforma",      valor: 256.48,  parcelas: 4,  paga: 3, color: '#F59E0B' },
    { id: 106, produto: "Paraíba",             valor: 358.20,  parcelas: 6,  paga: 6, color: '#A855F7' },
    { id: 107, produto: "CredShop",            valor: 50.00,   parcelas: 1,  paga: 1, color: '#06B6D4' },
    { id: 108, produto: "Plano Tim",           valor: 39.99,   parcelas: 1,  paga: 1, color: '#EC4899' },
];
let myChart = null;
let tipIndex = 0;

function fmt(n) { return 'R$ ' + n.toLocaleString('pt-BR', { minimumFractionDigits: 2 }); }
function save() { localStorage.setItem('matheus_fin_v3', JSON.stringify(db)); }

function updateDashboard() {
    db.sort((a, b) => (b.valor / b.parcelas) - (a.valor / a.parcelas));

    const tbody = document.getElementById('table-body');
    tbody.innerHTML = '';
    let totalGeral = 0, totalMes = 0, totalDevedor = 0, totalPago = 0;

    db.forEach(item => {
        const vlrParc = item.valor / item.parcelas;
        const restantes = Math.max(0, item.parcelas - item.paga);
        totalGeral   += item.valor;
        totalMes     += vlrParc;
        totalDevedor += vlrParc * restantes;
        totalPago    += vlrParc * item.paga;

        const prog = (item.paga / item.parcelas) * 100;
        const lastParc = restantes === 1;
        const row = document.createElement('tr');
        row.className = 'hover:bg-blue-50/50 transition-all cursor-pointer group';
        row.onclick = () => openEditModal(item.id);
        row.innerHTML = `
            <td class="px-6 py-4">
                <div class="flex items-center gap-3">
                    <div class="w-8 h-8 rounded-lg flex items-center justify-center text-white font-bold text-xs" style="background:${item.color}">${item.produto.charAt(0)}</div>
                    <div>
                        <span class="block font-bold text-gray-900 group-hover:text-blue-600 transition-colors">${item.produto}</span>
                        <span class="text-[10px] text-gray-400 uppercase font-semibold">${item.paga}/${item.parcelas}x ${lastParc ? '· <span class="text-yellow-500 normal-case">última parcela!</span>' : ''}</span>
                    </div>
                </div>
            </td>
            <td class="px-6 py-4 text-sm text-gray-500 font-medium">${fmt(item.valor)}</td>
            <td class="px-6 py-4">
                <div class="flex items-center gap-3">
                    <div class="flex-grow bg-gray-100 rounded-full h-1.5 max-w-[80px]">
                        <div class="progress-bar h-1.5 rounded-full" style="width:${prog}%;background:${prog>=100?'#10B981':item.color}"></div>
                    </div>
                    <span class="text-[10px] font-bold text-gray-400">${Math.round(prog)}%</span>
                </div>
            </td>
            <td class="px-6 py-4 text-right font-bold text-gray-900">${fmt(vlrParc)}</td>
        `;
        tbody.appendChild(row);
    });

    document.getElementById('kpi-total-geral').textContent = fmt(totalGeral);
    document.getElementById('kpi-total-mes').textContent   = fmt(totalMes);
    document.getElementById('kpi-devedor').textContent     = fmt(totalDevedor);
    document.getElementById('kpi-itens').textContent       = db.length;

    const perc = totalGeral > 0 ? (totalPago / totalGeral) * 100 : 0;
    document.getElementById('goal-percent').textContent = Math.round(perc) + '%';
    document.getElementById('goal-bar').style.width = perc + '%';
    document.getElementById('goal-pago').textContent = fmt(totalPago);
    document.getElementById('goal-rest').textContent = fmt(totalDevedor);

    updateHealth(totalMes);
    updateAlerts(totalMes);
    renderChart();
    renderLegend();
    renderEconomyTips();
    rotateTip();
    save();
}

function updateHealth(totalMes) {
    const el = document.getElementById('health-indicator');
    if (totalMes < 500) {
        el.className = 'px-4 py-2 rounded-full text-xs font-bold uppercase tracking-wider bg-green-100 text-green-700';
        el.textContent = 'Saúde: Ótima 🌿';
    } else if (totalMes < 1000) {
        el.className = 'px-4 py-2 rounded-full text-xs font-bold uppercase tracking-wider bg-blue-100 text-blue-700';
        el.textContent = 'Saúde: Controlada ✅';
    } else {
        el.className = 'px-4 py-2 rounded-full text-xs font-bold uppercase tracking-wider bg-red-100 text-red-600';
        el.textContent = 'Saúde: Atenção ⚠️';
    }
}

function updateAlerts(totalMes) {
    const section = document.getElementById('alert-section');
    section.innerHTML = '';
    const alerts = [];

    if (totalMes > 1000) {
        alerts.push({ cls: 'bg-red-50 border-red-200 text-red-700', msg: '🔴 Seus gastos mensais passam de R$1.000. Considere renegociar parcelas ou cortar algum compromisso.' });
    } else if (totalMes > 500) {
        alerts.push({ cls: 'bg-yellow-50 border-yellow-200 text-yellow-700', msg: '🟡 Gastos acima de R$500/mês. Fique atento para não comprometer mais da sua renda.' });
    } else {
        alerts.push({ cls: 'bg-green-50 border-green-200 text-green-700', msg: '🟢 Gastos mensais sob controle! Aproveite para guardar a diferença.' });
    }

    db.forEach(item => {
        if (item.parcelas - item.paga === 1) {
            alerts.push({ cls: 'bg-blue-50 border-blue-200 text-blue-700', msg: `🎉 "${item.produto}" está na última parcela! Você está quase lá!` });
        }
    });

    alerts.forEach(a => {
        const div = document.createElement('div');
        div.className = `p-3 rounded-xl border text-sm font-medium ${a.cls}`;
        div.innerHTML = a.msg;
        section.appendChild(div);
    });
}

function renderChart() {
    const ctx = document.getElementById('expensesChart').getContext('2d');
    if (myChart) myChart.destroy();
    if (!db.length) return;
    myChart = new Chart(ctx, {
        type: 'doughnut',
        data: {
            labels: db.map(i => i.produto),
            datasets: [{
                data: db.map(i => i.valor / i.parcelas),
                backgroundColor: db.map(i => i.color),
                borderWidth: 4, borderColor: '#ffffff', hoverOffset: 12
            }]
        },
        options: {
            responsive: true, maintainAspectRatio: false,
            plugins: {
                legend: { display: false },
                tooltip: { callbacks: { label: ctx => ` ${fmt(ctx.parsed)}` } }
            },
            cutout: '70%'
        }
    });
}

function renderLegend() {
    const el = document.getElementById('legend-container');
    el.innerHTML = '';
    const totalMes = db.reduce((a, c) => a + c.valor / c.parcelas, 0);
    if (!totalMes) { el.innerHTML = '<p class="text-gray-400 text-center text-sm italic py-2">Nenhum gasto registrado</p>'; return; }
    db.forEach(item => {
        const v = item.valor / item.parcelas;
        const perc = ((v / totalMes) * 100).toFixed(1);
        el.innerHTML += `
            <div class="flex items-center justify-between p-2 rounded-lg hover:bg-gray-50 transition-colors">
                <div class="flex items-center gap-2">
                    <span class="w-2.5 h-2.5 rounded-full" style="background:${item.color}"></span>
                    <span class="text-sm font-medium text-gray-700 truncate max-w-[130px]">${item.produto}</span>
                </div>
                <div class="text-right">
                    <span class="block text-xs font-bold text-gray-900">${fmt(v)}</span>
                    <span class="block text-[10px] text-gray-400 font-bold">${perc}%</span>
                </div>
            </div>`;
    });
}

function renderEconomyTips() {
    const el = document.getElementById('economy-tips');
    el.innerHTML = '';
    ECONOMY_TIPS.forEach(t => {
        el.innerHTML += `
            <div class="flex gap-3 items-start p-3 bg-gray-50 rounded-xl border border-gray-100">
                <span class="text-lg flex-shrink-0">${t.icon}</span>
                <p class="text-xs text-gray-600 leading-relaxed m-0">${t.text}</p>
            </div>`;
    });
}

function rotateTip() {
    const el = document.getElementById('motivation-text');
    el.classList.remove('fade-in');
    void el.offsetWidth;
    el.classList.add('fade-in');
    el.textContent = `"${MOTIVATIONAL[tipIndex % MOTIVATIONAL.length]}"`;
    tipIndex++;
}
setInterval(rotateTip, 7000);

function toggleModal() {
    const modal = document.getElementById('modal');
    const container = modal.querySelector('.modal-container');
    if (modal.classList.contains('opacity-0')) {
        modal.classList.remove('opacity-0', 'pointer-events-none');
        container.classList.remove('scale-95'); container.classList.add('scale-100');
        document.body.classList.add('modal-active');
    } else {
        modal.classList.add('opacity-0', 'pointer-events-none');
        container.classList.remove('scale-100'); container.classList.add('scale-95');
        document.body.classList.remove('modal-active');
    }
}
function openAddModal() {
    document.getElementById('modal-title').innerText = 'Novo Lançamento';
    ['edit-id','edit-nome','edit-valor'].forEach(id => document.getElementById(id).value = '');
    document.getElementById('edit-total-parc').value = 1;
    document.getElementById('edit-atual-parc').value = 0;
    document.getElementById('delete-btn').classList.add('hidden');
    toggleModal();
}
function openEditModal(id) {
    const item = db.find(i => i.id === id);
    document.getElementById('modal-title').innerText = 'Editar Registro';
    document.getElementById('edit-id').value = item.id;
    document.getElementById('edit-nome').value = item.produto;
    document.getElementById('edit-valor').value = item.valor;
    document.getElementById('edit-total-parc').value = item.parcelas;
    document.getElementById('edit-atual-parc').value = item.paga;
    document.getElementById('delete-btn').classList.remove('hidden');
    toggleModal();
}
function saveChanges() {
    const id = document.getElementById('edit-id').value;
    const nome = document.getElementById('edit-nome').value || 'Sem Nome';
    const valor = parseFloat(document.getElementById('edit-valor').value) || 0;
    const totalParc = parseInt(document.getElementById('edit-total-parc').value) || 1;
    const paga = parseInt(document.getElementById('edit-atual-parc').value) || 0;
    if (valor <= 0) { alert('Por favor, insira um valor válido.'); return; }
    const colors = ['#3B82F6','#10B981','#8B5CF6','#F59E0B','#EF4444','#EC4899','#06B6D4','#6366F1'];
    if (id === '') {
        db.push({ id: Date.now(), produto: nome, valor, parcelas: totalParc, paga, color: colors[db.length % colors.length] });
    } else {
        const item = db.find(i => i.id == id);
        item.produto = nome; item.valor = valor; item.parcelas = totalParc; item.paga = paga;
    }
    toggleModal(); updateDashboard();
}
function deleteItem() {
    if (!confirm('Excluir este registro?')) return;
    db = db.filter(i => i.id != document.getElementById('edit-id').value);
    toggleModal(); updateDashboard();
}
document.getElementById('monthSelect').addEventListener('change', () => {
    db = db.map(item => ({ ...item, paga: Math.min(item.paga + 1, item.parcelas) }));
    updateDashboard();
});
window.onload = updateDashboard;
</script>
</body>
</html>
