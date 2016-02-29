<properties
    pageTitle="在 Azure App Service 中設定自訂網域名稱"
    description="了解如何在 Azure App Service 中將自訂網域名稱與 Web 應用程式搭配使用。"
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor="jimbe"
    tags="top-support-issue"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/23/2015"
    ms.author="cephalin"/>

# 在 Azure App Service 中設定自訂網域名稱

> [AZURE.SELECTOR]
- [購買網域的 Web 應用程式](custom-dns-web-site-buydomains-web-app.md)
- [內含外部網域的 web 應用程式](web-sites-custom-domain-name.md)
- [包含流量管理員的 Web 應用程式](web-sites-traffic-manager-custom-domain-name.md)
- [GoDaddy](web-sites-godaddy-custom-domain-name.md)

當您建立 Web 應用程式時，Azure 會將它指派給 azurewebsites.net 的子網域。 例如，如果您的 web 應用程式的名稱為 **contoso**, ，URL 是 **contoso.azurewebsites.net**。 Azure 也會指派虛擬 IP 位址。

若是實際執行的 Web 應用程式，您可能想讓使用者看到自訂網域名稱。 這篇文章說明如何設定自訂網域名稱與 [App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714)。

如果您需要更多協助，在本文中的任何時間點，您可以與 Azure 專家 [MSDN Azure 和堆疊溢位論壇](http://azure.microsoft.com/support/forums/)。 或者，您也可以提出 Azure 支援事件。 移至 [Azure 支援網站](http://azure.microsoft.com/support/options/) ，然後按一下 **取得支援**。

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]

## 概觀

如果您還沒有註冊的外部網域名稱 (不是 *。 azurewebsites.net)，設定自訂網域的最簡單方式是直接在購買 [Azure 入口網站](https://portal.azure.com)。 這個程序可讓您直接在入口網站中管理您的 Web 應用程式的網域名稱，而不必到 GoDaddy 等第三方網站管理名稱。 同樣地，在您的 web 應用程式中設定的網域名稱已大幅簡化，不論您的 web 應用程式使用 [Azure 流量管理員](web-sites-traffic-manager-custom-domain-name.md) 與否。 如需詳細資訊，請參閱 [購買，並在 Azure App Service 中設定自訂網域名稱](custom-dns-web-site-buydomains-web-app.md)。

如果您已經是網域名稱，或您想要保留網域從其他網域註冊機構，以下是將 web 應用程式的自訂網域名稱的一般步驟 (請參閱 [godaddy.com 的特定指示](web-sites-godaddy-custom-domain-name.md)):

1. 保留您的網域名稱。 本文不包含此流程。 有許多網域註冊機構可供選擇。 當您註冊時，這些網站會逐步引導您進行此流程。
1. 建立 DNS 記錄，將網域對應至您的 Azure Web 應用程式。
1. 將網域名稱新增 [Azure 入口網站](https://portal.azure.com)。

在這基本大綱中，有一些需要注意的特定情況：

- 對應根網域。 根網域是您透過網域註冊機構保留的網域。 例如， **contoso.com**。
- 對應子網域。 例如， **blogs.contoso.com**。  您可以將不同的子網域對應至不同的 Web 應用程式。
- 對應萬用字元。 例如， **\*.contoso.com**。 萬用字元項目會套用至網域的所有子網域。

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-modes.md)]


## DNS 記錄類型

網域名稱系統 (DNS) 使用資料記錄，將網域名稱對應至 IP 位址。 有數種 DNS 記錄。 針對 web 應用程式，您要建立 *的* 記錄或 *CNAME* 記錄。

- A **(位址)** 記錄會對應至 IP 位址的網域名稱。
- A **CNAME (正式名稱)** 記錄會對應至另一個網域名稱的網域名稱。 DNS 會使用第二個名稱查詢位址。 使用者仍然可以在瀏覽器中看到第一個網域名稱。 例如，您可以將 contoso.com 對應至 *& lt; yourwebapp & gt;*。azurewebsites.net。

若 IP 位址變更，CNAME 項目仍會有效，但 A 記錄就必須更新。 不過，有部分網域註冊機構不允許 CNAME 記錄使用根網域或萬用字元網域。 若有該情況，則必須使用 A 記錄。

> [AZURE.NOTE] IP 位址可能會變更，如果您刪除並重新建立您的 web 應用程式，或變更 web 應用程式模式後釋放。


## 尋找虛擬 IP 位址

若您要建立 CNAME 記錄，可略過此步驟。 若要建立 A 記錄，您需要 Web 應用程式的虛擬 IP 位址。 取得 IP 位址：

