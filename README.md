<!DOCTYPE html>
<html lang="ms">
<head>
  <meta charset="UTF-8">
  <meta name="viewport" content="width=device-width, initial-scale=1.0">
  <title>Pelaporan Kokurikulum SK Morni Pok - Versi Premium</title>

  <!-- Tailwind CSS -->
  <script src="https://cdn.tailwindcss.com"></script>

  <!-- html2pdf -->
  <script src="https://cdnjs.cloudflare.com/ajax/libs/html2pdf.js/0.10.1/html2pdf.bundle.min.js"></script>

  <!-- Dropbox Saver -->
  <script src="https://www.dropbox.com/static/api/2/dropins.js" id="dropboxjs" data-app-key="YOUR_DROPBOX_APP_KEY"></script>

  <style>
    .report-page { width: 210mm; min-height: 297mm; }
    body::-webkit-scrollbar { display: none; }
  </style>
</head>
<body class="bg-gray-100 font-sans">

<div class="max-w-6xl mx-auto bg-white shadow-2xl rounded-2xl p-8 mt-6">
  <h1 class="text-3xl font-bold text-center mb-8 text-blue-800">PELAPORAN AKTIVITI KOKURIKULUM SK MORNI POK</h1>

  <div class="grid md:grid-cols-2 gap-6">
    <input id="tarikh" type="date" class="border p-3 rounded-lg" placeholder="Tarikh" />
    <input id="masa" type="text" class="border p-3 rounded-lg" placeholder="Masa" />
    <input id="bil" type="number" class="border p-3 rounded-lg" placeholder="Bil Kehadiran" />
    <input id="tempat" type="text" class="border p-3 rounded-lg" placeholder="Tempat" />
    <input id="guru" type="text" class="border p-3 rounded-lg md:col-span-2" placeholder="Nama Guru Penyelaras" />
  </div>

  <h2 class="font-semibold mt-8 mb-2">Unit</h2>
  <select id="unit" class="border p-3 rounded-lg w-full">
    <option value="">Pilih Unit</option>
    <option>Pengakap</option>
    <option>TKRS</option>
    <option>Puteri Islam</option>
    <option>Kelab Pencegahan Jenayah</option>
    <option>Kelab Kelestarian</option>
    <option>Kelab Simpanan Pendidikan</option>
    <option>Kelab STEM</option>
    <option>Kelab Doktor Muda</option>
    <option>Sukan Hoki</option>
    <option>Sukan Petanque</option>
    <option>Sukan Olahraga</option>
    <option>Sukan Bola Baling</option>
  </select>

  <div class="mt-6">
    <input id="tajuk" type="text" class="border p-3 rounded-lg w-full mb-3" placeholder="Tajuk Aktiviti" />
    <textarea id="objektif" class="border p-3 rounded-lg w-full mb-3" rows="3" placeholder="Objektif Aktiviti"></textarea>
    <textarea id="laporan" class="border p-3 rounded-lg w-full mb-3" rows="6" placeholder="Laporan Aktiviti"></textarea>
  </div>

  <h2 class="font-semibold mt-6">Gambar Aktiviti (min 5MB, max 5 gambar)</h2>
  <input type="file" id="images" multiple accept="image/*" class="border p-2 rounded-lg w-full" />

  <div class="flex flex-wrap gap-4 mt-6">
    <button onclick="generatePDF()" class="bg-blue-700 text-white px-5 py-3 rounded-lg shadow hover:bg-blue-800 transition">Jana PDF</button>
    <button onclick="saveToDropbox()" class="bg-indigo-700 text-white px-5 py-3 rounded-lg shadow hover:bg-indigo-800 transition">Simpan ke Dropbox</button>
  </div>
</div>

<!-- Template PDF -->
<div id="report" class="hidden">
  <div class="report-page p-6 text-sm">
    <h2 class="text-2xl font-bold text-center mb-4 text-blue-800">LAPORAN AKTIVITI SK MORNI POK</h2>
    <p><b>Tarikh:</b> <span id="r_tarikh"></span> | <b>Masa:</b> <span id="r_masa"></span></p>
    <p><b>Tempat:</b> <span id="r_tempat"></span></p>
    <p><b>Bil Kehadiran:</b> <span id="r_bil"></span></p>
    <p><b>Guru Penyelaras:</b> <span id="r_guru"></span></p>
    <p><b>Unit:</b> <span id="r_unit"></span></p>
    <hr class="my-2" />
    <p><b>Tajuk Aktiviti</b><br><span id="r_tajuk"></span></p>
    <p><b>Objektif Aktiviti</b><br><span id="r_objektif"></span></p>
    <p><b>Laporan</b><br><span id="r_laporan"></span></p>
    <div id="r_images" class="grid grid-cols-3 gap-2 mt-4"></div>
  </div>
</div>

<script>
let pdfBlob;

function generatePDF() {
  document.getElementById('r_tarikh').innerText = tarikh.value;
  document.getElementById('r_masa').innerText = masa.value;
  document.getElementById('r_tempat').innerText = tempat.value;
  document.getElementById('r_bil').innerText = bil.value;
  document.getElementById('r_guru').innerText = guru.value;
  document.getElementById('r_unit').innerText = unit.value;
  document.getElementById('r_tajuk').innerText = tajuk.value;
  document.getElementById('r_objektif').innerText = objektif.value;
  document.getElementById('r_laporan').innerText = laporan.value;

  const imgDiv = document.getElementById('r_images');
  imgDiv.innerHTML = '';

  const files = [...images.files].slice(0,5);
  files.forEach(file => {
    if(file.size >= 5242880){ // ≥5MB
      const img = document.createElement('img');
      img.src = URL.createObjectURL(file);
      img.className = 'w-full h-32 object-cover';
      imgDiv.appendChild(img);
    } else {
      alert('Setiap gambar mesti sekurang-kurangnya 5MB.');
    }
  });

  const opt = { margin: 5, filename: 'Laporan_SK_Morni_Pok.pdf', image: { type: 'jpeg', quality: 0.98 }, html2canvas: { scale: 2 }, jsPDF: { unit: 'mm', format: 'a4' } };

  html2pdf().from(document.getElementById('report')).set(opt).outputPdf('blob').then(blob => {
    pdfBlob = blob;
    html2pdf().from(document.getElementById('report')).set(opt).save();
  });
}

function saveToDropbox() {
  if (!pdfBlob) { alert('Sila jana PDF dahulu'); return; }

  const file = new File([pdfBlob], 'Laporan_SK_Morni_Pok.pdf', { type: 'application/pdf' });

  Dropbox.save({
    files: [{ url: URL.createObjectURL(file), filename: file.name }],
    success: function () {
      alert('PDF berjaya disimpan ke Dropbox dan boleh diakses oleh PK Kokurikulum.');
    }
  });
}
</script>

</body>
</html>
