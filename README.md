<!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8">
<meta name="viewport" content="width=device-width, initial-scale=1">
<meta name="theme-color" content="#0b1020">
<title>LA Trade Lab — Simulador</title>
<style>
:root {
  --bg:#0b1020; --card:#151e32; --card2:#0a1428;
  --text:#edf2ff; --muted:#98a6c3; --blue:#83aaff;
  --green:#36d69a; --red:#ff6688; --yellow:#f5c76a;
}
* {box-sizing:border-box}
body {
  margin:0; background:var(--bg); color:var(--text);
  font:16px/1.5 system-ui,-apple-system,sans-serif;
}
main {max-width:760px; margin:auto; padding:18px 14px 40px}
header {padding:10px 2px 18px}
h1 {font-size:25px; margin:0}
h2 {font-size:19px; margin:0 0 16px}
p {margin:7px 0}
.small {font-size:13px;color:var(--muted)}
.card {
  background:var(--card); border-radius:22px;
  padding:22px; margin-bottom:18px;
}
.balance {font-size:36px;font-weight:750;letter-spacing:-1px}
.stats {
  display:grid;grid-template-columns:repeat(2,minmax(0,1fr));
  gap:12px;margin-top:20px
}
.stat {background:var(--card2);border-radius:13px;padding:13px}
.stat span {display:block;color:var(--muted);font-size:12px}
.stat strong {display:block;font-size:20px;margin-top:3px}
.price {font-size:24px;font-weight:700}
canvas {
  display:block;width:100%;height:210px;background:var(--card2);
  border-radius:12px;margin:14px 0
}
.signal {font-size:20px;font-weight:750;margin:12px 0}
.up {color:var(--green)} .down {color:var(--red)}
.wait {color:var(--yellow)}
.pill {
  display:inline-block;border-radius:999px;padding:5px 10px;
  background:var(--card2);font-size:12px;color:var(--muted)
}
label {display:block;color:var(--muted);font-size:13px;margin:13px 0 6px}
input,select {
  width:100%;padding:12px;border:1px solid #34415c;border-radius:10px;
  background:var(--card2);color:var(--text);font:inherit
}
.buttons {display:grid;grid-template-columns:1fr 1fr;gap:10px;margin-top:14px}
button {
  border:0;border-radius:11px;padding:13px 10px;font:inherit;
  font-weight:750;cursor:pointer
}
button:disabled {opacity:.35;cursor:not-allowed}
.buy {background:#176b53;color:white}
.sell {background:#87364c;color:white}
.secondary {background:#263550;color:var(--text);width:100%;margin-top:10px}
.primary {background:var(--blue);color:#071020;width:100%;margin-top:10px}
.note {
  background:#101a2c;border-left:3px solid var(--blue);
  padding:12px;border-radius:8px;font-size:13px;color:var(--muted)
}
.notice {color:var(--yellow);font-weight:650;margin-top:10px}
table {width:100%;border-collapse:collapse;font-size:13px}
th,td {text-align:left;padding:10px 5px;border-bottom:1px solid #2b3851}
th {color:var(--muted);font-weight:500}
#history {overflow-x:auto}
.metric {color:var(--muted);font-size:13px;margin-top:12px}
@media(max-width:420px) {
  .card{padding:17px}.balance{font-size:32px}
  .stats{gap:8px}.stat strong{font-size:17px}
}
</style>
</head>
<body>
<main>
<header>
  <div class="pill">LA TRADE LAB · VERSÃO EDUCATIVA</div>
  <h1 style="margin-top:10px">Simulador de mercado</h1>
  <p class="small">Análise técnica didática com dados totalmente sintéticos.</p>
</header>

<section class="card">
  <p class="small">Saldo virtual</p>
  <div id="balance" class="balance">R$ 50,00</div>
  <p>Resultado: <strong id="profit">R$ 0,00</strong></p>
  <p class="small" id="operationCount">Operações: 0 | Acertos: 0</p>
  <div class="stats">
    <div class="stat"><span>Taxa de acerto</span><strong id="winRate">—</strong></div>
    <div class="stat"><span>Perda diária restante</span><strong id="riskLeft">R$ 5,00</strong></div>
    <div class="stat"><span>Sequência de perdas</span><strong id="streak">0</strong></div>
    <div class="stat"><span>Maior queda do saldo</span><strong id="drawdown">R$ 0,00</strong></div>
  </div>
</section>

<section class="card">
  <h2>Mercado simulado</h2>
  <div class="small">Preço sintético (sem conexão com o mercado real)</div>
  <div class="price" id="price">1.08342</div>
  <canvas id="chart" width="700" height="260"></canvas>
  <div id="signal" class="signal wait">Sinal didático: AGUARDAR</div>
  <p id="signalReason" class="small">Coletando dados sintéticos...</p>
  <div class="stats">
    <div class="stat"><span>Média curta (5)</span><strong id="maFast">—</strong></div>
    <div class="stat"><span>Média longa (12)</span><strong id="maSlow">—</strong></div>
    <div class="stat"><span>RSI (14)</span><strong id="rsi">—</strong></div>
    <div class="stat"><span>Movimento recente</span><strong id="momentum">—</strong></div>
  </div>
  <p class="small" style="margin-top:14px">
    Os preços são aleatórios e gerados localmente. Os sinais não são previsões
    confiáveis nem recomendações de investimento.
  </p>
</section>

<section class="card">
  <h2>Configurar operação</h2>
  <label for="stake">Valor de entrada (R$)</label>
  <input id="stake" type="number" min="1" max="2" step="1" value="2">
  <label for="payout">Retorno simulado sobre a entrada</label>
  <select id="payout"><option value="0.8">80%</option></select>
  <label for="duration">Duração simulada</label>
  <select id="duration">
    <option value="10">10 segundos</option>
    <option value="30">30 segundos</option>
    <option value="60">1 minuto</option>
  </select>
  <div class="buttons">
    <button id="upBtn" class="buy">↗ SUBIDA</button>
    <button id="downBtn" class="sell">↘ QUEDA</button>
  </div>
  <button id="autoBtn" class="primary">Ativar modo automático</button>
  <button id="resetBtn" class="secondary">Reiniciar simulação</button>
  <p id="tradeStatus" class="metric">Nenhuma operação em andamento.</p>
  <p id="riskStatus" class="notice"></p>
</section>

<section class="card">
  <h2>Regras de proteção</h2>
  <p>• Saldo inicial: R$ 50,00.</p>
  <p>• Entrada padrão: R$ 2,00 (4% do saldo inicial).</p>
  <p>• Limite de perda: R$ 5,00 por sessão.</p>
  <p>• Pausa após 3 perdas consecutivas.</p>
  <p>• Uma operação por vez; sem aumentar a entrada para recuperar perdas.</p>
  <p>• O modo automático só opera quando os critérios técnicos concordam.</p>
  <div class="note">
    Essas proteções limitam a exposição dentro desta simulação. Elas não
    garantem lucro, não melhoram necessariamente a taxa de acerto e não
    representam proteção de dinheiro real.
  </div>
</section>

<section class="card">
  <h2>Histórico de operações</h2>
  <div id="history"><p class="small">Nenhuma operação concluída.</p></div>
  <p class="metric" id="summary">Aguardando resultados.</p>
</section>
</main>

<script>
(() => {
  "use strict";

  const INITIAL = 50;
  const DAILY_LIMIT = 5;
  const MAX_STREAK = 3;
  const TICK_MS = 1000;
  const $ = id => document.getElementById(id);
  const money = n => n.toLocaleString("pt-BR", {
    style:"currency", currency:"BRL"
  });
  const fmtPrice = n => n.toFixed(5);

  let balance = INITIAL;
  let prices = [];
  let history = [];
  let operations = 0;
  let wins = 0;
  let losses = 0;
  let lossStreak = 0;
  let net = 0;
  let peak = INITIAL;
  let maxDrawdown = 0;
  let auto = false;
  let pending = null;
  let nextId = 1;
  let pausedReason = "";
  let lastSignal = "AGUARDAR";
  let lastAnalysis = {};
  let timer = null;
  let autoCooldown = 0;

  // Dados inventados para fins educativos; não são cotações reais.
  let syntheticPrice = 1.08342;
  function makeTick() {
    const noise = (Math.random() - 0.5) * 0.0009;
    const drift = (Math.random() - 0.5) * 0.00008;
    syntheticPrice = Math.max(0.5, syntheticPrice + noise + drift);
    prices.push(syntheticPrice);
    if (prices.length > 120) prices.shift();
  }

  function average(arr) {
    return arr.reduce((a,b) => a+b, 0) / arr.length;
  }

  function calcRSI(arr, period=14) {
    if (arr.length < period + 1) return null;
    let gains = 0, declines = 0;
    for (let i=arr.length-period; i<arr.length; i++) {
      const diff = arr[i] - arr[i-1];
      if (diff > 0) gains += diff;
      else declines -= diff;
    }
    if (gains === 0 && declines === 0) return 50;
    if (declines === 0) return 100;
    const rs = gains / declines;
    return 100 - (100 / (1 + rs));
  }

  function analyze() {
    if (prices.length < 20) {
      lastSignal = "AGUARDAR";
      lastAnalysis = {reason:"Aguardando pelo menos 20 pontos sintéticos."};
      return;
    }

    const fast = average(prices.slice(-5));
    const slow = average(prices.slice(-12));
    const rsi = calcRSI(prices, 14);
    const recent = prices[prices.length-1] - prices[prices.length-4];
    const momentum = recent / prices[prices.length-4] * 100;

    let signal = "AGUARDAR";
    let reason = "Os critérios não estão suficientemente alinhados.";

    // Filtros conservadores didáticos. Não são um modelo preditivo validado.
    if (fast > slow && momentum > 0.005 && rsi >= 52 && rsi <= 65) {
      signal = "SUBIDA";
      reason = "Médias, momentum e RSI estão alinhados para cima.";
    } else if (fast < slow && momentum < -0.005 && rsi >= 35 && rsi <= 48) {
      signal = "QUEDA";
      reason = "Médias, momentum e RSI estão alinhados para baixo.";
    }

    lastSignal = signal;
    lastAnalysis = {fast, slow, rsi, momentum, reason};
  }

  function canTrade() {
    if (pausedReason) return false;
    if (pending) return false;
    if (INITIAL - balance >= DAILY_LIMIT - 0.00001) {
      pausedReason = "Limite de perda da sessão atingido.";
      return false;
    }
    if (lossStreak >= MAX_STREAK) {
      pausedReason = "Pausa após 3 perdas consecutivas.";
      return false;
    }
    const stake = Number($("stake").value);
    if (!Number.isFinite(stake) || stake <= 0 || stake > 2) return false;
    if (balance < stake) {
      pausedReason = "Saldo insuficiente para a entrada.";
      return false;
    }
    return true;
  }

  function renderChart() {
    const canvas = $("chart");
    const ctx = canvas.getContext("2d");
    const w = canvas.width, h = canvas.height;
    ctx.clearRect(0,0,w,h);
    ctx.fillStyle = "#0a1428";
    ctx.fillRect(0,0,w,h);
    if (prices.length < 2) return;

    const min = Math.min(...prices);
    const max = Math.max(...prices);
    const range = max - min || 0.0001;
    ctx.strokeStyle = "#83aaff";
    ctx.lineWidth = 2.5;
    ctx.beginPath();
    prices.forEach((p,i) => {
      const x = i / (prices.length-1) * (w-16) + 8;
      const y = h-12 - ((p-min)/range)*(h-24);
      if (i===0) ctx.moveTo(x,y);
      else ctx.lineTo(x,y);
    });
    ctx.stroke();
  }

  function render() {
    analyze();
    $("balance").textContent = money(balance);
    $("profit").textContent = money(net);
    $("profit").style.color = net >= 0 ? "var(--green)" : "var(--red)";
    $("operationCount").textContent =
      `Operações: ${operations} | Acertos: ${wins} | Erros: ${losses}`;
    $("winRate").textContent = operations ? `${(wins/operations*100).toFixed(1)}%` : "—";
    $("riskLeft").textContent = money(Math.max(0, DAILY_LIMIT - Math.max(0, INITIAL-balance)));
    $("streak").textContent = String(lossStreak);
    $("drawdown").textContent = money(maxDrawdown);
    $("price").textContent = fmtPrice(prices[prices.length-1] || syntheticPrice);
    $("signal").textContent = "Sinal didático: " + lastSignal;
    $("signal").className = "signal " +
      (lastSignal==="SUBIDA" ? "up" : lastSignal==="QUEDA" ? "down" : "wait");
    $("signalReason").textContent = lastAnalysis.reason || "";
    $("maFast").textContent = lastAnalysis.fast ? fmtPrice(lastAnalysis.fast) : "—";
    $("maSlow").textContent = lastAnalysis.slow ? fmtPrice(lastAnalysis.slow) : "—";
    $("rsi").textContent = lastAnalysis.rsi != null ? lastAnalysis.rsi.toFixed(1) : "—";
    $("momentum").textContent = lastAnalysis.momentum != null
      ? (lastAnalysis.momentum >= 0 ? "+" : "") + lastAnalysis.momentum.toFixed(3) + "%"
      : "—";

    $("upBtn").disabled = !canTrade();
    $("downBtn").disabled = !canTrade();
    $("autoBtn").textContent = auto ? "Desativar modo automático" : "Ativar modo automático";
    $("riskStatus").textContent = pausedReason;
    $("riskStatus").style.color = pausedReason ? "var(--red)" : "var(--yellow)";
    if (!pending && !pausedReason) {
      $("tradeStatus").textContent = auto
        ? "Modo automático ativo: aguardando um sinal válido."
        : "Nenhuma operação em andamento.";
    }
    $("summary").textContent = operations
      ? `Resultado líquido: ${money(net)} · ${wins} acertos · ${losses} erros`
      : "Aguardando resultados.";
    renderHistory();
    renderChart();
  }

  function renderHistory() {
    if (!history.length) {
      $("history").innerHTML = '<p class="small">Nenhuma operação concluída.</p>';
      return;
    }
    const rows = history.slice(0,20).map(h => `
      <tr>
        <td>#${h.id}</td>
        <td>${h.direction}</td>
        <td>${money(h.stake)}</td>
        <td style="color:${h.win ? 'var(--green)' : 'var(--red)'}">${h.win ? "Acerto" : "Erro"}</td>
        <td>${money(h.result)}</td>
      </tr>`).join("");
    $("history").innerHTML = `
      <table><thead><tr><th>#</th><th>Direção</th><th>Entrada</th>
      <th>Resultado</th><th>Variação</th></tr></thead><tbody>${rows}</tbody></table>`;
  }

  function startTrade(direction, isAuto=false) {
    if (!canTrade()) { render(); return; }
    if (isAuto && direction !== lastSignal) return;

    const stake = Number($("stake").value);
    const duration = Number($("duration").value);
    const payout = Number($("payout").value);

    if (direction !== "SUBIDA" && direction !== "QUEDA") return;
    if (stake > balance) return;

    const startPrice = prices[prices.length-1];
    pending = {
      id: nextId++, direction, stake, payout, duration,
      startPrice, ticks:0, startTime:Date.now()
    };
    $("tradeStatus").textContent =
      `Operação ${pending.id}: ${direction} · encerrando em ${duration}s`;
    $("upBtn").disabled = true;
    $("downBtn").disabled = true;
  }

  function finishTrade() {
    if (!pending) return;
    const trade = pending;
    pending = null;

    // Resultado depende apenas do movimento sintético ocorrido durante a operação.
    const endPrice = prices[prices.length-1];
    let move = endPrice - trade.startPrice;
    if (Math.abs(move) < 0.0000001) move = Math.random() < 0.5 ? 0.0000001 : -0.0000001;
    const win = trade.direction === "SUBIDA" ? move > 0 : move < 0;
    const result = win ? trade.stake * trade.payout : -trade.stake;

    balance += result;
    net += result;
    operations++;
    if (win) {
      wins++;
      lossStreak = 0;
    } else {
      losses++;
      lossStreak++;
    }
    peak = Math.max(peak, balance);
    maxDrawdown = Math.max(maxDrawdown, peak - balance);
    history.unshift({
      id:trade.id, direction:trade.direction, stake:trade.stake,
      win, result, startPrice:trade.startPrice, endPrice
    });

    $("tradeStatus").textContent =
      `Operação ${trade.id} finalizada: ${win ? "ACERTO" : "ERRO"} · ${money(result)}`;

    if (INITIAL - balance >= DAILY_LIMIT - 0.00001) {
      pausedReason = "Limite de perda de R$ 5,00 atingido. Sessão pausada.";
      auto = false;
    } else if (lossStreak >= MAX_STREAK) {
      pausedReason = "3 perdas consecutivas. Sessão pausada por segurança.";
      auto = false;
    } else {
      autoCooldown = 3;
    }
    render();
  }

  $("upBtn").addEventListener("click", () => startTrade("SUBIDA"));
  $("downBtn").addEventListener("click", () => startTrade("QUEDA"));

  $("autoBtn").addEventListener("click", () => {
    if (auto) {
      auto = false;
    } else if (!pausedReason) {
      auto = true;
    }
    render();
  });

  $("resetBtn").addEventListener("click", () => {
    auto = false;
    pending = null;
    balance = INITIAL;
    history = [];
    operations = wins = losses = lossStreak = 0;
    net = 0;
    peak = INITIAL;
    maxDrawdown = 0;
    pausedReason = "";
    nextId = 1;
    autoCooldown = 0;
    $("tradeStatus").textContent = "Simulação reiniciada.";
    render();
  });

  $("stake").addEventListener("change", () => {
    let value = Number($("stake").value);
    if (!Number.isFinite(value)) value = 2;
    value = Math.max(1, Math.min(2, value));
    $("stake").value = value;
    render();
  });

  function tick() {
    makeTick();

    if (pending) {
      pending.ticks++;
      const remaining = Math.max(0, pending.duration - pending.ticks);
      $("tradeStatus").textContent =
        `Operação ${pending.id}: ${pending.direction} · ${remaining}s restantes`;
      if (pending.ticks >= pending.duration) finishTrade();
    } else {
      if (autoCooldown > 0) autoCooldown--;
      if (auto && !pausedReason && autoCooldown <= 0 &&
          (lastSignal === "SUBIDA" || lastSignal === "QUEDA") &&
          canTrade()) {
        startTrade(lastSignal, true);
      }
    }
    render();
  }

  // Inicia com uma série suficiente para calcular médias e RSI.
  for (let i=0; i<30; i++) makeTick();
  render();
  timer = setInterval(tick, TICK_MS);
})();
</script>
</body>
</html>
