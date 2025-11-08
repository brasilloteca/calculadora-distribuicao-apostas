# calculadora-distribuicao-apostas
          <!DOCTYPE html>
<html lang="pt-BR">
<head>
<meta charset="UTF-8" />
<meta name="viewport" content="width=device-width, initial-scale=1.0" />
<title>Calculadora de Distribuição de Apostas</title>
<style>
  body {
    font-family: Arial, sans-serif;
    background: #f5f5f5;
    color: #222;
    margin: 0;
    padding: 0;
  }
  .container {
    max-width: 750px;
    margin: 0 auto;
    padding: 20px;
  }
  h1 {
    text-align: center;
    margin-bottom: 15px;
  }
  label {
    display: block;
    margin-bottom: 5px;
    font-weight: bold;
  }
  input[type="number"] {
    width: 100%;
    padding: 8px;
    margin-bottom: 12px;
    border-radius: 8px;
    border: 1px solid #ccc;
    font-size: 1em;
  }
  .grid {
    display: grid;
    grid-template-columns: repeat(auto-fit, minmax(150px, 1fr));
    gap: 10px;
  }
  button {
    width: 100%;
    background: #0078ff;
    color: white;
    padding: 12px;
    font-size: 1.1em;
    border: none;
    border-radius: 8px;
    cursor: pointer;
  }
  button:hover {
    background: #005fcc;
  }
  table {
    width: 100%;
    border-collapse: collapse;
    margin-top: 20px;
  }
  th, td {
    border: 1px solid #ddd;
    text-align: center;
    padding: 8px;
  }
  th {
    background-color: #0078ff;
    color: white;
  }
  .footer {
    text-align: center;
    margin-top: 30px;
    font-size: 0.9em;
    color: #555;
  }
  .resultados {
    background: #e8f0ff;
    border: 1px solid #0078ff;
    padding: 12px;
    border-radius: 8px;
    margin-top: 15px;
    font-weight: bold;
  }
</style>
</head>
<body>
  <div class="container">
    <h1>Calculadora de Distribuição de Apostas</h1>

    <label for="total">Valor total da aposta (R$):</label>
    <input type="number" id="total" placeholder="Ex: 100" step="0.01" min="0" />

    <label>Digite até 20 cotações:</label>
    <div class="grid" id="odds-container">
      <script>
        for (let i = 1; i <= 20; i++) {
          document.write(`<input type="number" step="0.01" min="1.01" id="odd${i}" placeholder="Odd ${i}" />`);
        }
      </script>
    </div>

    <label for="erros">Quantas apostas erraram?</label>
    <input type="number" id="erros" placeholder="Ex: 2" min="0" max="20" />

    <button onclick="calcular()">Calcular Distribuição</button>

    <table id="result" style="display:none;">
      <thead>
        <tr>
          <th>#</th>
          <th>Odd</th>
          <th>Valor a Apostar (R$)</th>
          <th>Retorno Individual (R$)</th>
        </tr>
      </thead>
      <tbody></tbody>
    </table>

    <div id="resumo" class="resultados" style="display:none;"></div>

    <div class="footer">Desenvolvido para uso pessoal - Cálculo baseado em retornos iguais</div>
  </div>

  <script>
    function calcular() {
      const total = parseFloat(document.getElementById("total").value);
      const erros = parseInt(document.getElementById("erros").value) || 0;

      if (isNaN(total) || total <= 0) {
        alert("Digite um valor total válido.");
        return;
      }

      const odds = [];
      for (let i = 1; i <= 20; i++) {
        const val = parseFloat(document.getElementById(`odd${i}`).value);
        if (!isNaN(val) && val > 1) odds.push(val);
      }

      if (odds.length === 0) {
        alert("Digite pelo menos uma cotação válida.");
        return;
      }

      if (erros >= odds.length) {
        alert("O número de apostas erradas deve ser menor que o total de apostas.");
        return;
      }

      const somaInversos = odds.reduce((s, o) => s + 1 / o, 0);
      const resultados = odds.map(o => (total / o) / somaInversos);
      const retornos = odds.map((o, i) => resultados[i] * o);

      // montar tabela
      const tabela = document.getElementById("result");
      const tbody = tabela.querySelector("tbody");
      tbody.innerHTML = "";
      retornos.forEach((ret, i) => {
        const linha = document.createElement("tr");
        linha.innerHTML = `
          <td>${i + 1}</td>
          <td>${odds[i].toFixed(2)}</td>
          <td>${resultados[i].toFixed(2)}</td>
          <td>${ret.toFixed(2)}</td>
        `;
        tbody.appendChild(linha);
      });
      tabela.style.display = "table";

      // ordenar retornos decrescentes e excluir os erros
      const retornosValidos = [...retornos].sort((a, b) => b - a).slice(0, odds.length - erros);
      const somaVencedores = retornosValidos.reduce((s, r) => s + r, 0);

      // exibir resumo
      const resumo = document.getElementById("resumo");
      resumo.innerHTML = `
        🧮 <b>Total apostado:</b> R$ ${total.toFixed(2)}<br>
        ❌ <b>Apostas erradas:</b> ${erros}<br>
        ✅ <b>Apostas vencedoras consideradas:</b> ${odds.length - erros}<br>
        💰 <b>Soma dos retornos vencedores:</b> R$ ${somaVencedores.toFixed(2)}<br>
        📊 <b>Lucro líquido (aproximado):</b> R$ ${(somaVencedores - total).toFixed(2)}
      `;
      resumo.style.display = "block";
    }
  </script>
</body>
</html>
