# Deployment Using Github Action for ThreeJS (With Vite)

- Install ThreeJS, Vite. `npm install vite` `npm install three`
- **Peringatan untuk asset .gltf**, setelah mencoba deployment pada lingkungan js, baik dengan vercel atau github, saya menemukan bahwa file .gltf selalu mengalami masalah ketika production (bahkan di server lokal), disarankan memakai file .glb, ini juga recommend karena ukurannya lebih kecil ketimbang .gltf
- Install vite-plugin-static-copy untuk melakukan rollup file (sehingga tidak perlu menggunakan metode import JavaScript ataupun meletakan file pada folder public)
  repo untuk plugin ini : https://github.com/sapphi-red/vite-plugin-static-copy<br>
  instalasi : `npm install vite-plugin-static-copy --save-dev`
- buat vite.config.js dengan :
  
  Pada targets, 'src' adalah folder tempat file yang ingin di-rollup, sementara 'dest' adalah folder tujuan untuk file yang di-rollup.
  ```
  import { defineConfig } from 'vite';
  import { viteStaticCopy } from 'vite-plugin-static-copy';
  
  
  export default defineConfig({
    assetsInclude: ['**/*.glb'],
    plugins: [
      viteStaticCopy({
        targets: [
          {
              src: 'file/*',
              dest: 'file'
            },
        ]
      })
    ],
    base : '/nama-repo/'
  });
   ```
  
- Untuk bisa menggunakan modul ES (ECMAScript modules) maka pada package.json tambahkan
  
  ```
    {
      type : "module",
      ...
    }
- Lakukan test dari server lokal dengan melakukan build lalu test preview
- Cara build adalah dengan
    ``npx vite build``
  bila tidak ada konfigurasi folder default adalah ./dist
- Cara test adalah dengan
  ``npx vite preview``
  lalu cek pada browser apakah web threejs berjalan dengan benar
- Buat folder .github/workflow lalu buat file deploy.yaml, isi file tersebut dengan konfigurasi yang dibutuhkan, contohnya pada : https://github.com/Habbatul/3D-Nostalgia/blob/main/.github/workflows/deploy.yml
- Setting pada repository project dengan pilih **Setting** -> **Action** -> **General** -> **Workflow permissions** -> pilih **Read and write permissions**
- Lakukan Commit-Push project Threejs, lihat apakah Github Action berjalan, bila gagal coba lakukan re-run
- Bila berhasil, pada **Setting** -> **Github Pages** pilih branch hasil deployment, bila mengikuti konfigurasi pada url yaml diatas maka branch nya adalah gh-pages (ini disesuaikan dengan konfigurasi yaml).

  
