# Malla-medicina-Ucsc<!DOCTYPE html>
<html lang="es">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Malla Medicina Interactiva</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      background-color: #fffef9;
      color: #333;
      margin: 20px;
    }
    h1 {
      text-align: center;
      color: #f4b400;
    }
    .semestre {
      margin-bottom: 30px;
    }
    .semestre h2 {
      background-color: #f4b400;
      color: white;
      padding: 10px;
      border-radius: 8px;
    }
    .ramo {
      border: 2px solid #ccc;
      border-radius: 10px;
      padding: 10px;
      margin: 10px;
      background-color: #e0f7fa;
      width: 300px;
      cursor: pointer;
    }
    .ramo.aprobado {
      background-color: #c8e6c9;
      border-color: #388e3c;
    }
    .ramos-container {
      display: flex;
      flex-wrap: wrap;
      gap: 15px;
    }
    .desbloqueado {
      opacity: 1;
      cursor: pointer;
    }
    .bloqueado {
      opacity: 0.4;
      cursor: not-allowed;
    }
  </style>
</head>
<body>
  <h1>Malla Curricular Medicina</h1>

  <div id="malla"></div>

  <script>
    const dataMalla = [
      [
        { nombre: "Morfología Humana", codigo: "MD108C" },
        { nombre: "Biología Celular y Molecular", codigo: "MD101C" },
        { nombre: "Integración I", codigo: "MD103C" },
        { nombre: "Filosofía", codigo: "TEO040C" },
        { nombre: "Teología", codigo: "TEO041C" }
      ],
      [
        { nombre: "Bioestadísticas", codigo: "MD108C4" },
        { nombre: "Biomatemáticas", codigo: "MD108C7" },
        { nombre: "Integración II", codigo: "MD108C3" },
        { nombre: "Antropología Médica", codigo: "TEO040C4" },
        { nombre: "Lectura Textos Médicos Inglés", codigo: "MD108C2" },
        { nombre: "Introducción a la Comunicación", codigo: "EDO034C" },
        { nombre: "Introducción a la Medicina", codigo: "MD108C6" },
        { nombre: "Historia Medicina", codigo: "MD108C1" }
      ],
      [
        { nombre: "Fisiología", codigo: "MD109C", requisitos: ["MD108C", "MD108C6", "MD108C3", "MD108C4"] },
        { nombre: "Epidemiología", codigo: "MD109C2" },
        { nombre: "Neurociencia", codigo: "MD109C6" },
        { nombre: "Metodología de la Investigación", codigo: "MD109C7" }
      ],
      [
        { nombre: "Acción Primaria en Salud", codigo: "MD109C2" },
        { nombre: "Bioquímica", codigo: "MD105C" },
        { nombre: "Farmacología", codigo: "MD109C5" }
      ],
      [
        { nombre: "Fisiopatología", codigo: "MD109C4" },
        { nombre: "Fundamentos Acción Médica", codigo: "MD102C" },
        { nombre: "Patología General", codigo: "MD109C8" },
        { nombre: "Salud Pública y Gestión", codigo: "MD109C7" }
      ],
      [
        { nombre: "Clínica Médica I", codigo: "MD109C6" },
        { nombre: "Fundamentos Investigación", codigo: "MD109C9" },
        { nombre: "Ciencias Básicas Aplicadas", codigo: "MD110C" },
        { nombre: "Geriatría y Cuidados Especiales", codigo: "MD110C5" }
      ],
      [
        { nombre: "Clínica Médica II", codigo: "MD109C3" },
        { nombre: "Investigación Clínica", codigo: "MD110C3" },
        { nombre: "Integración IV", codigo: "MD110C4" },
        { nombre: "Psiquiatría y Psicopatología", codigo: "MD111C6" }
      ],
      [
        { nombre: "Medicina Integrada II", codigo: "MD110C" },
        { nombre: "Integración V", codigo: "MD110C1" },
        { nombre: "Especialidades Médico-Quirúrgicas", codigo: "MD111C8" }
      ],
      [
        { nombre: "Obstetricia y Ginecología", codigo: "MD111C5" },
        { nombre: "Pediatría y Cirugía Infantil", codigo: "MD111C3" },
        { nombre: "Neurología y Neurocirugía", codigo: "MD111C6" }
      ],
      [
        { nombre: "Medicina Legal", codigo: "MD112C0" },
        { nombre: "Integración de los Saberes", codigo: "MD111C7" },
        { nombre: "Optativo de Profundización", codigo: "OPT200" }
      ],
      [
        { nombre: "Internado Médico", codigo: "MD112C" }
      ],
      [
        { nombre: "Internado Pediátrico", codigo: "MD1123C" },
        { nombre: "Internado Ginecológico", codigo: "MD1124C" }
      ],
      [
        { nombre: "Internado Salud Familiar", codigo: "MD1125C" }
      ],
      [
        { nombre: "Internado Cirugía", codigo: "MD1121C" }
      ]
    ];

    const mallaContainer = document.getElementById('malla');

    function renderMalla() {
      mallaContainer.innerHTML = '';

      dataMalla.forEach((semestre, index) => {
        const semestreDiv = document.createElement('div');
        semestreDiv.classList.add('semestre');

        const h2 = document.createElement('h2');
        h2.textContent = `${index + 1}° Semestre`;
        semestreDiv.appendChild(h2);

        const container = document.createElement('div');
        container.classList.add('ramos-container');

        semestre.forEach(ramo => {
          const div = document.createElement('div');
          div.classList.add('ramo');
          div.dataset.codigo = ramo.codigo;
          if (ramo.requisitos) div.dataset.requisitos = ramo.requisitos.join(',');
          div.innerHTML = `${ramo.nombre}<br><small>(${ramo.codigo})</small>`;
          container.appendChild(div);
        });

        semestreDiv.appendChild(container);
        mallaContainer.appendChild(semestreDiv);
      });
    }

    function checkDisponibilidad() {
      const ramos = document.querySelectorAll('.ramo');

      ramos.forEach(ramo => {
        const requisitos = ramo.dataset.requisitos?.split(',') || [];
        const aprobados = JSON.parse(localStorage.getItem('aprobados') || '[]');

        const puedeTomarse = requisitos.every(r => aprobados.includes(r));

        if (requisitos.length === 0 || puedeTomarse) {
          ramo.classList.remove('bloqueado');
          ramo.classList.add('desbloqueado');
        } else {
          ramo.classList.add('bloqueado');
          ramo.classList.remove('desbloqueado');
        }
      });
    }

    function setupClicks() {
      const ramos = document.querySelectorAll('.ramo');

      ramos.forEach(ramo => {
        ramo.addEventListener('click', () => {
          if (ramo.classList.contains('bloqueado')) return;

          ramo.classList.toggle('aprobado');

          const codigo = ramo.dataset.codigo;
          let aprobados = JSON.parse(localStorage.getItem('aprobados') || '[]');

          if (ramo.classList.contains('aprobado')) {
            aprobados.push(codigo);
          } else {
            aprobados = aprobados.filter(r => r !== codigo);
          }

          localStorage.setItem('aprobados', JSON.stringify(aprobados));
          checkDisponibilidad();
        });
      });
    }

    renderMalla();
    setupClicks();
    checkDisponibilidad();
  </script>
</body>
</html>
