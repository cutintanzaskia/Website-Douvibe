<!DOCTYPE html>
<html lang="id">
<head>
  <meta charset="UTF-8">
  <title>Platform Komunikasi Darurat</title>
  <style>
    body {
      font-family: Arial, sans-serif;
      padding: 20px;
      background-image: url('https://i.ibb.co/6rL3mJD/emergency-bg.jpg');
      background-size: cover;
      background-position: center;
      color: white;
      text-shadow: 1px 1px 3px black;
    }

    h1 {
      text-align: center;
      margin-bottom: 30px;
    }

    input, button {
      display: block;
      width: 100%;
      margin: 10px 0;
      padding: 10px;
      border: none;
      border-radius: 5px;
      font-size: 16px;
    }

    button {
      background-color: #ff4c4c;
      color: white;
      font-weight: bold;
      cursor: pointer;
    }

    button:hover {
      background-color: #cc0000;
    }

    ul {
      list-style: none;
      padding: 0;
    }

    li {
      background-color: rgba(0, 0, 0, 0.5);
      margin-bottom: 10px;
      padding: 10px;
      border-radius: 5px;
    }

    a {
      color: yellow;
      text-decoration: underline;
      margin-left: 10px;
    }

    .info {
      font-size: 14px;
      margin-top: 10px;
    }
  </style>
</head>
<body>
  <h1>Platform Komunikasi Darurat</h1>

  <input type="text" id="nama" placeholder="Nama Kontak">
  <input type="text" id="nomor" placeholder="Nomor HP">
  <button onclick="tambahKontak()">Tambah Kontak</button>
  <button onclick="hapusSemuaKontak()">Hapus Semua Kontak</button>

  <ul id="daftarKontak"></ul>

  <p class="info">Kontak dapat dihubungi langsung melalui WhatsApp, lengkap dengan lokasi kamu saat ini.</p>

  <script>
    function tambahKontak() {
      const nama = document.getElementById("nama").value;
      const nomor = document.getElementById("nomor").value;

      if (!nama || !nomor) {
        alert("Nama dan nomor wajib diisi.");
        return;
      }

      const kontak = { nama, nomor };
      simpanKontak(kontak);
      tampilkanKontak();

      document.getElementById("nama").value = "";
      document.getElementById("nomor").value = "";
    }

    function simpanKontak(kontakBaru) {
      let daftar = JSON.parse(localStorage.getItem("kontakDarurat")) || [];
      daftar.push(kontakBaru);
      localStorage.setItem("kontakDarurat", JSON.stringify(daftar));
    }

    function tampilkanKontak() {
      const ul = document.getElementById("daftarKontak");
      ul.innerHTML = "";

      const daftar = JSON.parse(localStorage.getItem("kontakDarurat")) || [];

      daftar.forEach(kontak => {
        const li = document.createElement("li");
        li.textContent = `${kontak.nama}: ${kontak.nomor}`;

        const tombolWA = document.createElement("a");
        tombolWA.textContent = "WhatsApp + Lokasi";
        tombolWA.href = "#";
        tombolWA.onclick = function () {
          if (!navigator.geolocation) {
            alert("Browser tidak mendukung lokasi.");
            return;
          }

          navigator.geolocation.getCurrentPosition(posisi => {
            const latitude = posisi.coords.latitude;
            const longitude = posisi.coords.longitude;
            const link = `https://wa.me/62${kontak.nomor.replace(/^0/, '')}?text=Darurat!%20Saya%20butuh%20bantuan.%20Ini%20lokasi%20saya:%20https://www.google.com/maps?q=${latitude},${longitude}`;
            window.open(link, "_blank");
          }, () => {
            alert("Gagal mengambil lokasi.");
          });

          return false;
        };

        li.appendChild(tombolWA);
        ul.appendChild(li);
      });
    }

    function hapusSemuaKontak() {
      const konfirmasi = confirm("Yakin mau hapus semua kontak?");
      if (konfirmasi) {
        localStorage.removeItem("kontakDarurat");
        tampilkanKontak();
      }
    }

    window.onload = tampilkanKontak;
  </script>
</body>
</html>
