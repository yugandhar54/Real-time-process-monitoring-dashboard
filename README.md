# Real-time-process-monitoring-dashboard
It is a project based on real-time process monitoring dashboard for operating system
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Real-Time Process Monitor</title>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <style>
        * {
            box-sizing: border-box;
            margin: 0;
            padding: 0;
            font-family: 'Arial', sans-serif;
        }

        body {
            background-color: #f0f2f5;
            padding: 20px;
        }

        .dashboard {
            max-width: 1200px;
            margin: 0 auto;
        }

        .header {
            text-align: center;
            padding: 20px;
            background-color: #2c3e50;
            color: white;
            border-radius: 8px;
            margin-bottom: 20px;
        }

        .stats-container {
            display: grid;
            grid-template-columns: repeat(auto-fit, minmax(250px, 1fr));
            gap: 20px;
            margin-bottom: 20px;
        }

        .stat-box {
            background: white;
            padding: 20px;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }

        .chart-container {
            background: white;
            padding: 20px;
            border-radius: 8px;
            margin-bottom: 20px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
        }

        .process-list {
            background: white;
            border-radius: 8px;
            box-shadow: 0 2px 4px rgba(0,0,0,0.1);
            overflow-x: auto;
        }

        table {
            width: 100%;
            border-collapse: collapse;
        }

        th, td {
            padding: 12px 15px;
            text-align: left;
            border-bottom: 1px solid #ddd;
        }

        th {
            background-color: #2c3e50;
            color: white;
        }

        tr:hover {
            background-color: #f5f5f5;
        }

        .progress-bar {
            width: 100%;
            height: 10px;
            background-color: #eee;
            border-radius: 5px;
            overflow: hidden;
        }

        .progress {
            height: 100%;
            background-color: #2980b9;
            transition: width 0.3s ease;
        }
    </style>
</head>
<body>
    <div class="dashboard">
        <div class="header">
            <h1>Real-Time Process Monitor</h1>
            <p>Updated every 2 seconds</p>
        </div>

        <div class="stats-container">
            <div class="stat-box">
                <h3>CPU Usage</h3>
                <div class="progress-bar">
                    <div class="progress" id="cpu-progress" style="width: 50%"></div>
                </div>
                <p id="cpu-text" class="stat-value">50%</p>
            </div>
            
            <div class="stat-box">
                <h3>Memory Usage</h3>
                <div class="progress-bar">
                    <div class="progress" id="memory-progress" style="width: 65%"></div>
                </div>
                <p id="memory-text" class="stat-value">65%</p>
            </div>
        </div>

        <div class="chart-container">
            <canvas id="performanceChart"></canvas>
        </div>

        <div class="process-list">
            <h3 style="padding: 15px">Running Processes</h3>
            <div style="padding: 0 15px">
                <input type="text" id="search" placeholder="Search processes..." style="
                    width: 100%;
                    padding: 8px;
                    margin-bottom: 10px;
                    border: 1px solid #ddd;
                    border-radius: 4px;
                ">
            </div>
            <table>
                <thead>
                    <tr>
                        <th>PID</th>
                        <th>Process Name</th>
                        <th>CPU %</th>
                        <th>Memory</th>
                        <th>Status</th>
                    </tr>
                </thead>
                <tbody id="process-table">
                    <!-- Process data will be inserted here -->
                </tbody>
            </table>
        </div>
    </div>

    <script>
        const ctx = document.getElementById('performanceChart').getContext('2d');
        const chart = new Chart(ctx, {
            type: 'line',
            data: {
                labels: [],
                datasets: [{
                    label: 'CPU Usage (%)',
                    data: [],
                    borderColor: '#2980b9',
                    tension: 0.1
                }, {
                    label: 'Memory Usage (%)',
                    data: [],
                    borderColor: '#27ae60',
                    tension: 0.1
                }]
            },
            options: {
                responsive: true,
                maintainAspectRatio: false,
                scales: {
                    y: {
                        beginAtZero: true,
                        max: 100
                    }
                }
            }
        });

        function generateProcessData() {
            const processes = [];
            const processNames = ['System', 'chrome', 'node', 'python', 'mysqld', 'bash'];
            
            for (let i = 0; i < 10; i++) {
                processes.push({
                    pid: Math.floor(Math.random() * 10000),
                    name: processNames[Math.floor(Math.random() * processNames.length)],
                    cpu: (Math.random() * 30).toFixed(1),
                    memory: (Math.random() * 500).toFixed(1) + ' MB',
                    status: Math.random() > 0.9 ? 'Warning' : 'Normal'
                });
            }
            return processes;
        }

        function updateDashboard() {
            const cpuUsage = Math.random() * 100;
            const memoryUsage = 30 + Math.random() * 70;

            document.getElementById('cpu-progress').style.width = cpuUsage + '%';
            document.getElementById('cpu-text').textContent = cpuUsage.toFixed(1) + '%';
            
            document.getElementById('memory-progress').style.width = memoryUsage + '%';
            document.getElementById('memory-text').textContent = memoryUsage.toFixed(1) + '%';

            const now = new Date().toLocaleTimeString();
            if (chart.data.labels.length > 15) chart.data.labels.shift();
            chart.data.labels.push(now);

            if (chart.data.datasets[0].data.length > 15) {
                chart.data.datasets[0].data.shift();
                chart.data.datasets[1].data.shift();
            }

            chart.data.datasets[0].data.push(cpuUsage);
            chart.data.datasets[1].data.push(memoryUsage);
            
            chart.update();

            const processes = generateProcessData();
            const tbody = document.getElementById('process-table');
            tbody.innerHTML = '';

            processes.forEach(proc => {
                const row = `<tr>
                    <td>${proc.pid}</td>
                    <td>${proc.name}</td>
                    <td>${proc.cpu}%</td>
                    <td>${proc.memory}</td>
                    <td style="color: ${proc.status === 'Warning' ? '#e74c3c' : '#27ae60'}">${proc.status}</td>
                </tr>`;
                tbody.innerHTML += row;
            });
        }

        document.getElementById('search').addEventListener('input', function(e) {
            const searchTerm = e.target.value.toLowerCase();
            document.querySelectorAll('#process-table tr').forEach(row => {
                row.style.display = row.children[1].textContent.toLowerCase().includes(searchTerm) ? '' : 'none';
            });
        });

        updateDashboard();
        setInterval(updateDashboard, 2000);
    </script>
</body>
</html>
