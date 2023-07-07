# Anggota kelompok

* Muhammad Ariel Saputra - 312110099
* Zidan Lutfi Ramadhan - 312110304
* Nur Pratama - 312110305
* Yusuf Putra Bintang Satria - 312110317
* Daffa Gibran - 312110371

# Link Youtube 

https://youtu.be/ZMYhyl-Erx4

# Rancangan ERD database

![WhatsApp Image 2023-07-07 at 10 51 52](https://github.com/pyatamaa/yyy/assets/92738041/a3f6448c-ac81-4e79-bad6-bb6c610f05ab)

# Penjelasan tentang database

<img width="181" alt="Screenshot 2023-07-03 173621" src="https://github.com/pyatamaa/yyy/assets/92738041/7e2de403-0006-4047-9661-6952b4ee2ab4">

Buatlah database dengan nama **kas** kemudian buat tabel dengan nama **warga**.

<img width="630" alt="Screenshot 2023-07-03 173801" src="https://github.com/pyatamaa/yyy/assets/92738041/90db16c2-4871-43f9-acf5-590b85cbf953">

Setelah itu isi tabel **warga** dengan field berikut:
* **warga_id** dengan tipe integer dengan panjang 11 karakter, disetting sebagai primary key dan pilih not null.
* **nik** dengan tipe varchar dengan panjang 50 karakter disetting sebagai primary key dan pilih not null.
* **nama** dengan tipe varchar dengan panjang 200 karakter lalu pilih not null.
* **alamat** dengan tipe tinytext lalu pilih not null.
* **status** dengan tipe tinyint dengan panjang 4 karakter lalu pilih null.

# Penjelasan tentang routing dan controller

Routing merupakan proses yang mengatur arah atau rute dari request untuk menentukan fungsi/bagian mana yang akan memproses request tersebut. Pada framework CI4, routing bertujuan untuk menentukan Controller mana yang harus merespon sebuah request. Controller adalah class atau script yang bertanggung jawab merespon sebuah request.
Pada Codeigniter, request yang diterima oleh file index.php akan diarahkan ke Router untuk kemudian oleh router tesebut diarahkan ke Controller. Router terletak pada file app/config/Routes.php. Untuk routes masukkan kode berikut:
```
$routes->get('/', 'Data_Warga::index');
$routes->get('/data_warga', 'Data_Warga::index');
$routes->get('/laporan', 'Laporan::index');
$routes->group('admin', ['filter' => 'auth'],function($routes) {
    $routes->get('data_warga', 'Data_Warga::admin_index');
    $routes->get('iuran', 'iuran::admin_index');
    $routes->get('laporan', 'laporan::admin_index');
    $routes->add('data_warga/add', 'Data_Warga::add');
    $routes->add('iuran/add', 'iuran::add');
    $routes->add('data_warga/edit/(:any)', 'Data_Warga::edit/$1');
    $routes->add('iuran/edit/(:any)', 'iuran::edit/$1');
    $routes->get('data_warga/delete/(:any)', 'Data_Warga::delete/$1');
    $routes->get('iuran/delete/(:any)', 'iuran::delete/$1');
    $routes->add('logout', 'User::logout');
});
```
Setelah itu cek routes di cmd xampp dengan mengetikkan **Php spark routes**

![Screenshot (118)](https://github.com/pyatamaa/yyy/assets/92738041/1d02412a-429b-463a-9060-836c1181f00f)

setelah muncul seperti ini maka kita lanjutkan ke **controller**, buatlah file dengan nama **Data_warga.php** kemudian masukkan kode ini

```
<?php

namespace App\Controllers;

use App\Models\RtModel;

class Data_Warga extends BaseController
{
    public function index()
    {
        $title = 'Data Warga';
        $model = new RtModel();
        $rt = $model->findAll();
        return view('rt/index', compact('rt', 'title'));
    }

    public function admin_index()
    {
        $title = 'Data Warga';
        $model = new RtModel();
        $rt = $model->findAll();
        return view('rt/admin_index', compact('rt', 'title'));
    }

    public function add()
    {
        // validasi data.
        $validation = \Config\Services::validation();
        $validation->setRules(['nik' => 'required']);
        $isDataValid = $validation->withRequest($this->request)->run();
        
        if ($isDataValid)
        {
            $rt = new RtModel();
            $rt->insert([
                'nik' => $this->request->getPost('nik'),
                'nama' => $this->request->getPost('nama'),
                'kelamin' => $this->request->getPost('kelamin'),
                'alamat' => $this->request->getPost('alamat'),
                'no_rumah' => $this->request->getPost('no_rumah'),
                'status' => $this->request->getPost('status'),
            ]);
            return redirect('admin/data_warga');
        }
        $title = "Tambah Data Warga";
        return view('rt/form_add', compact('title'));
    }

    public function edit($id)
    {
        $rt = new RtModel();
        // validasi data.
        $validation = \Config\Services::validation();
        $validation->setRules(['nik' => 'required']);
        $isDataValid = $validation->withRequest($this->request)->run();
        
        if ($isDataValid)
        {
            $rt->update($id, [
                'nik' => $this->request->getPost('nik'),
                'nama' => $this->request->getPost('nama'),
                'kelamin' => $this->request->getPost('kelamin'),
                'alamat' => $this->request->getPost('alamat'),
                'no_rumah' => $this->request->getPost('no_rumah'),
            ]);
            return redirect('admin/data_warga');
        }
        
        // ambil data lama
        $data = $rt->where('warga_id', $id)->first();
        $title = "Edit Data Warga";
        return view('rt/form_edit', compact('title', 'data'));
    }

    public function delete($id)
    {
        $rt = new RtModel();
        $rt->delete($id);
        return redirect('admin/data_warga');
    }
}
```
kemudian buat lagi file dengan nama **iuran.php** dan masukkan kode berikut

```
<?php

namespace App\Controllers;

use App\Models\IuranModel;

class iuran extends BaseController
{
    public function index()
    {
        $title = 'Iuran Warga';
        $model = new IuranModel();
        $iuran = $model->findAll();
        return view('iuran/index', compact('iuran', 'title'));
    }

    public function admin_index()
    {
        $title = 'Iuran Warga';
        $model = new IuranModel();
        $iuran = $model->findAll();
        return view('iuran/admin_index', compact('iuran', 'title'));
    }

    public function add()
    {
        // validasi data.
        $validation = \Config\Services::validation();
        $validation->setRules(['keterangan' => 'required']);
        $isDataValid = $validation->withRequest($this->request)->run();
        
        if ($isDataValid)
        {
            $iuran = new IuranModel();
            $iuran->insert([
                'keterangan' => $this->request->getPost('keterangan'),
                'tanggal' => $this->request->getPost('tanggal'),
                'bulan' => $this->request->getPost('bulan'),
                'tahun' => $this->request->getPost('tahun'),
                'jumlah' => $this->request->getPost('jumlah'),
                'warga_id' => $this->request->getPost('warga_id'),
            ]);
            return redirect('admin/iuran');
        }
        $title = "Tambah Iuran";
        return view('iuran/form_add', compact('title'));
    }

    public function edit($id)
    {
        $iuran = new IuranModel();
        // validasi data.
        $validation = \Config\Services::validation();
        $validation->setRules(['keterangan' => 'required']);
        $isDataValid = $validation->withRequest($this->request)->run();
        
        if ($isDataValid)
        {
            $iuran->update($id, [
                'keterangan' => $this->request->getPost('keterangan'),
                'tanggal' => $this->request->getPost('tanggal'),
                'bulan' => $this->request->getPost('bulan'),
                'tahun' => $this->request->getPost('tahun'),
                'jumlah' => $this->request->getPost('jumlah'),
                'warga' => $this->request->getPost('warga_id')
            ]);
            return redirect('admin/iuran');
        }
        
        // ambil data lama
        $data = $iuran->where('id', $id)->first();
        $title = "Edit Data Transaksi Iuran";
        return view('iuran/form_edit', compact('title', 'data'));
    }

    public function delete($id)
    {
        $iuran = new IuranModel();
        $iuran->delete($id);
        return redirect('admin/iuran');
    }
}
```
Kemudian buat lagi file dengan nama **Laporan.php** lalu masukkan kode berikut
```
<?php namespace App\Controllers;
 
use CodeIgniter\Controller;
use App\Models\LaporanModel;
 
class Laporan extends BaseController
{
    public function index()
    {
        $title = 'Laporan Warga';
        $model = new LaporanModel();
        $iuran = $model->getLaporan();
        return view('laporan/laporan.php', compact('iuran', 'title'));
    }

    public function admin_index()
    {
        $title = 'Laporan Warga';
        $model = new LaporanModel();
        $iuran = $model->getLaporan();
        return view('laporan/admin_laporan.php', compact('iuran', 'title'));
    }
}
```
Kemudian buat lagi file dengan nama **User.php** lalu masukkan kode berikut
```
<?php

namespace App\Controllers;

use App\Models\UserModel;

class User extends BaseController
{
    public function index()
    {
        $title = 'Daftar User';
        $model = new UserModel();
        $users = $model->findAll();
        return view('user/index', compact('users', 'title'));
    }

    public function login()
    {
        helper(['form']);
        $email = $this->request->getPost('email');
        $password = $this->request->getPost('password');
        if (!$email)
        {
            return view('user/login');
        }

        $session = session();
        $model = new UserModel();
        $login = $model->where('useremail', $email)->first();
        if ($login)
        {
            $pass = $login['userpassword'];
            if (password_verify($password, $pass))
            {
            $login_data = [
                'user_id' => $login['id'],
                'user_name' => $login['username'],
                'user_email' => $login['useremail'],
                'logged_in' => TRUE,
            ];
            $session->set($login_data);
            return redirect('admin/data_warga');
        }
        else
        {
            $session->setFlashdata("flash_msg", "Password salah.");
            return redirect()->to('/user/login');
            }
        }
        else
        {
            $session->setFlashdata("flash_msg", "email tidak terdaftar.");
            return redirect()->to('/user/login');
        }
    }

    public function logout()
    {
        session()->destroy();
        return redirect()->to('/');
    }
}
```
# Membuat layout web dengan CSS
Pada dasarnya layout web dengan css dapat diimplamentasikan dengan mudah pada codeigniter. Yang
perlu diketahui adalah, pada Codeigniter 4 file yang menyimpan asset css dan javascript terletak pada
direktori **public**. Pada folder view kita akan membuat layout web untuk user dan admin sebagai CRUD.

Sebagai contohnya seperti yang bisa dilihat pada screenshot dibawah ini

![WhatsApp Image 2023-07-05 at 17 18 12 (1)](https://github.com/pyatamaa/yyy/assets/92738041/b1a71b5d-52a1-4515-a560-54ac0b8e735c)

![WhatsApp Image 2023-07-05 at 17 18 12](https://github.com/pyatamaa/yyy/assets/92738041/dccd5178-8159-4343-9bc3-ae0912f35a85)

![WhatsApp Image 2023-07-05 at 17 18 11](https://github.com/pyatamaa/yyy/assets/92738041/ed99c44b-a810-47d6-b6c5-805acdce4b0a)

![WhatsApp Image 2023-07-05 at 17 18 10 (1)](https://github.com/pyatamaa/yyy/assets/92738041/fa53ac3c-1b1c-4430-8cfe-1528c6b1f5d9)

![WhatsApp Image 2023-07-05 at 17 18 09](https://github.com/pyatamaa/yyy/assets/92738041/e474c9b7-cd35-4440-a867-c9421322210c)

![WhatsApp Image 2023-07-05 at 17 18 10](https://github.com/pyatamaa/yyy/assets/92738041/89e7686e-e90b-4d97-a8e6-58d0a0a8864d)

![WhatsApp Image 2023-07-05 at 17 18 31](https://github.com/pyatamaa/yyy/assets/92738041/8440fbd9-9add-4797-8451-33e42f728b5b)

![WhatsApp Image 2023-07-05 at 17 18 13](https://github.com/pyatamaa/yyy/assets/92738041/2cd2cc73-2f74-48a6-b9ea-73ea0e09df8c)


disini kita akan membuat 2 css untuk tampilan user dan tampilan admin, pertama kita akan buat css untuk user dengan nama **style.css**
```
/* import google font */
@import
url('https://fonts.googleapis.com/css2?family=Open+Sans:ital,wght@0,300;0,400;0,600;0,700;0,800;1,300;1,400;1,600;1,700;1,800&display=swap');
@import
url('https://fonts.googleapis.com/css2?family=Open+Sans+Condensed:ital,wght@0,300;0,700;1,300&display=swap');

/* Reset CSS */
* {
    margin: 0;
    padding: 0;
}
body {
    line-height:1;
    font-size:100%;
    font-family:'Open Sans', sans-serif;
    color:#5a5a5a;
}
#container {
    width: 980px;
    margin: 0 auto;
    box-shadow: 0 0 1em #cccccc;
}

/* header */
header {
    padding: 20px;
    background-image: url(bg2.jpg);
}
header h1 {
    margin: 20px 10px;
    color: #f3ef00;
    text-align: center;
    font-style: oblique;
    font-weight: bold;
    text-shadow:2px 2px red;
}

/* navigasi */
nav {
    background-color: #db5719;
    height:50px;
    line-height:50px;
    position:relative;
    margin:auto;
    font-style: oblique;
    font-weight: bold;  
}
nav ul {
    list-style:none;
}
nav ul li a {
    float:left;
    width:150px;
    color:#fff;
    text-decoration:none;
    display:block;
    text-align:center;
}
nav ul li a.active,
nav ul li a:hover {
    background-color: #ea912b;
    display:block;
}

/*css untuk menu hover dropdown*/
nav ul li:hover .sub1 {display:block}
nav ul .sub1 {
    display:none;
    position:absolute;
    left:300px;
    top:50px;
    background-color:#ffae52;
}
.sub1 a:hover { color:#fff }

/* footer */
footer {
    clear:both;
    background-color:#1d1d1d;
    padding:20px;
    color:#eee;
}

/* Login Wrapper */
#login-wrapper {
    align-items: center;
    width: 500px;
    margin: 10px;
    margin-left: 30%;
    margin-top: 50px;
    padding: 20px;
    box-shadow: 0 0 1em #752929;
    background-image: url(bg.jpg);
    background-repeat: no-repeat;
    overflow: hidden;
    background-size: cover;
    color: #ffffff
}

/* Isi */
h1{
    margin-bottom: 20px;
}

.form-control{
    width: 100%;
    padding: 10px;
    border: 2px solid #cccccc;
    box-sizing: border-box;
    font-size: 15px;
    margin-bottom: 15px;
}

button[type=submit]{
    padding: 7px;
    color: white;
    background-color: rgb(44, 118, 148);
    box-sizing: border-box;
    font-size: 15px;
    border-radius: 4px;
}

button[type=submit]:active,
button[type=submit]:hover{
    opacity: 75%;
}

/* Tabel Warga */
body{
    font-family: sans-serif;
}
table{
    border-collapse: collapse;
    margin: 20px;
    width: 95%;
}
table td{
    border: 1px solid #c9c9c9;
    font-size: 19px;
    padding: 10px 8px;
}
table th {
    background:#ff8c49;
    color:#ffffff;
    font-size: 17px;
    text-align: left;
    border: 1px solid #c9c9c9;
    padding: 13px 15px;
}
table tr {
    background:#ffffff;
    text-align: left;
}
tr:hover{
    background: #e7e7e7;
}
```
terakhir kita akan buat css untuk admin dengan nama **admin.css**
```
/* import google font */
@import
url('https://fonts.googleapis.com/css2?family=Open+Sans:ital,wght@0,300;0,400;0,600;0,700;0,800;1,300;1,400;1,600;1,700;1,800&display=swap');
@import
url('https://fonts.googleapis.com/css2?family=Open+Sans+Condensed:ital,wght@0,300;0,700;1,300&display=swap');

/* Reset CSS */
* {
    margin: 0;
    padding: 0;
}
body {
    line-height:1;
    font-size:100%;
    font-family:'Open Sans', sans-serif;
    color:#5a5a5a;
}
#container {
    width: 980px;
    margin: 0 auto;
    box-shadow: 0 0 1em #ffffff;
}

/* header */
header {
    padding: 20px;
    background-image: url(bg2.jpg);
}
header h1 {
    margin: 20px 10px;
    color: #f3ef00;
    text-align: center;
    font-style: oblique;
    font-weight: bold;
    text-shadow:2px 2px red;
}

/* navigasi */
nav {
    background-color: #db5719;
    height:50px;
    line-height:50px;
    position:relative;
    margin:auto;
    font-style: oblique;
    font-weight: bold;    
}
nav ul {
    list-style:none;
}
nav ul li a {
    float:left;
    width:150px;
    color:#fff;
    text-decoration:none;
    display:block;
    text-align:center;
}
nav ul li a.active,
nav ul li a:hover {
    background-color: #ea912b;
    display:block;
}

/*css untuk menu hover dropdown*/
nav ul li:hover .sub1 {display:block}
nav ul .sub1 {
    display:none;
    position:absolute;
    left:300px;
    top:50px;
    background-color:#ffae52;
}
.sub1 a:hover { color:#fff }

nav ul li:hover .sub2 {display:block}
nav ul .sub2 {
    display:none;
    position:absolute;
    left:150px;
    top:50px;
    background-color:#ffae52;
}
.sub2 a:hover { color:#fff }

nav ul li:hover .sub3 {display:block}
nav ul .sub3 {
    display:none;
    position:absolute;
    left:0px;
    top:50px;
    background-color:#ffae52;
}
.sub3 a:hover { color:#fff }

/* footer */
footer {
    clear:both;
    background-color:#1d1d1d;
    padding:20px;
    color:#eee;
}

/* Login Wrapper */
#login-wrapper {
    align-items: center;
    width: 500px;
    margin: 10px;
    margin-left: 30%;
    margin-top: 50px;
    padding: 20px;
    box-shadow: 0 0 1em #cccccc;
}

/* Tabel Warga */
body{
    font-family: sans-serif;
}
table{
    border-collapse: collapse;
    margin: 20px;
    width: 95%;
}
table td{
    border: 1px solid #c9c9c9;
    font-size: 19px;
    padding: 10px 8px;
}
table th {
    background:#ff8c49;
    color:#ffffff;
    font-size: 17px;
    text-align: left;
    border: 1px solid #c9c9c9;
    padding: 13px 15px;
}
table tr {
    background:#ffffff;
    text-align: left;
}
tr:hover{
    background: #e7e7e7;
}

/* ADMIN TOMBOL */
.btn{
    font-size: 14px;
    background-color: #afafaf;
    color: #444444;
    border-radius: 5px;
    padding: 10px 20px;
    margin-top: 8x;
    text-decoration: none;
}

.btn-danger{
    font-size: 14px;
    background-color: rgb(223, 35, 35);
    color: white;
    border-radius: 5px;
    padding: 10px 20px;
    margin-top: 8x;
    text-decoration: none;
}

a:active,
a:hover{
    opacity: 80%;
}

/* TAMBAH WARGA */

h2{
    margin-top: 20px;
    margin-left: 20px;
}

textarea{
    width: 50%;
    padding: 10px;
    border: 2px solid gray;
    box-sizing: border-box;
    font-size: 15px;
    margin-left: 20px;
    margin-top: 15px;
}

input[type=text]{
    width: 50%;
    padding: 8px;
    border: 2px solid gray;
    box-sizing: border-box;
    font-size: 15px;
    margin-left: 20px;
    margin-top: 15px;
}

.btn-large{
    padding: 7px 10px;
    background-color: rgb(103, 139, 180);
    color: white;
    font-size: 15px;
    margin: 20px;
    float: right;
    margin-right: 470px;
}

.btn-back{
    padding: 10px;
    background-color: rgb(30, 117, 216);
    color: white;
    box-sizing: border-box;
    font-size: 15px;
    margin: 20px;
    float: left;
}

.btn-large:active,
.btn-large:hover{
    opacity: 80%;
}
```
Kita buat folder baru dengan nama **iuran** dalam direktori views, kemudian buat file **admin_index.php** dalam folder **iuran** lalu masukkan kode berikut
```
<?= $this->include('template/admin_header'); ?>

<table class="table">
    <thead>
        <tr>
            <th>ID</th>
            <th>Keterangan</th>
            <th>Tanggal</th>
            <th>Bulan</th>
            <th>Tahun</th>
            <th>Jumlah</th>
            <th>ID Warga</th>
            <th>Aksi</th>
        </tr>
    </thead>
    <tbody>
    <?php if($iuran): foreach($iuran as $row): ?>
    <tr>
        <td><?= $row['id']; ?></td>
        <td><?= $row['keterangan']; ?></td>
        <td><?= $row['tanggal']; ?></td>
        <td><?= $row['bulan']; ?></td>
        <td><?= $row['tahun']; ?></td>
        <td><?= $row['jumlah']; ?></td>
        <td><?= $row['warga_id']; ?></td>
        <td>
            <a class="btn" href="<?= base_url('/admin/iuran/edit/' .$row['id']);?>">Ubah</a>
            <a class="btn btn-danger" onclick="return confirm('Yakin menghapus data?');" href="<?= base_url('/admin/iuran/delete/' .$row['id']);?>">Hapus</a>
        </td>
    </tr>
    <?php endforeach; else: ?>
    <tr>
        <td colspan="8">Belum ada data.</td>
    </tr>
    <?php endif; ?>
    </tbody>
</table>

<?= $this->include('template/admin_footer'); ?>
```
setelah itu buat lagi file dengan nama **form_add.php**
```
<?= $this->include('template/admin_header'); ?>

<h2><?= $title; ?></h2>
<form action="" method="post">
    <p><input type="text" name="keterangan" placeholder="Keterangan"></p>
    <p><input type="text" id= "date" name="tanggal" placeholder="Tanggal"></p>
    <p><input type="text" name="bulan" placeholder="Bulan"></p>
    <p><input type="text" name="tahun" placeholder="Tahun"></p>
    <p><input type="text" name="jumlah" placeholder="Jumlah"></p>
    <p><input type="text" name="warga_id" placeholder="ID Warga"></p>
    <p><input type="submit" value="Tambah" class="btn btn-large"></p>
    <a href="<?= base_url('/admin/iuran');?>" class="btn btn-back">Batal</a>
</form>

<?= $this->include('template/admin_footer'); ?>
```
setelah itu buat lagi file dengan nama **form_edit.php**
```
<?= $this->include('template/admin_header'); ?>

<h2><?= $title; ?></h2>
<form action="" method="post">
    <p>
        <input type="text" name="keterangan" value="<?= $data['keterangan'];?>" >
    </p>
    <p>
        <input type="text" name="tanggal" value="<?= $data['tanggal'];?>" >
    </p>
    <p>
        <input type="text" name="bulan" value="<?= $data['bulan'];?>" >
    </p>
    <p>
        <input type="text" name="tahun" value="<?= $data['tahun'];?>" >
    </p>
    <p>
        <input type="text" name="jumlah" value="<?= $data['jumlah'];?>" >
    </p>
    <p><input type="submit" value="Kirim" class="btn btn-large"></p>
</form>

<?= $this->include('template/admin_footer'); ?>
```
kemudian buat lagi file dengan nama **index.php**
```
<?= $this->include('template/header'); ?>

<table class="table">
    <thead>
        <tr>
            <th>ID</th>
            <th>Keterangan</th>
            <th>Tanggal</th>
            <th>Bulan</th>
            <th>Tahun</th>
            <th>Jumlah</th>
            <th>ID Warga</th>
        </tr>
    </thead>
    <tbody>
    <?php if($iuran): foreach($iuran as $row): ?>
    <tr>
        <td><?= $row['id']; ?></td>
        <td><?= $row['keterangan']; ?></td>
        <td><?= $row['tanggal']; ?></td>
        <td><?= $row['bulan']; ?></td>
        <td><?= $row['tahun']; ?></td>
        <td><?= $row['jumlah']; ?></td>
        <td><?= $row['warga_id']; ?></td>
    </tr>
    <?php endforeach; else: ?>
    <tr>
        <td colspan="6">Belum ada data.</td>
    </tr>
    <?php endif; ?>
    </tbody>
</table>

<?= $this->include('template/footer'); ?>
```
balik lagi ke view, kita buat folder baru dengan nama **laporan** lalu buat folder baru dengan nama **admin_laporan.php**
```
<?= $this->include('template/admin_header'); ?>

<table class="table">
    <thead>
        <tr>
            <th>ID</th>
            <th>Nama Warga</th>
            <th>NIK</th>
            <th>Bulan</th>
            <th>Tahun</th>
            <th>Jumlah Kas</th>
        </tr>
    </thead>
    <tbody>
    <?php if($iuran): foreach($iuran as $row): ?>
    <tr>
        <td><?= $row['warga_id']; ?></td>
        <td><?= $row['nama']; ?></td>
        <td><?= $row['nik']; ?></td>
        <td><?= $row['bulan']; ?></td>
        <td><?= $row['tahun']; ?></td>
        <td><?= $row['jumlah']; ?></td>
    </tr>
    <?php endforeach; else: ?>
    <tr>
        <td colspan="6">Belum ada data.</td>
    </tr>
    <?php endif; ?>
    </tbody>
</table>

<?= $this->include('template/admin_footer'); ?>
```
lalu buat lagi file baru dengan nama **laporan.php**
```
<?= $this->include('template/header'); ?>

<table class="table">
    <thead>
        <tr>
            <th>ID</th>
            <th>Nama Warga</th>
            <th>NIK</th>
            <th>Bulan</th>
            <th>Tahun</th>
            <th>Jumlah Kas</th>
        </tr>
    </thead>
    <tbody>
    <?php if($iuran): foreach($iuran as $row): ?>
    <tr>
        <td><?= $row['warga_id']; ?></td>
        <td><?= $row['nama']; ?></td>
        <td><?= $row['nik']; ?></td>
        <td><?= $row['bulan']; ?></td>
        <td><?= $row['tahun']; ?></td>
        <td><?= $row['jumlah']; ?></td>
    </tr>
    <?php endforeach; else: ?>
    <tr>
        <td colspan="6">Belum ada data.</td>
    </tr>
    <?php endif; ?>
    </tbody>
</table>

<?= $this->include('template/footer'); ?>
```
kembali lagi kita buat folder baru didalam view dengan nama **rt** kemudian buat file baru dengan nama **admin_index**
```
<?= $this->include('template/admin_header'); ?>

<table class="table">
    <thead>
        <tr>
            <th>No</th>
            <th>Nik</th>
            <th>Nama</th>
            <th>Kelamin</th>
            <th>Alamat</th>
            <th>Nomor</th>
            <th>Aksi</th>
        </tr>
    </thead>
    <tbody>
    <?php if($rt): foreach($rt as $row): ?>
    <tr>
        <td><?= $row['warga_id']; ?></td>
        <td><?= $row['nik']; ?></td>
        <td><?= $row['nama']; ?></td>
        <td><?= $row['kelamin']; ?></td>
        <td><?= $row['alamat']; ?></td>
        <td><?= $row['no_rumah']; ?></td>
        <td>
            <a class="btn" href="<?= base_url('/admin/data_warga/edit/' .$row['warga_id']);?>">Ubah</a>
            <a class="btn btn-danger" onclick="return confirm('Yakin menghapus data?');" href="<?= base_url('/admin/data_warga/delete/' .$row['warga_id']);?>">Hapus</a>
        </td>
    </tr>
    <?php endforeach; else: ?>
    <tr>
        <td colspan="7">Belum ada data.</td>
    </tr>
    <?php endif; ?>
    </tbody>
</table>

<?= $this->include('template/admin_footer'); ?>
```
lalu buat file baru dengan nama **form_add.php*
```
<?= $this->include('template/admin_header'); ?>

<h2><?= $title; ?></h2>
<form action="" method="post">
    <p><input type="text" name="nik" placeholder="NIK"></p>
    <p><input type="text" name="nama" placeholder="Nama"></p>
    <p><input type="text" name="kelamin" placeholder="Jenis Kelamin"></p>
    <p><textarea name="alamat" cols="10" rows="10" placeholder="Alamat"></textarea></p>
    <p><input type="text" name="no_rumah" placeholder="Nomor Rumah"></p>
    <p><input type="submit" value="Tambah" class="btn btn-large"></p>
    <a href="<?= base_url('/admin/data_warga');?>" class="btn btn-back">Batal</a>
</form>

<?= $this->include('template/admin_footer'); ?>
```
lalu buat file baru lagi dengan nama **form_edit.php**
```
<?= $this->include('template/admin_header'); ?>

<h2><?= $title; ?></h2>
<form action="" method="post">
    <p>
        <input type="text" name="nik" value="<?= $data['nik'];?>" >
    </p>
    <p>
        <input type="text" name="nama" value="<?= $data['nama'];?>" >
    </p>
    <p>
        <input type="text" name="kelamin" value="<?= $data['kelamin'];?>" >
    </p>
    <p>
        <textarea name="alamat" cols="10" rows="10"><?=$data['alamat'];?></textarea>
    </p>
    <p>
        <input type="text" name="no_rumah" value="<?= $data['no_rumah'];?>" >
    </p>
    <p><input type="submit" value="Kirim" class="btn btn-large"></p>
</form>

<?= $this->include('template/admin_footer'); ?>
```
lalu buat lagi file baru dengan nama **index.php**
```
<?= $this->include('template/header'); ?>

<table class="table">
    <thead>
        <tr>
            <th>No</th>
            <th>Nik</th>
            <th>Nama</th>
            <th>Kelamin</th>
            <th>Alamat</th>
            <th>No. Rumah</th>

        </tr>
    </thead>
    <tbody>
    <?php if($rt): foreach($rt as $row): ?>
    <tr>
        <td><?= $row['warga_id']; ?></td>
        <td><?= $row['nik']; ?></td>
        <td><?= $row['nama']; ?></td>
        <td><?= $row['kelamin']; ?></td>
        <td><?= $row['alamat']; ?></td>
        <td><?= $row['no_rumah']; ?></td>
    </tr>
    <?php endforeach; else: ?>
    <tr>
        <td colspan="6">Belum ada data.</td>
    </tr>
    <?php endif; ?>
    </tbody>
</table>

<?= $this->include('template/footer'); ?>
```
kemudian buat folder baru didalan view dengan nama **template** lalu buat file dengan nama **admin_header.php**
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title><?= $title; ?></title>
    <link rel="stylesheet" href="<?= base_url('/admin.css');?>">

    <link rel="stylesheet" href="//code.jquery.com/ui/1.12.1/themes/base/jquery-ui.css">
    <link rel="stylesheet" href="/resources/demos/style.css">
    <script src="https://code.jquery.com/jquery-1.12.4.js"></script>
    <script src="https://code.jquery.com/ui/1.12.1/jquery-ui.js"></script>

    <script>
    $( function() {
        $( "#date" ).datepicker({
        dateFormat: "yy-mm-dd"
        });
    } );
  </script>
</head>
<body>
    <div id="container">
    <header>
        <h1>DASHBOARD</h1>
    </header>
    <nav>
        <ul>
            <li><a href="<?= base_url('/admin/data_warga');?>">Data Warga</a>
                <ul class="sub3">
                    <li><a href="<?= base_url('/admin/data_warga/add');?>">Tambah Warga</a></li>
                </ul>
            </li>
            <li><a href="<?= base_url('/admin/iuran');?>">Kas Warga</a>
                <ul class="sub2">
                    <li><a href="<?= base_url('/admin/iuran/add');?>">Tambah Iuran</a></li>
                </ul>
            </li>
            <li><a href="">Laporan Kas</a>
                <ul class="sub1">
                    <li><a href="<?= base_url('/admin/laporan');?>">Jumlah Kas</a></li>
                </ul>
            </li>
            <li><a href="<?= base_url('/admin/logout');?>">Logout</a></li>
        </ul>
    </nav>
```
lalu buat file baru dengan nama **admin_footer.php**
```
    <footer>
        <p>&copy; Universitas Pelita Bangsa</p>
    </footer>
    </div>
</body>
</html>
```
lalu buat file baru dengan nama **header.php**
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title><?= $title; ?></title>
    <link rel="stylesheet" href="<?= base_url('/style.css');?>">
</head>
<body>
    <div id="container">
    <header>
        <h1>DASHBOARD</h1>
    </header>
    <nav>
        <ul>
            <li><a href="<?= base_url('/data_warga');?>">Data Warga</a></li>
            <li><a href="<?= base_url('/iuran');?>">Kas Warga</a></li>
            <li><a href="">Laporan Kas</a>
                <ul class="sub1">
                    <li><a href="<?= base_url('/laporan');?>">Jumlah Kas</a></li>
                </ul>
            </li>
            <li><a href="<?= base_url('/user/login');?>">Login</a></li>
        </ul>
    </nav>
```
lalu buat file baru dengan nama **footer.php**
```
    <footer>
        <p>&copy; Universitas Pelita Bangsa</p>
    </footer>
    </div>
</body>
</html>
```
kembali ke view lalu buat folder baru dengan nama **user** lalu buat file dengan nama **login.php** 
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title>Login</title>
    <link rel="stylesheet" href="<?= base_url('/style.css');?>">
</head>
<body>
    <div id="login-wrapper">
        <h1>LOGIN</h1>
        <?php if(session()->getFlashdata('flash_msg')):?>
            <div class="alert alert-danger"><?=session()->getFlashdata('flash_msg') ?></div>
        <?php endif;?>
        <form action="" method="post">
            <div class="mb-3">
                <i class="fa fa-user"><i>
                <label for="InputForEmail" class="form-label">Email Address</label>
                <input type="email" name="email" class="form-control" id="InputForEmail" value="<?= set_value('email') ?>">
            </div>
            <div class="mb-3">
                <label for="InputForPassword" class="form-label">Password</label>
                <input type="password" name="password" class="form-control" id="InputForPassword">
            </div>
            <button type="submit" class="btn btn-primary">Login</button>
        </form>
    </div>
</body>
</html>
```
lalu kembali ke view buat file dengan nama **halaman.php**
```
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <title><?= $title; ?></title>
</head>
<body>
    <?= $this->include('template/header'); ?>
    <h1><?= $title; ?></h1>
    <hr>
    <p><?= $content; ?></p>
    <?= $this->include('template/footer'); ?>
</body>
</html>
```
# Membuat database seeder
Database seeder digunakan untuk membuat data dummy. Untuk keperluan ujicoba modul login, kita
perlu memasukkan data user dan password kedaalam database. Untuk itu buat database seeder
untuk tabel user. Buka CLI, kemudian tulis perintah berikut:

**php spark make:seeder UserSeeder**

Selanjutnya, buka file **UserSeeder.php** yang berada di lokasi direktori
**/app/Database/Seeds/UserSeeder.php** kemudian isi dengan kode berikut:
```
<?php

namespace App\Database\Seeds;

use CodeIgniter\Database\Seeder;

class UserSeeder extends Seeder
{
	public function run()
	{
		$model = model('UserModel');
		$model->insert([
			'username' => 'admin',
			'useremail' => 'admin@gmail.com',
			'userpassword' => password_hash('admin', PASSWORD_DEFAULT),
		]);
	}
}
```
Selanjutnya buka kembali CLI dan ketik perintah berikut:
**php spark db:seed UserSeeder**

# Menambahkan Auth filter
Selanjutnya membuat filer untuk halaman admin. Buat file baru dengan nama **Auth.php** pada
direktori **app/Filters**
```
<?php namespace App\Filters;

use CodeIgniter\HTTP\RequestInterface;
use CodeIgniter\HTTP\ResponseInterface;
use CodeIgniter\Filters\FilterInterface;

class Auth implements FilterInterface
{
    public function before(RequestInterface $request, $arguments = null)
    {
        // jika user belum login
        if(! session()->get('logged_in')){
            // maka redirct ke halaman login
            return redirect()->to('/user/login');
        }
    }

    public function after(RequestInterface $request, ResponseInterface $response, $arguments = null)
    {
        // Do something here
    }
}
```
