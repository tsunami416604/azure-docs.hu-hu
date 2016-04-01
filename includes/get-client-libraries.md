### 透過編輯器安裝

1. [安裝 Git][install-git]。 請注意在 Windows 中，您也必須將 Git 可執行檔新增至 PATH 環境變數。 

2. 建立名為 **composer.json** 根目錄中的專案並加入下列程式碼 ︰

    ```
    {
        "repositories": [
            {
                "type": "pear",
                "url": "http://pear.php.net"
            }
        ],
        "require": {
            "pear-pear.php.net/mail_mime" : "*",
            "pear-pear.php.net/http_request2" : "*",
            "pear-pear.php.net/mail_mimedecode" : "*",
            "microsoft/windowsazure": "*"
        }
    }
    ```

3. 下載 **[composer.phar][composer-phar]** 專案根目錄中。

4. 開啟命令提示字元，在專案根目錄中執行下列命令

    ```
    php composer.phar install
    ```

### 手動安裝

若要手動下載和安裝 PHP Client Libraries for Azure，請依照下列步驟進行：

> [AZURE.NOTE] Azure 的 PHP 用戶端程式庫有相依性 [HTTP_Request2](http://pear.php.net/package/HTTP_Request2), ，[Mail_mime](http://pear.php.net/package/Mail_mime), ，和 [Mail_mimeDecode](http://pear.php.net/package/Mail_mimeDecode) PEAR 封裝。 若要解決這些相依性的建議的方式是使用來安裝這些封裝 [PEAR 封裝管理員](http://pear.php.net/manual/en/installation.php)。
 
1. 下載包含的程式庫的.zip 封存檔 [GitHub][php-sdk-github]。 或者，分岔儲存機制並複製到本機電腦 後面這個選項需要有 GitHub 帳戶並在本機安裝 Git。
    
2. 將已下載之封存檔的 `WindowsAzure` 目錄複製到應用程式目錄結構中。

如需安裝 PHP Client Libraries for Azure （包括關於以 PEAR 封裝的資訊） 的詳細資訊，請參閱 [下載 Azure SDK for PHP][download-SDK-PHP]。

[php-sdk-github]: http://go.microsoft.com/fwlink/?LinkId=252719
[install-git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[download-SDK-PHP]: ../articles/php-download-sdk.md
[composer-phar]: http://getcomposer.org/composer.phar


