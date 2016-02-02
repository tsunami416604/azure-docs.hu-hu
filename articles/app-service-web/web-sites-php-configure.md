<properties
    pageTitle="在 Azure App Service Web Apps 中設定 PHP | Microsoft Azure"
    description="了解如何設定預設的 PHP 安裝，或是在 Azure App Service 中新增適用於 Web Apps 的自訂 PHP 安裝。"
    services="app-service"
    documentationCenter="php"
    authors="tfitzmac"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="PHP"
    ms.topic="article"
    ms.date="12/16/2015"
    ms.author="tomfitz"/>


# 在 Azure App Service Web Apps 中設定 PHP

## 簡介

本指南將示範如何設定 Web 應用程式中的內建 PHP 執行階段 [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714), 、 提供自訂 PHP 執行階段，以及啟用擴充功能。 若要使用的應用程式服務，請註冊 [免費試用]。 若要充分利用本指南，您應該先在 App Service 中建立 PHP Web 應用程式。

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)]

## 作法：變更內建 PHP 版本

當您建立 App Service Web 應用程式時，預設會安裝 PHP 5.4，而且可立即使用。 若要查看可用的修訂版、 其預設組態及啟用的擴充，最好是部署呼叫 [phpinfo ()] 函式的指令碼。

PHP 5.5 和 PHP 5.6 版本同樣可供使用，但預設並未啟用。 若要更新 PHP 版本，請遵循下列方法其中之一：

### Azure 入口網站

