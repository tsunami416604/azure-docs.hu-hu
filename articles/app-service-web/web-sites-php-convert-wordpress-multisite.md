<properties 
    pageTitle="在 Azure App Service 中將 WordPress 轉換成多網站" 
    description="了解如何將透過 Azure 中的組件庫所建立的現有 WordPress Web 應用程式轉換成 WordPress 多網站" 
    services="app-service\web" 
    documentationCenter="php" 
    authors="tfitzmac" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="PHP" 
    ms.topic="article" 
    ms.date="10/01/2015" 
    ms.author="tomfitz"/>



# 在 Azure App Service 中將 WordPress 轉換成多網站

## 概觀

*由 [Ben Lobaugh][ben-lobaugh], ，[Microsoft Open Technologies Inc.][ms-open-tech]*

在本教學課程中，您將了解如何將透過 Azure 的組件庫所建立的現有 WordPress Web 應用程式轉換成 WordPress 多網站安裝。 此外，您也將了解如何將自訂網域指派給安裝內的每一個子網站。

本文假設您目前已安裝 WordPress。 如果不這麼做，請遵循本指南提供的 [從 Azure 中的組件庫建立 WordPress 網站][website-from-gallery]。

轉換現有的 WordPress 單一網站安裝成多網站通常相當簡單，而且許多初始步驟都直接從 [建立網路][wordpress-codex-create-a-network] 頁面 [WordPress Codex](http://codex.wordpress.org)。

讓我們開始吧！

## 允許多網站

您必須先啟用多網站 `wp-config.php` 檔案中使用 **WP\_ALLOW\_MULTISITE** 常數。 有兩種方法可以編輯您的 Web 應用程式檔案：第一種是透過 FTP，第二種是透過 Git。 如果不熟悉如何設定這些方法，請參閱下列教學課程：

* [PHP 網站與 MySQL 和 FTP][website-w-mysql-and-ftp-ftp-setup]

* [PHP 網站與 MySQL 和 Git][website-w-mysql-and-git-git-setup]

使用您選擇的編輯器開啟 `wp-config.php` 檔案，然後在 `/* That's all, stop editing! Happy blogging. */` 那一行上方新增下列程式碼。

    /* Multisite */

    define( 'WP_ALLOW_MULTISITE', true );

記得儲存檔並重新傳送至伺服器！

## 網路設定

登入 *wp-admin* 區域中的 web 應用程式，您應該會看到新的項目底下 **工具** 稱為 menu **網路安裝**。 按一下 [ **Network Setup** ，填寫網路的詳細資料。

![Network Setup Screen][wordpress-network-setup]

本教學課程使用 *子目錄* 網站結構描述，因為應該一律可行，我們將會設定每一個子網站的自訂網域稍後在教學課程。 不過，應該就能的設定子網域安裝您透過將網域對應至 [Azure 入口網站](https://portal.azure.com) 並適當設定萬用字元 DNS。

如需有關子網域和子目錄的設定，請參閱 [類型的多站台網路][wordpress-codex-types-of-networks] WordPress Codex 上的文件。

## 啟用網路

資料庫中現在已設定網路，還差一步就能啟用網路功能。 請完成 `wp-config.php` 設定，並確定 `web.config` 可適當地路由傳送每一個網站。


按一下後 **安裝** 按鈕 *Network Setup* ] 頁面上，WordPress 將嘗試更新 `wp-config.php` 和 `web.config` 檔案。 不過，一定要檢查檔案，以確定更新成功。 如果失敗，此畫面會顯示必要的更新。 編輯並儲存檔案。


進行這些更新之後，您需要先登出，然後再登入回到 wp-admin 儀表板。

標示為管理列上現在應該有額外的功能表 **我的網站**。 此功能表可讓您控制透過新的網路 **Network Admin** 儀表板。

## 新增自訂網域

 [WordPress MU Domain Mapping][wordpress-plugin-wordpress-mu-domain-mapping] 外掛程式可讓您輕鬆地將自訂網域新增至您的網路中的任何網站。 為了讓外掛程式正常運作，您需要在入口網站上執行其他一些設定，也需要在網域註冊機構上這樣做。

## 允許將網域對應至 Web 應用程式

 **免費** [應用程式服務](http://go.microsoft.com/fwlink/?LinkId=529714) 計劃模式不支援將自訂網域新增至 Web 應用程式。 您必須切換至 **共用** 或 **標準** 模式。 作法：

* 登入 Azure 入口網站，並找出您的 Web 應用程式。 
* 按一下 [ **延展** 主要內容區域中的索引標籤
* 在 **一般**, ，選取 [ *共用* 或 *標準*
* 按一下 [ **儲存**

視使用量和您設定的其他設定而定，可能會出現訊息要求您確認變更，並認可 Web 應用程式現在可能會引發成本。

需要一些時間來處理新的設定，這段時間剛好可拿來開始設定您的網域。

## 驗證網域

您必須先確認擁有對應網域的權限，Azure Web Apps 才會允許您將該網域對應至網站。 在作法上，您必須將新的 CNAME 記錄加入至 DNS 項目。

* 登入網域的 DNS 管理員
* 建立新的 CNAME *awverify*
* 點 *awverify* 至 *awverify。YOUR_DOMAIN.azurewebsites.net*

DNS 變更需要一些時間才會完全生效，如果無法立即執行下列步驟，請休息一下，稍後再回來重試。

## 將網域加入至 Web 應用程式

返回您的 web 應用程式，透過 Azure 入口網站，請按一下 **設定**, ，然後按一下 [ **自訂網域和 SSL**。

當 *SSL 設定* 會顯示，您會看到您要在此輸入您想要指派給您的 web 應用程式的所有網域的欄位。 如果其中未列出某個網域，就表示不論網域 DNS 如何設定，都無法在 WordPress 內對應此網域。

![Manage custom domains dialog][wordpress-manage-domains]

在文字方塊中輸入網域之後，Azure 會驗證您先前建立的 CNAME 記錄。 如果 DNS 未完整傳播，則會出現紅色指標。 如果成功，則會出現綠色勾選記號。 

請記下對話方塊底部列出的 IP 位址。 設定網域的 A 記錄時需要此資訊。

## 設定網域 A 記錄

如果其他步驟成功，則現在可透過 DNS A 記錄，將網域指派給 Azure Web 應用程式。 

請務必注意，Azure web 應用程式接受 CNAME 與 A 記錄，不過您 *必須* 使用 A 記錄才能適當地對應網域。 CNAME 無法轉送至另一個 CNAME，即 Azure 以 YOUR_DOMAIN.azurewebsites.net 為您建立的記錄。

回到 DNS 管理員，設定 A 記錄來指向您在上一步記下的 IP 位址。


## 安裝和設定外掛程式

WordPress 多網站目前沒有內建的方法可對應自訂網域。 不過，會呼叫外掛程式 [WordPress MU Domain Mapping][wordpress-plugin-wordpress-mu-domain-mapping] ，為您增加此功能。 登入網站的 [Network Admin] 部分，並安裝 **WordPress MU Domain Mapping** 外掛程式。

安裝啟動此外掛程式後，請瀏覽 **設定** > **Domain Mapping** 來設定外掛程式。 在第一個文字方塊中， *伺服器 IP 位址*, ，輸入您用來設定網域的 A 記錄的 IP 位址。 設定任何 *網域選項* 您想要 （預設值是通常沒問題），然後按一下 **儲存**。

## 對應網域

請瀏覽 **儀表板** 您想要讓網域對應到站台。 按一下 [ **工具** > **Domain Mapping** 輸入到文字方塊中，然後按一下新的網域和 **新增**。

依預設，新的網域會改寫為自動產生的網站網域。 如果您想要讓所有流量都傳送至新網域中，核取 *這篇部落格的主要網域* 儲存之前的方塊。 無限的數量的網域加入站台，但只能有一個主網域。

## 再做一次

Azure Web Apps 可讓您將不限數量的網域加入至 Web 應用程式。 若要新增另一個網域，您需要執行 **驗證您的網域** 和 **設定網域 A 記錄** 區段為每個網域。  

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；無需承諾。

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱 ︰ [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)

[ben-lobaugh]: http://ben.lobaugh.net
[ms-open-tech]: http://msopentech.com
[website-from-gallery]: https://www.windowsazure.com/develop/php/tutorials/website-from-gallery/
[wordpress-codex-create-a-network]: http://codex.wordpress.org/Create_A_Network
[website-w-mysql-and-ftp-ftp-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-ftp/#header-0
[website-w-mysql-and-git-git-setup]: https://www.windowsazure.com/develop/php/tutorials/website-w-mysql-and-git/#header-1
[wordpress-network-setup]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-network-setup.png
[wordpress-codex-types-of-networks]: http://codex.wordpress.org/Before_You_Create_A_Network#Types_of_multisite_network
[wordpress-plugin-wordpress-mu-domain-mapping]: http://wordpress.org/extend/plugins/wordpress-mu-domain-mapping/

[wordpress-manage-domains]: ./media/web-sites-php-convert-wordpress-multisite/wordpress-manage-domains.png

 


