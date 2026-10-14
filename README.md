<!DOCTYPE html>
<html lang="id">

<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1">
    <title>Dashboard Statistik</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        * { box-sizing: border-box }
        body {
            margin: 0;
            font-family: Inter, "Segoe UI", Arial, sans-serif;
            background: #f5f7fb;
            color: #172033;
            line-height: 1.6
        }
        header {
            background: linear-gradient(120deg, #172554 0%, #1e40af 58%, #2563eb 100%);
            color: #fff;
            padding: 52px max(6%, calc((100% - 1150px) / 2));
            border-bottom: 4px solid #93c5fd
        }
        header h1 { margin: 0 0 8px; font-size: clamp(1.8rem, 4vw, 2.5rem); letter-spacing: -.04em }
        header p { margin: 0; color: #dbeafe; font-size: 1.05rem }
        main {
            max-width: 1150px;
            margin: 30px auto 48px;
            padding: 0 22px
        }
        .cards {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(210px, 1fr));
            gap: 18px;
            margin-bottom: 26px
        }
        .card {
            background: #fff;
            border: 1px solid #e7ecf4;
            border-radius: 16px;
            padding: 22px 24px;
            box-shadow: 0 8px 24px #1725540a;
            border-top: 3px solid #3b82f6
        }
        section {
            background: #fff;
            border: 1px solid #e7ecf4;
            border-radius: 16px;
            padding: 26px;
            margin-bottom: 22px;
            box-shadow: 0 8px 24px #1725540a
        }
        .card p { color: #64748b; margin: 0 0 8px; font-size: .92rem }
        .card h2 { margin: 0; color: #1d4ed8; font-size: 1.9rem; line-height: 1.2 }
        h2 { margin: 0 0 18px; color: #172554; font-size: 1.25rem; letter-spacing: -.02em }
        h3 { margin: 24px 0 10px; color: #334155; font-size: 1rem }
        section > h3:first-of-type { margin-top: 22px }
        p { margin: 10px 0 }
        .table-wrap { overflow-x: auto; border: 1px solid #e2e8f0; border-radius: 10px }
        .table-top { margin-top: 20px }
        table { width: 100%; border-collapse: collapse; min-width: 600px }
        th, td { padding: 12px 14px; border-bottom: 1px solid #e8edf5; text-align: center }
        th { background: #eff4ff; color: #334155; font-size: .88rem; font-weight: 650 }
        tbody tr:last-child td { border-bottom: 0 }
        tbody tr:hover { background: #f8faff }
        .insight { line-height: 1.8; color: #475569 }
        .insight p { margin: 8px 0 }
        #inputKelompok {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(190px, 1fr));
            gap: 12px;
            margin: 18px 0
        }
        #inputKelompok p { margin: 0 }
        .equation {
            overflow-wrap: anywhere;
            padding: 14px 16px;
            background: #eff6ff;
            border: 1px solid #dbeafe;
            border-radius: 10px;
            color: #1e40af;
            font-weight: 650
        }
        input {
            width: 100%;
            padding: 11px 12px;
            border: 1px solid #cbd5e1;
            border-radius: 9px;
            font: inherit;
            color: #172033;
            background: #fff
        }
        input:focus { outline: 3px solid #bfdbfe; border-color: #3b82f6 }
        button {
            border: 0;
            border-radius: 9px;
            padding: 12px 18px;
            background: #2563eb;
            color: #fff;
            font: inherit;
            font-weight: 650;
            cursor: pointer;
            transition: background .2s, transform .2s
        }
        button:hover { background: #1d4ed8; transform: translateY(-1px) }
        button:focus-visible { outline: 3px solid #bfdbfe; outline-offset: 2px }
        canvas { max-height: 400px }
        footer { text-align: center; color: #64748b; padding: 25px; border-top: 1px solid #e2e8f0 }
        @media (max-width: 600px) {
            header { padding: 36px 22px }
            main { margin-top: 20px; padding: 0 14px }
            section { padding: 20px 16px }
            .cards { grid-template-columns: repeat(2, minmax(0, 1fr)); gap: 12px }
            .card { padding: 18px 16px }
            .card h2 { font-size: 1.55rem }
        }
    </style>
</head>

<body>
    <header>
        <h1>Dashboard Statistik Deskriptif</h1>
        <p>Analisis data pengunjung, penjualan, dan regresi linear</p>
    </header>

    <main>
        <div class="cards">
            <div class="card">
                <p>Total hari pengamatan</p>
                <h2 id="jumlah">10</h2>
            </div>
            <div class="card">
                <p>Rata-rata pengunjung</p>
                <h2 id="meanX">-</h2>
            </div>
            <div class="card">
                <p>Rata-rata penjualan</p>
                <h2 id="meanY">-</h2>
            </div>
            <div class="card">
                <p>Korelasi pengunjung &amp; penjualan</p>
                <h2 id="korelasi">-</h2>
            </div>
        </div>

        <section>
            <h2>Statistik Deskriptif</h2>
            <div class="table-wrap">
                <table>
                    <thead>
                        <tr>
                            <th>Variabel</th>
                            <th>Mean</th>
                            <th>Median</th>
                            <th>Standar Deviasi</th>
                            <th>Minimum</th>
                            <th>Maximum</th>
                        </tr>
                    </thead>
                    <tbody id="statistik"></tbody>
                </table>
            </div>
        </section>
        <section>
            <h2>Regresi Linear aBerganda</h2>

            <p>Variabel target: <b>Penjualan (Y)</b></p>
            <p>Variabel bebas: <b>Hari (X1)</b> dan
                <b>Pengunjung (X2)</b>
            </p>

            <h3>Persamaan Regresi Berganda</h3>
            <p class="equation" id="persamaanMulti"></p>

            <h3>Koefisien Regresi</h3>
            <div class="table-wrap">
                <table>
                    <thead><tr><th>Variabel</th><th>Koefisien</th></tr></thead>
                    <tbody>
                        <tr><td>Konstanta</td><td id="konstantaMulti"></td></tr>
                        <tr><td>Hari (X1)</td><td id="koefHari"></td></tr>
                        <tr><td>Pengunjung (X2)</td><td id="koefPengunjung"></td></tr>
                    </tbody>
                </table>
            </div>

            <h3>Kualitas Model</h3>
            <p>R-kuadrat (R²): <b id="r2Multi"></b></p>

            <h3>Hasil Prediksi</h3>
            <p>Hari ke-11 dengan 13 pengunjung:</p>
            <p>Prediksi penjualan:
                <b id="prediksiMulti"></b>
            </p>

            <h3>Kesimpulan</h3>
            <div class="insight">
                <p>
                    Model regresi linear berganda menggunakan
                    hari dan jumlah pengunjung secara bersamaan
                    untuk memperkirakan penjualan.
                </p>
                <p>
                    Nilai R² menunjukkan proporsi variasi
                    penjualan yang dapat dijelaskan oleh model
                    pada data pengamatan ini.
                </p>
                <p>
                    Hasil prediksi merupakan perkiraan berdasarkan
                    data yang tersedia, bukan jaminan penjualan
                    sebenarnya.
                </p>
            </div>
        </section>

        <section>
            <h2>Statistik Deskriptif 10 Kelompok</h2>
            <p>Masukkan data setiap kelompok, dipisahkan dengan koma. Contoh: 10, 12, 15, 18.</p>
            <div id="inputKelompok"></div>
            <button onclick="hitungKelompok()">Hitung Statistik Kelompok</button>
            <div class="table-wrap table-top">
                <table>
                    <thead>
                        <tr>
                            <th>Kelompok</th>
                            <th>Jumlah</th>
                            <th>Mean</th>
                            <th>Median</th>
                            <th>Standar Deviasi</th>
                            <th>Minimum</th>
                            <th>Maximum</th>
                        </tr>
                    </thead>
                    <tbody id="hasilKelompok"></tbody>
                </table>
            </div>
        </section>

        <section>
            <h2>Grafik Regresi Linear</h2>
            <canvas id="grafik"></canvas>
            <p>Persamaan regresi: <b id="persamaan"></b></p>
            <p>Prediksi penjualan untuk 13 pengunjung: <b id="prediksi"></b></p>
        </section>

        <section>
            <h2>Insight Statistik</h2>
            <div class="insight" id="insight"></div>
        </section>

        <section>
            <h2>Data Pengamatan</h2>
            <div class="table-wrap">
                <table>
                    <thead>
                        <tr>
                            <th>Hari</th>
                            <th>Pengunjung</th>
                            <th>Penjualan</th>
                        </tr>
                    </thead>
                    <tbody id="dataTabel"></tbody>
                </table>
            </div>
        </section>
    </main>

    <footer>Dashboard Statistik • HTML Lokal</footer>

    <script>
        // Data contoh dari kode Python
        const data = [
            [4, 5], [3, 7], [5, 7], [8, 5], [8, 7],
            [9, 6], [7, 6], [10, 9], [11, 11], [12, 11]
        ];

        const fmt = n => Number.isFinite(n) ? n.toFixed(2) : "-";

        function mean(arr) {
            return arr.reduce((s, v) => s + v, 0) / arr.length;
        }
        function median(arr) {
            const a = [...arr].sort((x, y) => x - y);
            const m = Math.floor(a.length / 2);
            return a.length % 2 ? a[m] : (a[m - 1] + a[m]) / 2;
        }
        function std(arr) {
            if (arr.length < 2) return 0;
            const m = mean(arr);
            return Math.sqrt(arr.reduce((s, v) => s + (v - m) ** 2, 0) / (arr.length - 1));
        }
        function corr(x, y) {
            const mx = mean(x), my = mean(y);
            const numerator = x.reduce((s, v, i) => s + (v - mx) * (y[i] - my), 0);
            const denominator = Math.sqrt(
                x.reduce((s, v) => s + (v - mx) ** 2, 0) *
                y.reduce((s, v) => s + (v - my) ** 2, 0)
            );
            return denominator === 0 ? 0 : numerator / denominator;
        }
        function statsRow(name, arr) {
            return `<tr><td>${name}</td><td>${fmt(mean(arr))}</td>
 <td>${fmt(median(arr))}</td><td>${fmt(std(arr))}</td>
 <td>${fmt(Math.min(...arr))}</td><td>${fmt(Math.max(...arr))}</td></tr>`;
        }
        function solveLinearSystem(matrix, values) {
            const rows = matrix.map((row, i) => [...row, values[i]]);
            const size = values.length;
            for (let column = 0; column < size; column++) {
                let pivot = column;
                for (let row = column + 1; row < size; row++) {
                    if (Math.abs(rows[row][column]) > Math.abs(rows[pivot][column])) pivot = row;
                }
                if (Math.abs(rows[pivot][column]) < 1e-12) {
                    throw new Error("Data tidak cukup untuk menghitung regresi linear berganda.");
                }
                [rows[column], rows[pivot]] = [rows[pivot], rows[column]];
                const divisor = rows[column][column];
                for (let cell = column; cell <= size; cell++) rows[column][cell] /= divisor;
                for (let row = 0; row < size; row++) {
                    if (row === column) continue;
                    const factor = rows[row][column];
                    for (let cell = column; cell <= size; cell++) {
                        rows[row][cell] -= factor * rows[column][cell];
                    }
                }
            }
            return rows.map(row => row[size]);
        }

        const x = data.map(d => d[0]);
        const y = data.map(d => d[1]);
        const hari = data.map((_, i) => i + 1);
        const mx = mean(x), my = mean(y);
        const r = corr(x, y);
        const slope = x.reduce((s, v, i) => s + (v - mx) * (y[i] - my), 0) /
            x.reduce((s, v) => s + (v - mx) ** 2, 0);
        const intercept = my - slope * mx;
        const prediction = intercept + slope * 13;
        const predictors = data.map((d, i) => [1, hari[i], d[0]]);
        const matrix = predictors[0].map((_, i) =>
            predictors[0].map((__, j) =>
                predictors.reduce((sum, row) => sum + row[i] * row[j], 0)
            )
        );
        const vector = predictors[0].map((_, i) =>
            predictors.reduce((sum, row, index) => sum + row[i] * y[index], 0)
        );
        const [constantMulti, dayCoefficient, visitorCoefficient] = solveLinearSystem(matrix, vector);
        const multiMean = mean(y);
        const residualSum = predictors.reduce((sum, row, i) =>
            sum + (y[i] - row[0] * constantMulti - row[1] * dayCoefficient - row[2] * visitorCoefficient) ** 2, 0);
        const totalSum = y.reduce((sum, value) => sum + (value - multiMean) ** 2, 0);
        const r2Multi = 1 - residualSum / totalSum;
        const multiPrediction = constantMulti + 11 * dayCoefficient + 13 * visitorCoefficient;
        const equationTerm = value => `${value < 0 ? " - " : " + "}${fmt(Math.abs(value))}`;

        document.getElementById("meanX").textContent = fmt(mx);
        document.getElementById("meanY").textContent = fmt(my);
        document.getElementById("korelasi").textContent = r.toFixed(3);

        document.getElementById("statistik").innerHTML =
            statsRow("Pengunjung", x) + statsRow("Penjualan", y);

        document.getElementById("persamaan").textContent =
            `Y = ${fmt(intercept)} + ${fmt(slope)}X`;
        document.getElementById("prediksi").textContent = fmt(prediction);
        document.getElementById("persamaanMulti").textContent =
            `Y = ${fmt(constantMulti)}${equationTerm(dayCoefficient)} Hari${equationTerm(visitorCoefficient)} Pengunjung`;
        document.getElementById("konstantaMulti").textContent = fmt(constantMulti);
        document.getElementById("koefHari").textContent = fmt(dayCoefficient);
        document.getElementById("koefPengunjung").textContent = fmt(visitorCoefficient);
        document.getElementById("r2Multi").textContent = fmt(r2Multi);
        document.getElementById("prediksiMulti").textContent = fmt(multiPrediction);

        const hubungan = Math.abs(r) < 0.2 ? "sangat lemah" :
            Math.abs(r) < 0.4 ? "lemah" :
                Math.abs(r) < 0.6 ? "sedang" :
                    Math.abs(r) < 0.8 ? "kuat" : "sangat kuat";

        document.getElementById("insight").innerHTML = `
<p>1. Rata-rata pengunjung ${fmt(mx)} dan rata-rata penjualan ${fmt(my)}.</p>
<p>2. Korelasi sebesar ${r.toFixed(3)} menunjukkan hubungan ${hubungan}
${r >= 0 ? "positif" : "negatif"}.</p>
<p>3. Persamaan regresi digunakan untuk memperkirakan penjualan berdasarkan jumlah pengunjung.</p>
<p>4. Prediksi untuk 13 pengunjung adalah ${fmt(prediction)}.</p>
<p>5. Prediksi merupakan perkiraan, bukan jaminan hasil penjualan sebenarnya.</p>`;

        document.getElementById("dataTabel").innerHTML = data.map((d, i) =>
            `<tr><td>${i + 1}</td><td>${d[0]}</td><td>${d[1]}</td></tr>`
        ).join("");

        // Form input untuk 10 kelompok
        const inputArea = document.getElementById("inputKelompok");
        for (let i = 1; i <= 10; i++) {
            inputArea.innerHTML += `
 <p><label>Kelompok ${i}<br>
 <input id="kel${i}" type="text" style="width:100%;padding:10px"
 placeholder="Contoh: 10, 12, 15, 18"></label></p>`;
        }

        function hitungKelompok() {
            let html = "";
            for (let i = 1; i <= 10; i++) {
                const raw = document.getElementById("kel" + i).value.trim();
                const arr = raw.split(/[,\s;]+/).filter(Boolean).map(Number);
                if (!raw || arr.some(v => !Number.isFinite(v))) {
                    html += `<tr><td>Kelompok ${i}</td><td colspan="6">Masukkan angka yang valid</td></tr>`;
                    continue;
                }
                html += `<tr><td>Kelompok ${i}</td><td>${arr.length}</td>
  <td>${fmt(mean(arr))}</td><td>${fmt(median(arr))}</td>
  <td>${fmt(std(arr))}</td><td>${fmt(Math.min(...arr))}</td>
  <td>${fmt(Math.max(...arr))}</td></tr>`;
            }
            document.getElementById("hasilKelompok").innerHTML = html;
        }

        new Chart(document.getElementById("grafik"), {
            type: "scatter",
            data: {
                datasets: [
                    {
                        label: "Data aktual",
                        data: data.map(d => ({ x: d[0], y: d[1] })),
                        backgroundColor: "#2563eb"
                    },
                    {
                        label: "Garis regresi",
                        type: "line",
                        data: [
                            { x: Math.min(...x), y: intercept + slope * Math.min(...x) },
                            { x: Math.max(...x), y: intercept + slope * Math.max(...x) }
                        ],
                        borderColor: "red",
                        borderWidth: 2,
                        pointRadius: 0,
                        fill: false
                    }
                ]
            },
            options: {
                responsive: true,
                scales: {
                    x: { title: { display: true, text: "Pengunjung (X)" } },
                    y: { title: { display: true, text: "Penjualan (Y)" } }
                }
            }
        });
    </script>
</body>

</html>