1. 瀏覽至您的 web 應用程式中 [Azure 入口網站](https://portal.azure.com) ，然後按一下 **設定** ] 按鈕。

    ![Web 應用程式設定][settings-button]

2. 從 [設定]**** 分頁中，選取 [應用程式設定]**** 並選擇新的 PHP 版本。

    ![應用程式設定][application-settings]

3. 按一下 [Web 應用程式設定]**** 分頁頂端的 [儲存]**** 按鈕。

    ![儲存組態設定。][save-button]

### Azure PowerShell (Windows)

1. 開啟 Azure PowerShell 並登入您的帳戶。

        PS C:\> Login-AzureRmAccount

2. 設定 Web 應用程式的 PHP 版本。

        PS C:\> Set-AzureWebsite -PhpVersion [5.4 | 5.5 | 5.6] -Name {site-name}

3. PHP 版本現在已設定完成。 您可確認這些設定：

        PS C:\> Get-AzureWebsite -Name {site-name} | findstr PhpVersion


### Azure 命令列介面 (Linux、Mac、Windows)

若要使用 Azure 命令列介面，您必須在電腦上安裝 **Node.js**。

1. 開啟 [終端機]，然後登入您的帳戶。

        azure login

2. 設定 Web 應用程式的 PHP 版本。

        azure site set --php-version [5.4 | 5.5] {site-name}

3. PHP 版本現在已設定完成。 您可確認這些設定：

        azure site show {site-name}



## 作法：變更內建 PHP 組態

對於任一個內建的 PHP 執行階段，您可以遵循下列步驟，來變更任何組態選項。 (如需 php.ini 指示詞的詳細資訊，請參閱 php.ini 指示詞清單)。

### 變更 PHP\_INI\_USER，PHP\_INI\_PERDIR，PHP\_INI\_ALL 組態設定

1. 新增 [。.user.ini] 至根目錄的檔案。
2. 使用在 `php.ini` 檔案中使用的相同語法，將組態設定新增至 `.user.ini` 檔案。 例如，如果您要啟動 `display_errors` 設定，並將 `upload_max_filesize` 設定設為 10M，則 `.user.ini` 檔案將包含下列文字：

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

3. 部署 Web 應用程式。
4. 重新啟動 Web 應用程式。 (必須重新啟動，因為 PHP 讀取 `.user.ini` 檔案的頻率是由 `user_ini.cache_ttl` 設定所控制，這是系統層級設定，預設為 300 秒 (5 分鐘)。 重新啟動 web 應用程式將強制 PHP 讀取中的新設定 `。.user.ini` 檔案。)

除了使用 `。.user.ini` 檔案中，您可以使用 [ini_set ()] 函式指令碼中設定不是系統層級指示詞的組態選項。

### 變更 PHP\_INI\_SYSTEM 組態設定

1. 新增應用程式設定 Web 應用程式索引鍵 `PHP_INI_SCAN_DIR` 和值 `d:\home\site\ini`
2. 建立 `settings.ini` 檔案使用 Kudo 主控台 (http:// < 站台名稱 >。 scm.azurewebsite.net) 中 `d:\home\site\ini` 目錄。
3. 將組態設定，來新增 `settings.ini` 檔案中使用相同的語法會使用在 php.ini 檔案中。 例如，如果您想要指向 `curl.cainfo` 設為 [ `*.crt` 檔案，並將 'wincache.maxfilesize' 設定為 512k，您 `settings.ini` 檔案將包含下列文字:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512

4. 重新啟動 Web 應用程式以載入變更。

## 作法：在預設 PHP 執行階段中啟用擴充

上一節所述，若要查看預設 PHP 版本、 其預設組態及啟用的擴充，最好是部署呼叫 [phpinfo ()] 的指令碼。 若要啟用擴充，請依照下列步驟執行：

### 透過 ini 設定來設定

1. 新增 `ext` 目錄新增至 `d:\home\site` 目錄。
2. 將 `.dll` 延伸模組檔案中 `ext` 目錄 (例如， `php_mongo.dll` 和 `php_xdebug.dll`)。 確定擴充與 PHP 預設版本 (截至撰寫時為止，該版本為 PHP 5.4) 相容，而且與 VC9 及非安全執行緒 (nts) 相容。
3. 新增應用程式設定 Web 應用程式索引鍵 `PHP_INI_SCAN_DIR` 和值 `d:\home\site\ini`
4. 建立 `ini` 檔案中 `d:\home\site\ini` 呼叫 `extensions.ini`。
5. 將組態設定，來新增 `extensions.ini` 檔案中使用相同的語法會使用在 php.ini 檔案中。 例如，如果您想要啟用 MongoDB 和 XDebug 擴充，您 `extensions.ini` 檔案將包含下列文字:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll

6. 重新啟動 Web 應用程式以載入變更。

### 透過應用程式設定來設定

1. 新增 `bin` 目錄新增至根目錄。
2. 將 `.dll` 擴充檔放入 `bin` 目錄 (例如，`php_mongo.dll`)。 確定擴充與 PHP 預設版本 (截至撰寫時為止，該版本為 PHP 5.4) 相容，而且與 VC9 及非安全執行緒 (nts) 相容。
3. 部署 Web 應用程式。
4. 在 Azure 入口網站中瀏覽至您的 Web 應用程式，然後按一下 [設定]**** 按鈕。

    ![Web 應用程式設定][settings-button]

5. 從 [設定]**** 分頁中選取 [應用程式設定]****，然後捲動至 [應用程式設定]**** 區段。
6. 在 [應用程式設定]**** 區段中，建立 **PHP_EXTENSIONS** 索引鍵。 此索引鍵的值是相對於網站根目錄的路徑：**bin\your-ext-file**。

    ![啟用應用程式設定中的擴充][php-extensions]

7. 按一下 [Web 應用程式設定]**** 分頁頂端的 [儲存]**** 按鈕。

    ![儲存組態設定。][save-button]

Zend 擴充功能也支援使用 **PHP_ZENDEXTENSIONS** 索引鍵。 若要啟用多個擴充功能，包括以逗號分隔的清單 `.dll` 應用程式設定值的檔案。


## 做法：使用自訂 PHP 執行階段

除了預設的 PHP 執行階段之外，App Service Web Apps 也可以使用您提供的 PHP 執行階段來執行 PHP 指令碼。 您指定的執行階段可以由也是您提供的 `php.ini` 檔案加以設定。 若要使用自訂 PHP 執行階段搭配 Web Apps，請依照下列步驟執行。

1. 取得 PHP for Windows 的非安全執行緒 VC9 或 VC11 相容版本。 在這裡找到最新版 PHP for Windows: [http://windows.php.net/download/]。 在以下的封存可以找到舊版: [http://windows.php.net/downloads/releases/archives/]。
2. 為您的執行階段修改 `php.ini` 檔案。 請注意，Web Apps 將忽略僅系統層級指示詞的任何組態設定 (如需僅系統層級指示詞的詳細資訊，請參閱 php.ini 指示詞清單)。
3. 或者，將擴充新增至 PHP 執行階段，並且在 `php.ini` 檔案中啟用這些擴充。
4. 新增 `bin` 目錄新增至根目錄，並放入包含 PHP 執行階段中的目錄 (例如， `bin\php`)。
5. 部署 Web 應用程式。
4. 在 Azure 入口網站中瀏覽至您的 Web 應用程式，然後按一下 [設定]**** 按鈕。

    ![Web 應用程式設定][settings-button]

7. 從 [設定]**** 分頁中選取 [應用程式設定]****，然後捲動至 [處理常式對應]**** 區段。 新增 `*.php` 延伸至欄位，然後將路徑加入 `cgi.exe` 可執行檔。 如果您將 PHP 執行階段放入 `bin` 目錄中應用程式根目錄，該路徑將是 `D:\home\site\wwwroot\bin\php\php-cgi.exe`。

    ![指定處理常式對應中的處理常式][handler-mappings]

8. 按一下 [Web 應用程式設定]**** 分頁頂端的 [儲存]**** 按鈕。

    ![儲存組態設定。][save-button]

## 後續步驟

如需詳細資訊，請參閱 [PHP 開發人員中心](/develop/php/)。
>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

## 變更的項目

* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)


[free trial]: https://www.windowsazure.com/pricing/free-trial/ 
[phpinfo()]: http://php.net/manual/en/function.phpinfo.php 
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png 
[list of php.ini directives]: http://www.php.net/manual/en/ini.list.php 
[.user.ini]: http://www.php.net/manual/en/configuration.file.per-user.php 
[ini_set()]: http://www.php.net/manual/en/function.ini-set.php 
[application-settings]: ./media/web-sites-php-configure/application-settings.png 
[settings-button]: ./media/web-sites-php-configure/settings-button.png 
[save-button]: ./media/web-sites-php-configure/save-button.png 
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png 
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png 
[http://windows.php.net/download/]: http://windows.php.net/download/ 
[http://windows.php.net/downloads/releases/archives/]: http://windows.php.net/downloads/releases/archives/ 
[setphpvercli]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png 
[getphpvercli]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png 
[setphpverps]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png 
[getphpverps]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png 

