# Documentation PKL

## Install Projek Laravel

```php
composer create-project laravel/laravel myprojecr –prefer-dist
//--prefer-dist akan meinstall depencies yang dibutuhkan
```

### Create migration

```php
php artisan make:model Siswa -m
```

Table Siswas 

```php
Schema::create('siswas', function (Blueprint $table) {
	$table->id();
	$table->foreignId('user_id')->constrained()->onDelete('cascade');
	$table->string('nama');
	$table->string('nis')->unique();
	$table->enum('gender',['L','P']);
	$table->string('alamat');
	$table->string('kontak');
	$table->enum('status_pkl',['1','0'])->nullable();
	$table->timestamps();
});
```

Table Gurus

```php
Schema::create('gurus', function (Blueprint $table) {
	$table->id();
	$table->foreignId('user_id')->constrained()->onDelete('cascade');
	$table->string('nama');
	$table->string('nip')->unique();
	$table->enum('gender',['L','P']);
	$table->string('alamat');
	$table->string('kontak');
	$table->timestamps();
});
```

Table Industris

```php
Schema::create('industris', function (Blueprint $table) {
	$table->id();
	$table->string('nama');
	$table->string('bidang_usaha');
	$table->string('alamat');
	$table->string('kontak');
	$table->string('email')->nullable();
	$table->string('guru_pembimbing')->nullable();
	$table->timestamps();
});
```

Table Pkls

```php
Schema::create('pkls', function (Blueprint $table) {
	$table->id();
	$table->foreignId('siswa_id')->constrained('siswas')->onDelete('restrict');
	$table->foreignId('guru_id')->constrained('gurus');
	$table->foreignId('industri_id')->constrained('industris')->onDelete('restrict');
	$table->datetime('tanggal_mulai');
	$table->datetime('tanggal_selesai');
	$table->timestamps();
});
```

## install breeze untuk auth

```php
composer require laravel/breeze
php artisan breeze:install
pilih livewire volt
npm install
php artisan migrate
```

## Install Filament

```php
composer require filament/filament -W
// -W for depencies
php artisan filament:install --panels
// Install panel admin
php artisan make:filament-user
//create user filament
```

### Create Resource

```php
php artisan make:filament-resource User
```

User adalah model user

buat untuk model yang lain

## Install Filament shield

for role and permission

```php
composer require bezhansalleh/filament-shield
```

### publish the config

```php
php artisan vendor:publish --tag="filament-shield-config”
//tambahkan HasRoles ke user model
php artisan shield:setup
//install plugin ke panel
php artisan shield:install admin
//admin adalah id panel
php artisan shield:generate –all
//for generate permission and role
//beri permission untuk setiap role
```

## Create Middleware for role

```php
php artisan make:middleware RoleMiddleware
```

isi

public function handle(Request $request, Closure $next, string ...$roles): Response

{

if(!Auth::check()){

return redirect()->route('login');

}

$user = Auth::user();

if(!$user->hasAnyRole(...$roles)){

return redirect()->back()->with('error', 'You do not have permission to access this page.');

}

return $next($request);

}

—

…$roles berarti parameter yang bisa lebih dari satu atau variadic parameter

if(!$user->hasAnyRole(...$roles)){

berarti user yang tidak memiliki role yang ditentukan akan di redirect ke halaman sebelumnya dengna redirect()->back()

daftarkan di bootstrap/app.php dengna alias

- >withMiddleware(function (Middleware $middleware) {

//

$middleware->alias([

'role' => RoleMiddleware::class,

]);

penggunakan pada web.php

->middleware(['auth', 'verified','role:siswa,guru'])

pada adminpanel

- >authMiddleware([

Authenticate::class,

'role:super_admin',

]);
