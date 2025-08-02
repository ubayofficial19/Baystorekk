// src/App.js
import React, { useState, useEffect } from 'react';
import './App.css';

// URL base dari Strapi backend Anda. Pastikan ini benar.
const STRAPI_URL = "http://localhost:1337";

function App() {
  // State untuk menyimpan daftar produk yang didapat dari Strapi
  const [products, setProducts] = useState([]);
  // State untuk loading, agar bisa menampilkan pesan "memuat..."
  const [loading, setLoading] = useState(true);

  // useEffect akan berjalan sekali saat komponen pertama kali dimuat
  useEffect(() => {
    // Fungsi untuk mengambil data dari Strapi
    async function fetchProducts() {
      try {
        // Panggil API endpoint produk di Strapi.
        // "?populate=*" penting agar data gambar ikut terkirim.
        const response = await fetch(`${STRAPI_URL}/api/products?populate=*`);
        const data = await response.json();

        // Simpan data produk ke dalam state
        setProducts(data.data);
      } catch (error) {
        console.error("Gagal mengambil data produk:", error);
      } finally {
        // Apapun hasilnya, set loading menjadi false
        setLoading(false);
      }
    }

    fetchProducts();
  }, []); // Array kosong berarti efek ini hanya berjalan sekali

  return (
    <div className="container">
      <header className="header">
        <h1>Toko Kopi Kami</h1>
        <p>Pilih biji kopi premium favorit Anda yang kami siapkan secara fresh.</p>
      </header>

      {loading ? (
        <p>Memuat produk...</p>
      ) : (
        <div className="product-grid">
          {/* Loop (map) setiap produk dan tampilkan sebagai kartu */}
          {products.map((product) => (
            <div key={product.id} className="product-card">
              {/* Pastikan URL gambar digabung dengan URL base Strapi */}
              <img
                src={`${STRAPI_URL}${product.attributes.gambar.data.attributes.url}`}
                alt={product.attributes.nama}
                className="product-image"
              />
              <div className="product-info">
                <h2>{product.attributes.nama}</h2>
                <p className="product-description">{product.attributes.deskripsi}</p>
                <p className="product-price">
                  Rp {product.attributes.harga.toLocaleString('id-ID')}
                </p>
                <button className="order-button" onClick={() => alert(`Anda ingin memesan ${product.attributes.nama}`)}>
                  Pesan Sekarang
                </button>
              </div>
            </div>
          ))}
        </div>
      )}
    </div>
  );
}

export default App;
