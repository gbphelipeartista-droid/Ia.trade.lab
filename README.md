
<!DOCTYPE html>
<html lang="pt-BR">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>LA Trade Lab | Simulador Multimercado</title>
  <style>
    :root {
      --bg: #090d16;
      --panel: #111827;
      --panel2: #182235;
      --border: #29364b;
      --text: #eef3ff;
      --muted: #94a3b8;
      --green: #2de39a;
      --red: #ff657a;
      --blue: #69a7ff;
      --yellow: #ffd166;
    }

    * { box-sizing: border-box; }

    body {
      margin: 0;
      background: var(--bg);
      color: var(--text);
      font-family: Arial, Helvetica, sans-serif;
    }

    header {
      padding: 22px 18px;
      border-bottom: 1px solid var(--border);
      background: #0c1220;
    }

    .wrap {
      width: min(1150px, 100%);
      margin: auto;
      padding: 0 16px;
    }

    .brand {
      display: flex;
      align-items: center;
      gap: 12px;
    }

    .logo {
      width: 44px;
      height: 44px;
      display: grid;
      place-items: center;
      border-radius: 12px;
      color: #07131a;
      background: var(--green);
      font-weight: 900;
      font-size: 19px;
    }

    h1 { margin: 0; font-size: 22px; }
    h2 { font-size: 17px; margin: 0 0 14px; }
    p { line-height: 1.5; }
    .muted { color: var(--muted); }
    .small { font-size: 12px; }

    .status {
      display: inline-flex;
      align-items: center;
      gap: 7px;
      color: var(--yellow);
      border: 1px solid #66552c;
      background: #292313;
      border-radius: 30px;
      padding: 7px 10px;
      font-size: 12px;
      margin-top: 12px;
    }

    .dot {
      width: 7px;
      height: 7px;
      background: var(--yellow);
      border-radius: 50%;
    }

    main { padding: 22px 0 38px; }

    .grid {
      display: grid;
      grid-template-columns: repeat(12, minmax(0, 1fr));
      gap: 14px;
    }

    .card {
      background: var(--panel);
      border: 1px solid var(--border);
      border-radius: 15px;
      padding: 17px;
      min-width: 0;
    }

    .span-12 { grid-column: span 12; }
    .span-8 { grid-column: span 8; }
    .span-6 { grid-column: span 6; }
    .span-4 { grid-column: span 4; }

    .metrics {
      display: grid;
      grid-template-columns: repeat(3, minmax(0, 1fr));
      gap: 10px;
    }

    .metric {
      background: var(--panel2);
      padding: 13px;
      border-radius: 11px;
      min-width: 0;
    }

    .metric-label { color: var(--muted); font-size: 12px; }
    .metric-value {
      font-size: clamp(17px, 3vw, 24px);
      font-weight: 700;
      margin-top: 7px;
      overflow-wrap: anywhere;
    }

    label {
      display: block;
      color: var(--muted);
      font-size: 12px;
      margin-bottom: 7px;
    }

    select, input {
      width: 100%;
      padding: 12px;
      background: #0b1220;
      border: 1px solid var(--border);
      color: var(--text);
      border-radius: 9px;
      outline: none;
    }

    select:focus, input:focus { border-color: var(--blue); }

    .fields {
      display: grid;
      grid-template-columns: repeat(2, minmax(0, 1fr));
      gap: 12px;
    }

    .field { min-width: 0; }

    button {
      border: 0;
      border-radius: 9px;
      padding: 12px 14px;
      font-weight: 700;
      cursor: pointer;
      color: #08121b;
      background: var(--green);
    }

    button:disabled { opacity: .45; cursor: not-allowed; }
    button.secondary { background: #26344b; color: var(--text); }
    button.up { background: #164e3a; color: #70ffc1; }
    button.down { background: #582634; color: #ff9baa; }
    button.warning { background: #66501d; color: #fff0bd; }

    .actions {
      display: grid;
      grid-template-columns: repeat(2, minmax(0, 1fr));
      gap: 10px;
      margin-top: 14px;
    }

    .actions button { min-height: 46px; }

    .chart-box {
      position: relative;
      width: 100%;
      height: 260px;
      background: #0b1220;
      border: 1px solid var(--border);
      border-radius: 10px;
      overflow: hidden;
    }

    canvas { width: 100%; height: 100%; display: block; }

    .signal {
      padding: 15px;
      border-radius: 11px;
      background: var(--panel2);
      margin-bottom: 12px;
    }

    .signal-title { font-weight: 800; font-size: 19px; }
    .green { color: var(--green); }
    .red { color: var(--red); }
    .blue { color: var(--blue); }
    .yellow { color: var(--yellow); }

    .bar {
      height: 7px;
      background: #273449;
      border-radius: 10px;
      overflow: hidden;
      margin-top: 8px;
    }

    .bar > div {
      height: 100%;
      width: 0;
      background: var(--blue);
      transition: width .2s;
    }

    .notice {
      padding: 12px;
      border-radius: 9px;
      background: #292313;
      border: 1px solid #66552c;
      color: #ffe8a3;
      font-size: 12px;
      line-height: 1.5;
    }

    .history {
      overflow-x: auto;
    }

    table {
      border-collapse: collapse;
      width: 100%;
      font-size: 13px;
      white-space: nowrap;
    }

    th, td {
      padding: 11px 8px;
      text-align: left;
      border-bottom: 1px solid var(--border);
    }

    th { color: var(--muted); font-weight: 500; }

    .switch-row {
      display: flex;
      justify-content: space-between;
      gap: 12px;
      align-items: center;
      padding: 12px 0;
    }

    .switch-row input {
      width: 20px;
      height: 20px;
      accent-color: var(--green);
    }

    .footer {
      color: var(--muted);
      font-size: 12px;
      text-align: center;
      padding: 24px 10px;
    }

    @media (max-width: 760px) {
      .span-8, .span-6, .span-4 { grid-column: span 12; }
      .metrics { grid-template-columns: 1fr; }
      .metric { display: flex; justify-content: space-between; gap: 10px; align-items: center; }
      .metric-value { margin: 0; }
      .fields { grid-template-columns: 1fr; }
      .chart-box { height: 220px; }
    }
  </style>
</head>
<body>
<header>
  <div class="wrap">
    <div class="brand">
      <div class="logo">LA</div>
      <div>
        <h1>LA Trade Lab</h1>
        <div class="muted small">Laboratório de análise e simulação multimercado</div>
      </div>
    </div>
    <div class="status"><span class="dot"></span> MODO SIMULADOR — SEM COTAÇÕES REAIS</div>
  </div>
</header>

<main class="wrap">
  <div class="grid">

    <section class="card span-12">
      <h2>Seu painel</h2>
      <div class="metrics">
        <div class="metric">
          <div class="metric-label">Saldo virtual</div>
          <div class="metric-value" id="balance">R$ 50,00</div>
        </div>
        <div class="metric">
          <div class="metric-label">Resultado acumulado</div>
          <div class="metric-value" id="profit">R$ 0,00</div>
        </div>
        <div class="metric">
          <div class="metric-label">Operações encerradas</div>
          <div class="metric-value" id="tradeCount">0</div>
        </div>
      </div>
    </section>

    <section class="card span-4">
      <h2>Mercado e ativo</h2>
      <div class="field">
        <label for="market">Escolha o mercado</label>
        <select id="market">
          <option value="crypto">Criptomoedas</option>
          <option value="b3">Ações brasileiras — B3</option>
          <option value="us">Ações americanas</option>
          <option value="forex">Forex — moedas</option>
        </select>
      </div>
      <div class="field" style="margin-top:12px">
        <label for="asset">Ativo para simular</label>
        <select id="asset"></select>
      </div>
      <p class="muted small" id="assetInfo"></p>
      <div class="notice">
        Os preços exibidos nesta versão são inventados pelo simulador. Não representam preços atuais nem são sinais para investir dinheiro real.
      </div>
    </section>

    <section class="card span-8">
      <h2>Preço simulado</h2>
      <div class="metrics">
        <div class="metric">
          <div class="metric-label">Preço atual</div>
          <div class="metric-value" id="price">—</div>
        </div>
        <div class="metric">
          <div class="metric-label">Variação simulada</div>
          <div class="metric-value" id="change">—</div>
        </div>
        <div class="metric">
          <div class="metric-label">Atualização</div>
          <div class="metric-value" id="updated" style="font-size:16px">—</div>
        </div>
      </div>
      <div class="chart-box" style="margin-top:14px">
        <canvas id="chart" aria-label="Gráfico de preços simulados"></canvas>
      </div>
      <p class="muted small">Gráfico de preços sintéticos gerados localmente. Não é um gráfico de mercado ao vivo.</p>
    </section>

    <section class="card span-6">
      <h2>Análise automática experimental</h2>
      <div class="signal">
        <div class="muted small">Leitura dos indicadores simulados</div>
        <div class="signal-title yellow" id="signal">AGUARDAR</div>
        <p class="muted small" id="reason">Aguardando dados suficientes para calcular os indicadores.</p>
      </div>

      <div class="field">
        <label>Média móvel curta (SMA 5)</label>
        <div class="metric-value" id="smaShort">—</div>
      </div>
      <div class="field" style="margin-top:14px">
        <label>Média móvel longa (SMA 12)</label>
        <div class="metric-value" id="smaLong">—</div>
      </div>
      <div class="field" style="margin-top:14px">
        <label>RSI simplificado</label>
        <div class="metric-value" id="rsi">—</div>
        <div class="bar"><div id="rsiBar"></div></div>
      </div>
      <p class="muted small">A leitura é uma regra demonstrativa baseada em dados sintéticos. Não usa inteligência artificial nem prevê o mercado real.</p>
    </section>

    <section class="card span-6">
      <h2>Operações virtuais</h2>
      <div class="fields">
        <div class="field">
          <label for="stake">Valor por operação (R$)</label>
          <input id="stake" type="number" min="1" max="5" step="1" value="2">
        </div>
        <div class="field">
          <label for="duration">Duração (segundos)</label>
          <select id="duration">
            <option value="10">10 segundos</option>
            <option value="20" selected>20 segundos</option>
            <option value="30">30 segundos</option>
            <option value="60">60 segundos</option>
          </select>
        </div>
      </div>

      <div class="actions">
        <button class="up" id="upBtn">SIMULAR SUBIDA</button>
        <button class="down" id="downBtn">SIMULAR QUEDA</button>
      </div>

      <div class="switch-row">
        <div>
          <strong>Modo automático</strong>
          <div class="muted small">Abre operações virtuais com base nos indicadores.</div>
        </div>
        <input id="autoMode" type="checkbox" aria-label="Ativar modo automático">
      </div>

      <div class="notice" id="autoStatus">
        Automação desligada. Nenhuma ordem real será enviada.
      </div>

      <div class="actions">
        <button class="secondary" id="resetBtn">Reiniciar saldo virtual</button>
        <button class="secondary" id="clearBtn">Limpar histórico</button>
      </div>
      <p class="muted small">Simulação com duração e resultado virtuais. O pagamento de acerto é configurado em 80% do valor arriscado; isso não corresponde a uma oferta financeira real.</p>
    </section>

    <section class="card span-12">
      <h2>Operações em andamento</h2>
      <div id="openTrades" class="muted small">Nenhuma operação em andamento.</div>
    </section>

    <section class="card span-12">
      <h2>Histórico de simulação</h2>
      <div class="history">
        <table>
          <thead>
            <tr>
              <th>Horário</th>
              <th>Mercado</th>
              <th>Ativo</th>
              <th>Direção</th>
              <th>Valor</th>
              <th>Resultado</th>
              <th>Saldo após operação</th>
            </tr>
          </thead>
          <tbody id="history">
            <tr><td colspan="7" class="muted">Nenhuma operação realizada.</td></tr>
          </tbody>
        </table>
      </div>
    </section>

    <section class="card span-12">
      <h2>Limites de segurança do simulador</h2>
      <div class="notice">
        • Saldo inicial virtual: R$ 50,00.<br>
        • Valor máximo por operação: R$ 5,00.<br>
        • O modo automático para de abrir operações quando o saldo virtual fica abaixo de R$ 2,00 ou quando atinge o limite de perda acumulada de R$ 10,00.<br>
        • Nenhuma conta bancária ou corretora é acessada.<br>
        • Nunca insira senhas ou chaves de API neste arquivo público.
      </div>
    </section>
  </div>
</main>

<div class="footer">
  LA Trade Lab · Ferramenta educacional · Todos os resultados são simulados
</div>

<script>
(() => {
  "use strict";

  const INITIAL_BALANCE = 50;
  const PAYOUT = 0.80;
  const MAX_STAKE = 5;
  const MAX_CUMULATIVE_LOSS = 10;

  const markets = {
    crypto: {
      label: "Criptomoedas",
      currency: "US$",
      assets: [
        { symbol: "BTC/USD", name: "Bitcoin", base: 65000 },
        { symbol: "ETH/USD", name: "Ethereum", base: 3200 },
        { symbol: "SOL/USD", name: "Solana", base: 150 },
        { symbol: "XRP/USD", name: "XRP", base: 0.60 }
      ]
    },
    b3: {
      label: "Ações brasileiras — B3",
      currency: "R$",
      assets: [
        { symbol: "PETR4", name: "Petrobras PN", base: 35 },
        { symbol: "VALE3", name: "Vale ON", base: 60 },
        { symbol: "ITUB4", name: "Itaú Unibanco PN", base: 32 },
        { symbol: "BBAS3", name: "Banco do Brasil ON", base: 28 }
      ]
    },
    us: {
      label: "Ações americanas",
      currency: "US$",
      assets: [
        { symbol: "AAPL", name: "Apple", base: 220 },
        { symbol: "MSFT", name: "Microsoft", base: 450 },
        { symbol: "NVDA", name: "NVIDIA", base: 140 },
        { symbol: "TSLA", name: "Tesla", base: 300 }
      ]
    },
    forex: {
      label: "Forex — moedas",
      currency: "",
      assets: [
        { symbol: "EUR/USD", name: "Euro / Dólar", base: 1.08 },
        { symbol: "GBP/USD", name: "Libra / Dólar", base: 1.27 },
        { symbol: "USD/JPY", name: "Dólar / Iene", base: 150 },
        { symbol: "AUD/USD", name: "Dólar australiano / Dólar", base: 0.66 }
      ]
    }
  };

  const $ = id => document.getElementById(id);

  let balance = INITIAL_BALANCE;
  let cumulativeResult = 0;
  let history = [];
  let openTrades = [];
  let prices = [];
  let currentPrice = 0;
  let initialPrice = 0;
  let autoTimer = null;
  let lastAutoTrade = 0;
  let marketKey = "crypto";
  let assetIndex = 0;
  let lastTick = Date.now();
  let tradeId = 1;

  function asset() {
    return markets[marketKey].assets[assetIndex];
  }

  function money(value) {
    return "R$ " + value.toLocaleString("pt-BR", {
      minimumFractionDigits: 2,
      maximumFractionDigits: 2
    });
  }

  function quote(value) {
    const currency = markets[marketKey].currency;
    const digits = value < 1 ? 5 : value < 100 ? 3 : 2;
    return (currency ? currency + " " : "") +
      value.toLocaleString("pt-BR", {
        minimumFractionDigits: digits,
        maximumFractionDigits: digits
      });
  }

  function formatTime(date = new Date()) {
    return date.toLocaleTimeString("pt-BR");
  }

  function setMarketOptions() {
    const select = $("asset");
    select.innerHTML = "";

    markets[marketKey].assets.forEach((item, index) => {
      const option = document.createElement("option");
      option.value = String(index);
      option.textContent = item.symbol + " — " + item.name;
      select.appendChild(option);
    });

    assetIndex = 0;
    resetPriceSeries();
    $("assetInfo").textContent =
      markets[marketKey].label + " · " + asset().name +
      " · dados sintéticos, não cotações ao vivo.";
  }

  function resetPriceSeries() {
    const base = asset().base;
    currentPrice = base;
    initialPrice = base;
    prices = [];

    for (let i = 0; i < 30; i++) {
      const variation = (Math.random() - 0.5) * 0.006;
      prices.push(base * (1 + variation));
    }

    currentPrice = prices[prices.length - 1];
    initialPrice = prices[0];
    render();
  }

  function nextPrice() {
    const base = asset().base;
    const volatility = marketKey === "forex" ? 0.0008 : 0.0025;
    const randomMove = (Math.random() - 0.49) * volatility;
    const price = Math.max(0.00001, currentPrice * (1 + randomMove));

    currentPrice = price;
    prices.push(price);

    if (prices.length > 100) prices.shift();

    $("updated").textContent = formatTime();
    settleTrades();
    render();
  }

  function average(values) {
    if (!values.length) return 0;
    return values.reduce((sum, n) => sum + n, 0) / values.length;
  }

  function sma(period) {
    if (prices.length < period) return null;
    return average(prices.slice(-period));
  }

  function calculateRSI(period = 14) {
    if (prices.length <= period) return null;

    let gains = 0;
    let losses = 0;

    for (let i = prices.length - period; i < prices.length; i++) {
      const diff = prices[i] - prices[i - 1];
      if (diff > 0) gains += diff;
      if (diff < 0) losses += Math.abs(diff);
    }

    if (gains === 0 && losses === 0) return 50;
    if (losses === 0) return 100;

    const relativeStrength = gains / losses;
    return 100 - (100 / (1 + relativeStrength));
  }

  function analyze() {
    const shortMA = sma(5);
    const longMA = sma(12);
    const rsiValue = calculateRSI(14);

    if (shortMA === null || longMA === null || rsiValue === null) {
      return {
        signal: "AGUARDAR",
        reason: "Coletando dados suficientes.",
        shortMA, longMA, rsiValue
      };
    }

    if (shortMA > longMA && rsiValue >= 45 && rsiValue < 68) {
      return {
        signal: "SUBIDA",
        reason: "Média curta acima da longa e RSI dentro da faixa definida.",
        shortMA, longMA, rsiValue
      };
    }

    if (shortMA < longMA && rsiValue > 32 && rsiValue <= 55) {
      return {
        signal: "QUEDA",
        reason: "Média curta abaixo da longa e RSI dentro da faixa definida.",
        shortMA, longMA, rsiValue
      };
    }

    return {
      signal: "AGUARDAR",
      reason: "Os critérios demonstrativos não estão alinhados.",
      shortMA, longMA, rsiValue
    };
  }

  function renderAnalysis() {
    const result = analyze();
    const signal = $("signal");
    signal.textContent = result.signal;
    signal.className = "signal-title " +
      (result.signal === "SUBIDA" ? "green" :
       result.signal === "QUEDA" ? "red" : "yellow");

    $("reason").textContent = result.reason;
    $("smaShort").textContent =
      result.shortMA === null ? "Calculando..." : quote(result.shortMA);
    $("smaLong").textContent =
      result.longMA === null ? "Calculando..." : quote(result.longMA);
    $("rsi").textContent =
      result.rsiValue === null ? "Calculando..." : result.rsiValue.toFixed(1);
    $("rsiBar").style.width =
      result.rsiValue === null ? "0%" : result.rsiValue + "%";
  }

  function renderChart() {
    const canvas = $("chart");
    const rect = canvas.getBoundingClientRect();
    if (!rect.width || !rect.height) return;

    const ratio = window.devicePixelRatio || 1;
    canvas.width = Math.round(rect.width * ratio);
    canvas.height = Math.round(rect.height * ratio);

    const ctx = canvas.getContext("2d");
    ctx.scale(ratio, ratio);

    const width = rect.width;
    const height = rect.height;
    ctx.clearRect(0, 0, width, height);

    const min = Math.min(...prices);
    const max = Math.max(...prices);
    const range = max - min || Math.abs(max * 0.001) || 1;
    const pad = 18;

    ctx.strokeStyle = "#243148";
    ctx.lineWidth = 1;

    for (let i = 1; i <= 4; i++) {
      const y = (height / 5) * i;
      ctx.beginPath();
      ctx.moveTo(0, y);
      ctx.lineTo(width, y);
      ctx.stroke();
    }

    ctx.strokeStyle = "#2de39a";
    ctx.lineWidth = 2;
    ctx.beginPath();

    prices.forEach((price, index) => {
      const x = pad + (index / Math.max(1, prices.length - 1)) *
        (width - pad * 2);
      const y = height - pad -
        ((price - min) / range) * (height - pad * 2);

      if (index === 0) ctx.moveTo(x, y);
      else ctx.lineTo(x, y);
    });

    ctx.stroke();

    ctx.fillStyle = "#94a3b8";
    ctx.font = "11px Arial";
    ctx.fillText(quote(max), 8, 14);
    ctx.fillText(quote(min), 8, height - 5);
  }

  function renderHistory() {
    const tbody = $("history");

    if (!history.length) {
      tbody.innerHTML =
        '<tr><td colspan="7" class="muted">Nenhuma operação realizada.</td></tr>';
      return;
    }

    tbody.innerHTML = history.slice(0, 50).map(item => {
      const resultClass = item.result > 0 ? "green" :
        item.result < 0 ? "red" : "muted";

      return `<tr>
        <td>${item.time}</td>
        <td>${item.market}</td>
        <td>${item.asset}</td>
        <td>${item.direction}</td>
        <td>${money(item.stake)}</td>
        <td class="${resultClass}">${item.result > 0 ? "+" : ""}${money(item.result)}</td>
        <td>${money(item.balance)}</td>
      </tr>`;
    }).join("");
  }

  function renderOpenTrades() {
    if (!openTrades.length) {
      $("openTrades").textContent = "Nenhuma operação em andamento.";
      return;
    }

    $("openTrades").innerHTML = openTrades.map(trade => {
      const seconds = Math.max(0, Math.ceil((trade.endAt - Date.now()) / 1000));
      return `<div class="metric" style="margin-bottom:8px">
        <strong>${trade.direction} · ${trade.asset}</strong>
        <span>${money(trade.stake)} · faltam ${seconds}s</span>
      </div>`;
    }).join("");
  }

  function render() {
    $("balance").textContent = money(balance);
    $("profit").textContent = (cumulativeResult > 0 ? "+" : "") +
      money(cumulativeResult);
    $("profit").className = "metric-value " +
      (cumulativeResult > 0 ? "green" :
       cumulativeResult < 0 ? "red" : "");
    $("tradeCount").textContent = String(history.length);
    $("price").textContent = quote(currentPrice);

    const change = initialPrice ? ((currentPrice - initialPrice) / initialPrice) * 100 : 0;
    $("change").textContent = (change > 0 ? "+" : "") + change.toFixed(3) + "%";
    $("change").className = "metric-value " +
      (change > 0 ? "green" : change < 0 ? "red" : "");

    renderAnalysis();
    renderChart();
    renderHistory();
    renderOpenTrades();
  }

  function createTrade(direction, automatic = false) {
    const stake = Number($("stake").value);
    const duration = Number($("duration").value);

    if (!Number.isFinite(stake) || stake < 1 || stake > MAX_STAKE) {
      alert("Escolha um valor entre R$ 1,00 e R$ 5,00 por operação.");
      return false;
    }

    if (stake > balance) {
      alert("Saldo virtual insuficiente para essa operação.");
      return false;
    }

    if (cumulativeResult <= -MAX_CUMULATIVE_LOSS) {
      if (automatic) stopAuto("Limite de perda acumulada atingido.");
      else alert("Limite de perda acumulada de R$ 10,00 atingido. Reinicie o simulador para testar novamente.");
      return false;
    }

    balance -= stake;

    openTrades.push({
      id: tradeId++,
      direction,
      stake,
      startPrice: currentPrice,
      endAt: Date.now() + duration * 1000,
      market: markets[marketKey].label,
      marketKey,
      asset: asset().symbol,
      automatic
    });

    render();

    if (automatic) {
      $("autoStatus").textContent =
        "Modo automático ativo: operações exclusivamente virtuais.";
    }

    return true;
  }

  function settleTrades() {
    const now = Date.now();
    const stillOpen = [];

    for (const trade of openTrades) {
      if (now < trade.endAt) {
        stillOpen.push(trade);
        continue;
      }

      const upWon = currentPrice > trade.startPrice;
      const downWon = currentPrice < trade.startPrice;
      const won = trade.direction === "SUBIDA" ? upWon : downWon;

      let result = -trade.stake;

      if (won) {
        result = trade.stake * PAYOUT;
        balance += trade.stake + result;
      } else if (currentPrice === trade.startPrice) {
        result = 0;
        balance += trade.stake;
      }

      cumulativeResult += result;

      history.unshift({
        time: formatTime(),
        market: trade.market,
        asset: trade.asset,
        direction: trade.direction,
        stake: trade.stake,
        result,
        balance
      });
    }

    openTrades = stillOpen;

    if (cumulativeResult <= -MAX_CUMULATIVE_LOSS && $("autoMode").checked) {
      stopAuto("Limite de perda acumulada atingido.");
    }

    if (balance < 2 && $("autoMode").checked) {
      stopAuto("Saldo virtual abaixo de R$ 2,00.");
    }
  }

  function stopAuto(message) {
    $("autoMode").checked = false;
    if (autoTimer) clearInterval(autoTimer);
    autoTimer = null;
    $("autoStatus").textContent = message;
  }

  function startAuto() {
    if (autoTimer) clearInterval(autoTimer);

    $("autoStatus").textContent =
      "Modo automático ativo: apenas simulações, sem ordens reais.";

    autoTimer = setInterval(() => {
      if (!$("autoMode").checked) {
        stopAuto("Automação desligada.");
        return;
      }

      if (balance < 2) {
        stopAuto("Saldo virtual abaixo de R$ 2,00.");
        return;
      }

      if (cumulativeResult <= -MAX_CUMULATIVE_LOSS) {
        stopAuto("Limite de perda acumulada atingido.");
        return;
      }

      // Evita abrir muitas operações simultaneamente.
      if (openTrades.length > 0) return;

      // Dá tempo para o indicador mudar antes de uma nova operação.
      if (Date.now() - lastAutoTrade < 10000) return;

      const result = analyze();
      if (result.signal === "SUBIDA" || result.signal === "QUEDA") {
        const created = createTrade(result.signal, true);
        if (created) lastAutoTrade = Date.now();
      }
    }, 1000);
  }

  $("market").addEventListener("change", event => {
    stopAuto("Mercado alterado. Confira o novo ativo antes de reativar a automação.");
    marketKey = event.target.value;
    setMarketOptions();
  });

  $("asset").addEventListener("change", event => {
    stopAuto("Ativo alterado. Confira o novo ativo antes de reativar a automação.");
    assetIndex = Number(event.target.value);
    $("assetInfo").textContent =
      markets[marketKey].label + " · " + asset().name +
      " · dados sintéticos, não cotações ao vivo.";
    resetPriceSeries();
  });

  $("upBtn").addEventListener("click", () => createTrade("SUBIDA"));
  $("downBtn").addEventListener("click", () => createTrade("QUEDA"));

  $("autoMode").addEventListener("change", event => {
    if (event.target.checked) {
      startAuto();
    } else {
      stopAuto("Automação desligada. As operações já abertas continuam até o encerramento.");
    }
  });

  $("resetBtn").addEventListener("click", () => {
    if (!confirm("Deseja reiniciar o saldo virtual para R$ 50,00 e cancelar as operações em andamento?")) {
      return;
    }

    stopAuto("Saldo reiniciado. Automação desligada.");
    balance = INITIAL_BALANCE;
    cumulativeResult = 0;
    history = [];
    openTrades = [];
    render();
  });

  $("clearBtn").addEventListener("click", () => {
    if (!confirm("Deseja limpar o histórico de operações? O saldo atual será mantido.")) {
      return;
    }

    history = [];
    render();
  });

  window.addEventListener("resize", renderChart);

  setMarketOptions();
  render();

  // Atualização de preço apenas dentro da simulação local.
  setInterval(nextPrice, 1000);
  setInterval(renderOpenTrades, 500);
})();
</script>
</body>
</html>