1.  在瀏覽器中開啟 [Azure 入口網站](https://portal.azure.com)。
2.  按一下 [ **瀏覽** 左邊頁面的選項。
3.  按一下 [ **Web 應用程式** 刀鋒視窗。
4.  按一下您的 Web 應用程式的名稱。
5.  在 **Essentials** 頁面上，按一下 **所有設定**。
6.  按一下 [ **自訂網域和 SSL**。
7.  在 **自訂網域和 SSL** 刀鋒視窗中，按一下 [ **帶出外部網域 」**。 IP 位址位於此部分的底部。

## 建立 DNS 記錄

登入網域註冊機構，並使用它們的工具加入 A 記錄或 CNAME 記錄。 每個註冊機構的 Web 應用程式都稍有不同，但以下是部分一般方針。

1.  尋找管理 DNS 記錄的頁面。 尋找標示為網站的連結或區域 **網域名稱**, ，**DNS**, ，或 **Name Server Management**。 通常可以找到的連結檢視您的帳戶資訊，例如尋找連結 **我的網域**。
2.  當您找到管理頁面時，請尋找可讓您加入或編輯 DNS 記錄的連結。 這可能會列為 **區域檔案**, ，**DNS 記錄**, ，或為 **進階** 設定連結。

頁面可能會分別列出 A 記錄與 CNAME 記錄，或提供選取記錄類型的下拉式清單。 此外，也可能使用其他名稱做為記錄類型，例如 **IP 位址記錄** 而不是記錄，或 **別名記錄** 而不是 CNAME 記錄。  通常註冊機構建立一些記錄，所以可能已經有記錄根網域或一般子網域，例如 **www**。

當您建立或編輯記錄時，可使用欄位將網域名稱對應至 IP 位址 (針對 A 記錄)，或對應至其他網域 (針對 CNAME 記錄)。 對於 CNAME 記錄，您會將對應 *從* 您的自訂網域 *至* azurewebsites.net 子網域。

在許多註冊機構的工具中，您只要輸入網域的子網域部分即可，不必輸入整個網域名稱。 此外，許多工具都會使用‘@’表示根網域。 例如：

<table cellspacing="0" border="1">
  <tr>
    <th>主機</th>
    <th>記錄類型</th>
    <th>IP 位址或 URL</th>
  </tr>
  <tr>
    <td>@</td>
    <td>A (位址)</td>
    <td>168.62.48.183</td>
  </tr>
  <tr>
    <td>www</td>
    <td>CNAME (別名)</td>
    <td>contoso.azurewebsites.net</td>
  </tr>
</table>

假設自訂網域名稱為 'contoso.com'，則會建立以下記錄：

- **contoso.com** 對應至 168.62.48.183。
- **www.contoso.com** 對應至 **contoso.azurewebsites.net**。

>[AZURE.NOTE] 您可以使用 Azure DNS 來裝載 web 應用程式所需的網域記錄。 若要設定您的自訂網域，並在 Azure DNS 中建立您的記錄，請參閱 [建立 web 應用程式的自訂 DNS 記錄](../dns-web-sites-custom-domain)。

<a name="awverify" />
## 建立 awverify 記錄 (僅限 A 記錄)

若您建立 A 記錄，Web 應用程式也會要求特定的 CNAME 記錄，用於確認您是否擁有嘗試使用的網域。 此 CNAME 記錄必須具有下列形式。

- *如果 A 記錄對應根網域或萬用字元網域:* 建立 CNAME 記錄對應從 **awverify.lt; 您的網域 & gt;** 至 **awverify.lt; yourwebappname & gt;。azurewebsites.net**。  例如，如果 A 記錄屬於 **contoso.com**, ，建立 CNAME 記錄 **awverify.contoso.com**。
- *如果 A 記錄對應特定子網域:* 建立 CNAME 記錄對應從 **awverify.lt; 子網域 & gt;** 至 **awverify.lt; yourwebappname & gt;。azurewebsites.net**。 例如，如果 A 記錄屬於 **blogs.contoso.com**, ，建立 CNAME 記錄 **awverify.blogs.contoso.com**。

您 Web 應用程式的訪客將不會看到 awverify 子網域；這只是供 Azure 用於驗證您的網域。

## 在 Web 應用程式上啟用網域名稱

[AZURE.INCLUDE [modes](../../includes/custom-dns-web-site-enable-on-web-site.md)]

>[AZURE.NOTE] 如果您想要註冊 Azure 帳戶前開始使用 Azure App Service，請移至 [試用 App Service](http://go.microsoft.com/fwlink/?LinkId=523751), ，您可以立即建立短期入門 web 應用程式的應用程式服務中。 不需要信用卡；沒有承諾。

## 確認 DNS 傳播

完成設定步驟之後，可能需要一些時間來傳播變更，視您的 DNS 提供者而定。 您可以確認 DNS 傳播正如預期般使用 [http://digwebinterface.com/](http://digwebinterface.com/)。 您瀏覽至網站之後，請在文字方塊中按一下 [指定主機名稱 **挖掘**。 查看結果以確認最近的變更是否生效。  

![](./media/web-sites-custom-domain-name/1-digwebinterface.png)

> [AZURE.NOTE] DNS 項目傳用的時間最多 48 小時 (有時再)。 如果您已正確設定所有項目，仍然需要等待傳播成功。

## 後續步驟

如需詳細資訊請參閱: [開始使用 Azure DNS](../dns/dns-getstarted-create-dnszone.md) 和 [網域委派給 Azure DNS](../dns/dns-domain-delegation.md)

## 變更的項目
* 如需變更從應用程式服務的網站的指南，請參閱: [Azure App Service，及其對現有 Azure 服務的影響](http://go.microsoft.com/fwlink/?LinkId=529714)

<!-- Anchors. -->
[Overview]: #overview
[DNS record types]: #dns-record-types
[Find the virtual IP address]: #find-the-virtual-ip-address
[Create the DNS records]: #create-the-dns-records
[Enable the domain name on your web app]: #enable-the-domain-name-on-your-web-app

<!-- Images -->
[subdomain]: media/web-sites-custom-domain-name/azurewebsites-subdomain.png

