# laravel-auth-template

## 從github安裝自訂的Laravel-auth-template
```bash
git clone https://github.com/aaron700117/laravel-auth-template.git
```
### 安裝必須套件
```bash
cd laravel-auth-template
npm install
composer install
```

### 初始化 (laravel-auth-template路徑下)
```bash
cp .env.example  .env
php artisan key:generate
touch database/database.sqlite
php artisan migrate
npm run build
```

### 下面命令能解決livewire.js flux.js 404問題
```bash
php artisan vendor:publish --force --tag=livewire:assets
php artisan flux:publish --all
```

#### 🔹 為什麼需要手動執行 `php artisan vendor:publish --force --tag=livewire:assets`？

Laravel Livewire 在某些情況下**不會自動發布資產**，原因如下：

1. **Livewire 採用「零設定」原則**，預設會透過路由 `/livewire/livewire.js` 提供 JS 資源，但這依賴 Laravel 的路由系統。
    
2. 在 **生產環境** 或使用 **Nginx / CloudPanel** 等非 Laravel 內建伺服器時，這些路由可能無法正確解析，導致 404。
    
3. 因此，**手動發布資產到** `public/vendor/livewire` 是最穩定的方式，確保 JS 能被靜態伺服器正確提供。

#### ✅ 發布 Blade Flux元件（例如自訂按鈕、表單等）：

```bash
php artisan flux:publish
```

你可以選擇要發布哪些元件，或使用 `--all` 一次全部發布：
```bash
php artisan flux:publish --all
```

### 解決Nginx 造成的flux.js 404問題
將原本的行變為註解
```
#location ~* ^.+\.(css|js|jpg|jpeg|gif|png|ico|gz|svg|svgz|ttf|otf|woff|woff2|eot|mp4|ogg|ogv|webm|webp|zip|swf|map)$ {
  
```
增加新的一行
```
location ~* ^.+\.(css|jpg|jpeg|gif|png|ico|gz|svg|svgz|ttf|otf|woff|woff2|eot|mp4|ogg|ogv|webm|webp|zip|swf|map)$ {
```

**其實就是CloudPanel產生的Nginx規則中排除 js**

检查Nginx的配置,爬坑最终发现:图片、js/css文件都会nginx中优先先匹配
原文:https://blog.csdn.net/huaishitou/article/details/103883763

**如果沒使用CloudPanel只使用純Nginx**
### 官方修改如下
### 設定 nginx
如果您在載入 Flux 的 JavaScript 和 CSS 資源時遇到問題，則可能需要設定 nginx 伺服器以允許這種情況發生。
預設情況下，Flux 在您的應用程式中公開兩個路由來提供其資產：**/flux/flux.js**和 **/flux/flux.css**。
對於大多數應用程式來說這都沒問題，但是如果您使用自訂配置的 nginx，您可能會從此端點收到 404。
若要解決此問題，您可以將以下內容新增至 nginx 設定中：
```
location ~ ^/flux/flux(\.min)?\.(js|css)$ {
    expires off;
    try_files $uri $uri/ /index.php?$query_string;
}
```

### 刀鋒樣板 加載flux
檔案路徑: laravel-auth-template/resources/views/components/layouts/app/header.blade.php
```
<head>        

	@include('partials.head')
	
	//如果沒下面這行須加上
	@fluxAppearance

</head>


<body>
	...
	{{ $slot }}

	//放在 `</body>` 前會比較穩定，不干擾頁面渲染。
	//如果沒下面這行須加上
	@fluxScripts
</body>
```


