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
direktori **public**.

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
