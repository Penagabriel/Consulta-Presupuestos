<!DOCTYPE html>
<html>
<head>
  <meta charset="UTF-8">
  <title>Consulta de Presupuestos</title>
  <style>
    body { font-family: Arial; padding: 20px; }
    table { border-collapse: collapse; width: 100%; margin-top: 20px; }
    th, td { border: 1px solid #ddd; padding: 8px; text-align: left; }
    th { background-color: #f2f2f2; }
    .Pendiente { background-color: #ffeb99; }
    .Pedido { background-color: #99ccff; }
    .Entregado { background-color: #99ff99; }
  </style>
</head>
<body>
  <h2>Consulta de Presupuesto</h2>
  <label>Número de Presupuesto:</label><br>
  <input type="text" id="numPresupuesto"><br><br>
  <label>Contraseña:</label><br>
  <input type="password" id="clave"><br><br>
  <button onclick="consultar()">Consultar</button>

  <div id="resultados"></div>

  <script>
    const sheetCSV = 'https://docs.google.com/spreadsheets/d/e/2PACX-1vRXBKjq1NdPKPGgpg5wRVozG_S63BkoD1Lx8FTog2YIMWe0ysxaXhCxWEuGKbioigKzN6QP5IYvyToZ/pub?gid=1664351147&single=true&output=csv'; // tu link CSV

    let datos = [];

    // Cargar CSV y parsear
    fetch(sheetCSV)
      .then(response => response.text())
      .then(csvText => {
        const filas = csvText.split('\n');
        const encabezados = filas[0].split(',');
        datos = filas.slice(1).map(f => {
          const celdas = f.split(',');
          let obj = {};
          encabezados.forEach((h,i) => { obj[h.trim()] = celdas[i]; });
          return obj;
        });
      });

    function consultar() {
      const num = document.getElementById('numPresupuesto').value.trim();
      const clave = document.getElementById('clave').value.trim();
      const resultados = datos.filter(f => f['N"'] === num && f['CONTRASEÑA'] === clave);

      let html = "";
      if(resultados.length === 0){
        html = "<p>Presupuesto o contraseña incorrectos</p>";
      } else {
        html = "<table><tr><th>Denominación</th><th>Cantidad</th><th>Precio Unitario</th><th>Monto</th><th>Estado</th><th>Observación</th></tr>";
        resultados.forEach(r => {
          html += `<tr class="${r['ESTADO']}"><td>${r['DENOMINACIÓN']}</td><td>${r['CANTIDAD']}</td><td>${r['PRECIO UNITARIO']}</td><td>${r['MONTO']}</td><td>${r['ESTADO']}</td><td>${r['OBSERVACIÓN']}</td></tr>`;
        });
        html += "</table>";
      }
      document.getElementById('resultados').innerHTML = html;
    }
  </script>
</body>
</html>
