# Simulasi Perpindahan Panas Menggunakan Dataset Cuaca

## Deskripsi Tugas
Tugas ini merupakan simulasi perpindahan panas yang dilakukan untuk memenuhi empat tugas utama:
1. Mengamati efek kecepatan pendinginan terhadap penurunan suhu benda.
2. Mensimulasikan proses pemanasan benda.
3. Menggunakan dataset cuaca nyata untuk mensimulasikan perubahan suhu benda.
4. Membandingkan simulasi dengan lingkungan tetap (CES) dan lingkungan yang berubah-ubah (DES).

Simulasi ini menggunakan data cuaca dari file `weatherHistory.csv` (diambil dari Kaggle) yang berisi informasi suhu per jam dari tahun 2006 hingga 2016. Untuk tugas 3 dan 4, fokus simulasi adalah data suhu dari 1 hingga 14 April 2006.

## Prasyarat
Untuk menjalankan Tugas ini, pastikan perangkat Anda memiliki:
- Python 3.6 atau versi lebih baru.
- Library Python berikut:
  - `pandas` (untuk mengolah data)
  - `numpy` (untuk perhitungan numerik)
  - `matplotlib` (untuk membuat grafik)

Anda dapat menginstal library ini dengan perintah:
```bash
pip install pandas numpy matplotlib
```

## Struktur File
- `weatherHistory.csv`: Dataset cuaca yang digunakan (pastikan file ini ada di direktori yang sama dengan kode).
- `simulate_heat_transfer.py`: Kode utama untuk menjalankan semua simulasi (Tugas 1 hingga 4).
- `README.md`: File ini, berisi panduan Tugas.

## Cara Menjalankan
1. Pastikan file `weatherHistory.csv` sudah ada di direktori yang sama dengan kode.
2. Simpan kode berikut dalam file bernama `simulate_heat_transfer.py`:

```python
import pandas as pd
import numpy as np
import matplotlib.pyplot as plt

# Membaca dataset
data = pd.read_csv('weatherHistory.csv')
data['Formatted Date'] = pd.to_datetime(data['Formatted Date'], utc=True)

# Fungsi untuk simulasi pendinginan (Tugas 1)
def simulate_cooling():
    T_init = 100.0
    T_env = 25.0
    dt = 1.0
    time = np.arange(0, 100, dt)
    cooling_rates = [0.05, 0.1, 0.2]
    
    plt.figure(figsize=(10, 6))
    for k in cooling_rates:
        T = T_init
        temperatures = []
        for t in time:
            temperatures.append(T)
            dT_dt = -k * (T - T_env)
            T += dT_dt * dt
        plt.plot(time, temperatures, label=f'Kecepatan Pendinginan={k}')
    plt.xlabel('Waktu (detik)')
    plt.ylabel('Suhu (°C)')
    plt.title('Penurunan Suhu dengan Berbagai Kecepatan Pendinginan')
    plt.legend()
    plt.grid()
    plt.show()

# Fungsi untuk simulasi pemanasan (Tugas 2)
def simulate_heating():
    T_init = 25.0
    T_source = 100.0
    dt = 1.0
    time = np.arange(0, 100, dt)
    heating_rate = 0.1
    T = T_init
    temperatures = []
    
    for t in time:
        temperatures.append(T)
        dT_dt = heating_rate * (T_source - T)
        T += dT_dt * dt
    plt.figure(figsize=(10, 6))
    plt.plot(time, temperatures, label=f'Kecepatan Pemanasan={heating_rate}')
    plt.xlabel('Waktu (detik)')
    plt.ylabel('Suhu (°C)')
    plt.title('Kenaikan Suhu dengan Pemanasan')
    plt.legend()
    plt.grid()
    plt.show()

# Fungsi untuk simulasi dengan dataset cuaca (Tugas 3)
def simulate_with_weather_data():
    start_date = '2006-04-01'
    end_date = '2006-04-14'
    weather_data = data[(data['Formatted Date'] >= start_date) & (data['Formatted Date'] <= end_date)]
    
    T_init = 25.0
    k = 0.05
    dt = 1.0
    time = np.arange(len(weather_data))
    T = T_init
    simulated_temps = []
    
    for i in range(len(time)):
        T_env = weather_data['Temperature (C)'].iloc[i]
        simulated_temps.append(T)
        dT_dt = -k * (T - T_env)
        T += dT_dt * dt
    
    plt.figure(figsize=(10, 6))
    plt.plot(weather_data['Formatted Date'], weather_data['Temperature (C)'], label='Suhu Lingkungan')
    plt.plot(weather_data['Formatted Date'], simulated_temps, label='Suhu Benda yang Disimulasikan')
    plt.xlabel('Tanggal')
    plt.ylabel('Suhu (°C)')
    plt.title('Simulasi Suhu dengan Data Cuaca (1-14 April 2006)')
    plt.legend()
    plt.grid()
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.show()

# Fungsi untuk membandingkan CES dan DES (Tugas 4)
def compare_ces_des():
    start_date = '2006-04-01'
    end_date = '2006-04-14'
    weather_data = data[(data['Formatted Date'] >= start_date) & (data['Formatted Date'] <= end_date)]
    
    T_init = 25.0
    k = 0.05
    dt = 1.0
    time = np.arange(len(weather_data))
    
    # CES: Lingkungan tetap (suhu rata-rata)
    T_env_constant = weather_data['Temperature (C)'].mean()
    T_ces = T_init
    ces_temps = []
    for i in range(len(time)):
        ces_temps.append(T_ces)
        dT_dt = -k * (T_ces - T_env_constant)
        T_ces += dT_dt * dt
    
    # DES: Lingkungan berubah-ubah (dari data cuaca)
    T_des = T_init
    des_temps = []
    for i in range(len(time)):
        T_env = weather_data['Temperature (C)'].iloc[i]
        des_temps.append(T_des)
        dT_dt = -k * (T_des - T_env)
        T_des += dT_dt * dt
    
    plt.figure(figsize=(10, 6))
    plt.plot(weather_data['Formatted Date'], ces_temps, label='CES (Lingkungan Tetap)')
    plt.plot(weather_data['Formatted Date'], des_temps, label='DES (Lingkungan Berubah)')
    plt.plot(weather_data['Formatted Date'], weather_data['Temperature (C)'], '--', label='Suhu Lingkungan (DES)')
    plt.xlabel('Tanggal')
    plt.ylabel('Suhu (°C)')
    plt.title('Perbandingan CES vs DES (1-14 April 2006)')
    plt.legend()
    plt.grid()
    plt.xticks(rotation=45)
    plt.tight_layout()
    plt.show()

# Jalankan semua simulasi
if __name__ == "__main__":
    print("Menjalankan Tugas 1: Simulasi Pendinginan")
    simulate_cooling()
    print("Menjalankan Tugas 2: Simulasi Pemanasan")
    simulate_heating()
    print("Menjalankan Tugas 3: Simulasi dengan Dataset Cuaca")
    simulate_with_weather_data()
    print("Menjalankan Tugas 4: Perbandingan CES vs DES")
    compare_ces_des()
```

3. Jalankan kode dengan perintah berikut:
```bash
python simulate_heat_transfer.py
```

Kode akan menghasilkan grafik untuk masing-masing tugas:
- Tugas 1: Grafik penurunan suhu dengan berbagai kecepatan pendinginan.
- Tugas 2: Grafik kenaikan suhu saat benda dipanaskan.
- Tugas 3: Grafik suhu benda dibandingkan dengan suhu lingkungan dari data cuaca.
- Tugas 4: Grafik perbandingan CES (lingkungan tetap) dan DES (lingkungan berubah).
