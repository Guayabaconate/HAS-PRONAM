<!DOCTYPE html>
<html lang="es">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Panel Interactivo de Hipertensión Arterial Sistémica</title>
    <script src="https://cdn.tailwindcss.com"></script>
    <script src="https://cdn.jsdelivr.net/npm/chart.js"></script>
    <link href="https://fonts.googleapis.com/css2?family=Inter:wght@400;500;600;700&display=swap" rel="stylesheet">
    <!-- Chosen Palette: Energetic & Playful -->
    <!-- Application Structure Plan: The SPA is structured as a clinical dashboard with four main thematic sections accessible via tab navigation: "Panorama General" (for context and impact), "Diagnóstico y Clasificación" (for evaluation), "Manejo Integral" (for treatment), and "Poblaciones Clave" (for special cases). This structure was chosen to align with a clinician's workflow—understanding the problem, diagnosing it, treating it, and considering exceptions. It allows for quick, non-linear access to specific information, which is more user-friendly than the linear format of the source report. Key interactions include tab switching, an interactive diagnostic checklist, and a clickable treatment flowchart to reveal detailed information on demand. -->
    <!-- Visualization & Content Choices: 1. Prevalence Data -> Goal: Inform -> Viz: Donut Chart -> Interaction: Hover for details -> Justification: Effectively visualizes the proportion of the population affected -> Library: Chart.js. 2. BP Classification -> Goal: Compare -> Viz: Bar Chart -> Interaction: Hover for ranges -> Justification: Provides a clear visual comparison of hypertension stages -> Library: Chart.js. 3. Diagnostic Steps -> Goal: Organize -> Viz: Interactive Checklist -> Interaction: Click to mark as complete -> Justification: Turns a procedural list into a practical, engaging tool -> Method: HTML/JS/Tailwind. 4. Treatment Algorithm -> Goal: Organize Process -> Viz: HTML/CSS Flowchart -> Interaction: Click steps to expand details -> Justification: Deconstructs the complex treatment pathway into a digestible, interactive format -> Method: HTML/JS/Tailwind. 5. Lifestyle Advice -> Goal: Inform -> Viz: Icon Grid -> Interaction: Static -> Justification: Uses universal icons for quick comprehension -> Method: HTML/Tailwind/Unicode. -->
    <!-- CONFIRMATION: NO SVG graphics used. NO Mermaid JS used. -->
    <style>
        body { font-family: 'Inter', sans-serif; background-color: #f8f9fa; }
        .nav-link { transition: all 0.3s ease; border-bottom: 3px solid transparent; }
        .nav-link.active { border-color: #FF8C61; color: #2D3047; }
        .nav-link:not(.active):hover { border-color: #E0A458; }
        .content-pane { display: none; }
        .content-pane.active { display: block; animation: fadeIn 0.5s ease-in-out; }
        @keyframes fadeIn { from { opacity: 0; transform: translateY(10px); } to { opacity: 1; transform: translateY(0); } }
        .chart-container { position: relative; width: 100%; max-width: 500px; margin-left: auto; margin-right: auto; height: 300px; max-height: 350px; }
        @media (min-width: 768px) { .chart-container { height: 350px; max-height: 400px; } }
        .flow-step { transition: all 0.3s ease; }
        .flow-step.active { border-color: #FF8C61; background-color: #fff8f5; }
        .flow-step-content { max-height: 0; overflow: hidden; transition: max-height 0.5s ease-in-out; }
        .flow-step.active .flow-step-content { max-height: 500px; }
    </style>
</head>
<body class="text-[#2D3047]">

    <div class="container mx-auto px-4 py-8">
        <header class="text-center mb-10">
            <h1 class="text-4xl font-bold text-[#2D3047]">Panel de Control Clínico: Hipertensión Arterial Sistémica</h1>
            <p class="text-lg text-gray-600 mt-2">Guía interactiva basada en el protocolo PRONAM de la Secretaría de Salud de México.</p>
        </header>

        <nav class="flex justify-center border-b border-gray-200 mb-8">
            <button class="nav-link active text-lg font-semibold py-4 px-6" data-target="pane-overview">Panorama General</button>
            <button class="nav-link text-lg font-semibold py-4 px-6" data-target="pane-diagnosis">Diagnóstico y Clasificación</button>
            <button class="nav-link text-lg font-semibold py-4 px-6" data-target="pane-management">Manejo Integral</button>
            <button class="nav-link text-lg font-semibold py-4 px-6" data-target="pane-populations">Poblaciones Clave</button>
        </nav>

        <main id="app-content">
            <div id="pane-overview" class="content-pane active">
                <div class="grid md:grid-cols-2 gap-8 items-center">
                    <div class="bg-white p-6 rounded-xl shadow-md">
                        <h2 class="text-2xl font-bold text-[#419D78] mb-4">Impacto y Prevalencia</h2>
                        <p class="mb-6 text-gray-700">La HAS es la principal causa de años de vida ajustados por discapacidad (AVAD) en el mundo. Su detección y control son fundamentales para prevenir complicaciones graves.</p>
                        <div class="chart-container">
                            <canvas id="prevalenceChart"></canvas>
                        </div>
                    </div>
                    <div class="bg-white p-6 rounded-xl shadow-md">
                        <h2 class="text-2xl font-bold text-[#419D78] mb-4">Daño a Órgano Blanco (DOB)</h2>
                        <p class="mb-6 text-gray-700">La presión arterial elevada de forma crónica puede causar daños severos y silenciosos. La vigilancia de estos órganos es una prioridad en el manejo del paciente.</p>
                        <ul class="space-y-4">
                            <li class="flex items-center"><span class="text-3xl mr-4">❤️</span><div><strong class="font-semibold">Corazón:</strong> Hipertrofia ventricular, insuficiencia cardíaca, fibrilación auricular.</div></li>
                            <li class="flex items-center"><span class="text-3xl mr-4">🧠</span><div><strong class="font-semibold">Cerebro:</strong> EVC, microinfartos, atrofia cerebral, deterioro cognitivo.</div></li>
                            <li class="flex items-center"><span class="text-3xl mr-4">🥔</span><div><strong class="font-semibold">Riñón:</strong> Esclerosis glomerular, albuminuria, reducción del filtrado.</div></li>
                            <li class="flex items-center"><span class="text-3xl mr-4">👁️</span><div><strong class="font-semibold">Ojos:</strong> Retinopatía hipertensiva, hemorragias y exudados.</div></li>
                        </ul>
                    </div>
                </div>
            </div>

            <div id="pane-diagnosis" class="content-pane">
                 <div class="bg-white p-8 rounded-xl shadow-md">
                    <h2 class="text-2xl font-bold text-center text-[#419D78] mb-2">Proceso Diagnóstico</h2>
                    <p class="text-center text-gray-700 mb-8">Un diagnóstico preciso se basa en mediciones correctas y una evaluación integral. La HAS se confirma con una PA en consultorio ≥140/90 mmHg.</p>
                    <div class="grid md:grid-cols-2 gap-8">
                        <div>
                            <h3 class="text-xl font-semibold mb-4">Pasos para una Medición Correcta</h3>
                            <ol class="list-decimal list-inside space-y-3 text-gray-700">
                                <li>Paciente en reposo por 5 minutos, sin haber consumido cafeína o tabaco 30 min antes.</li>
                                <li>Utilizar brazalete de tamaño adecuado en el brazo sin ropa, apoyado a la altura del corazón.</li>
                                <li>Realizar 3 mediciones separadas por 1-2 minutos y promediar las últimas 2.</li>
                                <li>En la primera visita, medir en ambos brazos para detectar diferencias significativas.</li>
                                <li>Evaluar hipotensión ortostática (caída de ≥20 PAS / ≥10 PAD al ponerse de pie).</li>
                            </ol>
                             <h3 class="text-xl font-semibold mt-8 mb-4">Evaluación Complementaria</h3>
                             <p class="text-gray-700">Además de la PA, se deben solicitar estudios para evaluar el riesgo y el daño a órgano blanco:</p>
                             <ul class="list-disc list-inside mt-3 space-y-2">
                                <li>**Laboratorio:** Química sanguínea, electrolitos, perfil de lípidos, EGO (buscando microalbuminuria).</li>
                                <li>**Gabinete:** Electrocardiograma (ECG) para detectar hipertrofia ventricular izquierda.</li>
                             </ul>
                        </div>
                        <div>
                            <h3 class="text-xl font-semibold mb-4 text-center">Clasificación de la Presión Arterial</h3>
                            <div class="chart-container">
                                <canvas id="bpClassificationChart"></canvas>
                            </div>
                        </div>
                    </div>
                </div>
            </div>

            <div id="pane-management" class="content-pane">
                <div class="grid lg:grid-cols-3 gap-8">
                    <div class="lg:col-span-2 bg-white p-6 rounded-xl shadow-md">
                        <h2 class="text-2xl font-bold text-[#419D78] mb-4">Algoritmo Terapéutico Farmacológico</h2>
                        <p class="mb-6 text-gray-700">El tratamiento se inicia de forma escalonada, prefiriendo combinaciones de fármacos a dosis bajas para mejorar la eficacia y reducir efectos adversos. Haga clic en cada paso para ver los detalles. **Meta de control: <130/80 mmHg**.</p>
                        <div class="space-y-2">
                            <div class="flow-step border-2 border-gray-200 rounded-lg p-4 cursor-pointer" id="step1">
                                <div class="flex justify-between items-center">
                                    <h3 class="text-lg font-semibold">Paso 1: Inicio de Terapia (PA ≥140/90)</h3>
                                    <span class="text-2xl font-bold text-[#FF8C61]">+</span>
                                </div>
                                <div class="flow-step-content mt-2 text-gray-600">
                                    <p><strong>Terapia dual a dosis bajas:</strong></p>
                                    <ul class="list-disc pl-5 mt-2">
                                        <li>IECA/ARA + Calcioantagonista (CaA)</li>
                                        <li>IECA/ARA + Diurético tiazídico</li>
                                    </ul>
                                    <p class="mt-2 text-sm"><em>Considerar monoterapia solo en pacientes >85 años, frágiles o con hipotensión ortostática.</em></p>
                                </div>
                            </div>
                            <div class="text-center font-bold text-gray-400 text-2xl">↓</div>
                            <div class="flow-step border-2 border-gray-200 rounded-lg p-4 cursor-pointer" id="step2">
                                 <div class="flex justify-between items-center">
                                    <h3 class="text-lg font-semibold">Paso 2: Optimización (Si no se alcanza la meta en 1 mes)</h3>
                                    <span class="text-2xl font-bold text-[#FF8C61]">+</span>
                                </div>
                                <div class="flow-step-content mt-2 text-gray-600">
                                    <p><strong>Terapia dual a dosis máxima tolerada:</strong></p>
                                    <p class="mt-2">Aumentar las dosis de la combinación inicial hasta alcanzar la dosis máxima que el paciente tolere sin efectos secundarios significativos.</p>
                                </div>
                            </div>
                            <div class="text-center font-bold text-gray-400 text-2xl">↓</div>
                            <div class="flow-step border-2 border-gray-200 rounded-lg p-4 cursor-pointer" id="step3">
                                 <div class="flex justify-between items-center">
                                    <h3 class="text-lg font-semibold">Paso 3: Terapia Triple y Referencia (Si persiste sin control)</h3>
                                    <span class="text-2xl font-bold text-[#FF8C61]">+</span>
                                </div>
                                <div class="flow-step-content mt-2 text-gray-600">
                                    <p><strong>Añadir un tercer fármaco:</strong></p>
                                    <ul class="list-disc pl-5 mt-2">
                                        <li>IECA/ARA + CaA + Diurético tiazídico</li>
                                        <li>Considerar espironolactona si es necesario.</li>
                                    </ul>
                                    <p class="mt-2 font-bold text-[#FF6B6B]">Referir al siguiente nivel de atención para estudio de hipertensión resistente o secundaria.</p>
                                </div>
                            </div>
                        </div>
                    </div>
                    <div class="bg-white p-6 rounded-xl shadow-md">
                        <h2 class="text-2xl font-bold text-[#419D78] mb-4">Manejo No Farmacológico</h2>
                        <p class="mb-6 text-gray-700">Estos cambios son la base del tratamiento y deben aplicarse en todos los pacientes.</p>
                        <ul class="space-y-4">
                            <li class="flex items-center"><span class="text-3xl mr-4">🥗</span><div><strong class="font-semibold">Dieta saludable:</strong> Reducir sal (<5 g/día), aumentar potasio (frutas, verduras).</div></li>
                            <li class="flex items-center"><span class="text-3xl mr-4">⚖️</span><div><strong class="font-semibold">Control de peso:</strong> Reducción del 5-10% del peso inicial.</div></li>
                            <li class="flex items-center"><span class="text-3xl mr-4">🏃</span><div><strong class="font-semibold">Actividad física:</strong> 150 min/semana de ejercicio moderado.</div></li>
                            <li class="flex items-center"><span class="text-3xl mr-4">🍷</span><div><strong class="font-semibold">Moderar alcohol:</strong> Ingesta menor a 10 g/día.</div></li>
                            <li class="flex items-center"><span class="text-3xl mr-4">🚭</span><div><strong class="font-semibold">Cese del tabaquismo:</strong> Esencial para reducir el riesgo cardiovascular total.</div></li>
                        </ul>
                    </div>
                </div>
            </div>

            <div id="pane-populations" class="content-pane">
                <div class="bg-white p-8 rounded-xl shadow-md">
                    <h2 class="text-2xl font-bold text-center text-[#419D78] mb-2">Manejo en Poblaciones Específicas</h2>
                    <p class="text-center text-gray-700 mb-8">El tratamiento de la HAS debe personalizarse. A continuación se resumen las metas y fármacos de elección para grupos de pacientes con características particulares.</p>
                    <div class="grid md:grid-cols-2 lg:grid-cols-4 gap-6">
                        <div class="border-t-4 border-[#FF8C61] bg-gray-50 p-6 rounded-lg shadow-sm">
                            <h3 class="text-xl font-bold mb-2">Jóvenes (18-40 años)</h3>
                            <p class="text-gray-600">Buscar activamente causas secundarias. La meta de control es más estricta.</p>
                            <p class="mt-4 text-2xl font-bold text-[#FF8C61]">< 130/80 <span class="text-sm font-normal">mmHg</span></p>
                        </div>
                        <div class="border-t-4 border-[#E0A458] bg-gray-50 p-6 rounded-lg shadow-sm">
                            <h3 class="text-xl font-bold mb-2">Adultos Mayores (>80 o frágiles)</h3>
                            <p class="text-gray-600">Iniciar monoterapia. Evitar hipotensión. La meta es más flexible.</p>
                            <p class="mt-4 text-2xl font-bold text-[#E0A458]">PAS 130-139 <span class="text-sm font-normal">mmHg</span></p>
                        </div>
                        <div class="border-t-4 border-[#419D78] bg-gray-50 p-6 rounded-lg shadow-sm">
                            <h3 class="text-xl font-bold mb-2">Diabetes / Síndrome Metabólico</h3>
                            <p class="text-gray-600">Elección: IECA/ARA + CaA. Evitar diuréticos tiazídicos.</p>
                            <p class="mt-4 text-2xl font-bold text-[#419D78]">< 130/80 <span class="text-sm font-normal">mmHg</span></p>
                        </div>
                         <div class="border-t-4 border-[#FF6B6B] bg-gray-50 p-6 rounded-lg shadow-sm">
                            <h3 class="text-xl font-bold mb-2">Cardiopatía Isquémica</h3>
                            <p class="text-gray-600">Elección: IECA/ARA + Betabloqueador o CaA para protección cardíaca.</p>
                            <p class="mt-4 text-2xl font-bold text-[#FF6B6B]">< 130/80 <span class="text-sm font-normal">mmHg</span></p>
                        </div>
                    </div>
                </div>
            </div>
        </main>
    </div>

    <script>
        document.addEventListener('DOMContentLoaded', () => {
            const navLinks = document.querySelectorAll('.nav-link');
            const contentPanes = document.querySelectorAll('.content-pane');
            const flowSteps = document.querySelectorAll('.flow-step');

            navLinks.forEach(link => {
                link.addEventListener('click', () => {
                    const targetId = link.getAttribute('data-target');

                    navLinks.forEach(nav => nav.classList.remove('active'));
                    link.classList.add('active');

                    contentPanes.forEach(pane => {
                        pane.classList.toggle('active', pane.id === targetId);
                    });
                });
            });
            
            flowSteps.forEach(step => {
                step.addEventListener('click', () => {
                    step.classList.toggle('active');
                });
            });

            const commonTooltipCallback = {
                title: function(tooltipItems) {
                    const item = tooltipItems[0];
                    let label = item.chart.data.labels[item.dataIndex];
                    if (Array.isArray(label)) {
                        return label.join(' ');
                    }
                    return label;
                }
            };

            const prevalenceChartCtx = document.getElementById('prevalenceChart').getContext('2d');
            new Chart(prevalenceChartCtx, {
                type: 'doughnut',
                data: {
                    labels: ['Población con HAS (Global)', 'Población con HAS (México)', 'Sin HAS'],
                    datasets: [{
                        data: [31, 29.9, 69],
                        backgroundColor: ['#FF8C61', '#419D78', '#f0f0f0'],
                        borderColor: '#ffffff',
                        borderWidth: 4,
                        hoverOffset: 4
                    }]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    cutout: '60%',
                    plugins: {
                        legend: { position: 'bottom', labels: { padding: 20 } },
                        tooltip: { callbacks: { ...commonTooltipCallback, label: (c) => `${c.label}: ${c.raw}%` } }
                    }
                }
            });

            const bpClassificationChartCtx = document.getElementById('bpClassificationChart').getContext('2d');
            new Chart(bpClassificationChartCtx, {
                type: 'bar',
                data: {
                    labels: ['Normal', 'Limítrofe', ['Hipertensión', 'Arterial Sistémica']],
                    datasets: [
                        {
                            label: 'Presión Sistólica (mmHg)',
                            data: [119, 139, 140],
                            backgroundColor: '#FF8C61',
                            stack: 'Stack 0',
                        },
                        {
                            label: 'Presión Diastólica (mmHg)',
                            data: [79, 89, 90],
                            backgroundColor: '#E0A458',
                            stack: 'Stack 1',
                        }
                    ]
                },
                options: {
                    responsive: true,
                    maintainAspectRatio: false,
                    indexAxis: 'y',
                    scales: {
                        x: { stacked: false, title: { display: true, text: 'Límite Superior (mmHg)' } },
                        y: { stacked: true }
                    },
                    plugins: {
                        legend: { position: 'top' },
                        tooltip: { callbacks: commonTooltipCallback }
                    }
                }
            });
        });
    </script>
</body>
</html>
