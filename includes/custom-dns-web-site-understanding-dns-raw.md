網域名稱系統 (DNS) 主要用來尋找網際網路上的資源。 例如，當您在瀏覽器中輸入 Web 應用程式位址，或按一下網頁上的連結時，它會使用 DNS 將網域轉譯成 IP 位址。 IP 位址如同街道地址，但它不是很容易使用。 比方說，就能輕鬆記住的 DNS 名稱，例如 **contoso.com** 比記住如 192.168.1.88 或 2001:0:4137:1f67:24a2:3888:9cce:fea3 的 IP 位址。

DNS 系統根據 *記錄*。 記錄會關聯特定 *名稱*, ，例如 **contoso.com**, ，與 IP 位址或其他 DNS 名稱。 當應用程式 (如網頁瀏覽器) 查詢 DNS 中的名稱時，它會尋找記錄，並使用它指向為位址的任何值。 如果指向的值為 IP 位址，瀏覽器將使用該值。 如果指向其他 DNS 名稱，則應用程式必須重新解析。 所有名稱解析最終會導出一個 IP 位址。

當您在 App Service 中建立 Web 應用程式時，會將 DNS 名稱自動指派給 Web 應用程式。 此名稱將採用的形式 **& lt; yourwebappname & gt;。azurewebsites.net**。 另外還有一個虛擬 IP 位址可供使用時建立 DNS 記錄，因此您可以建立指向的記錄 **。 azurewebsites.net**, ，或指向 IP 位址。

> [AZURE.NOTE] Web 應用程式的 IP 位址會變更，如果您刪除並重新建立您的 web 應用程式，或將應用程式服務計劃模式變更為 **免費** 設為之後 **基本**, ，**共用**, ，或 **標準**。

另有多種類型的記錄，每個都有自己的功能與限制，但 web 應用程式只需要關注兩種， *A* 和 *CNAME* 記錄。

###位址記錄 (A 記錄)

A 記錄將網域對應，例如 **contoso.com** 或 **www.contoso.com**, ，*或萬用字元網域* 例如 **\*.contoso.com**, ，IP 位址。 以 App Service 中的 Web 應用程式而言，就是指服務的虛擬 IP 或您為 Web 應用程式購買的特定 IP 位址。

相較於 CNAME 記錄，A 記錄的主要優點為：

* 您可以對應根網域，例如 **contoso.com** 至 IP 位址; 許多註冊機報僅允許使用此 A 記錄

* 您可以有一個項目使用萬用字元，例如 **\*.contoso.com**, ，這會處理多個子網域的要求，例如 **mail.contoso.com**, ，**blogs.contoso.com**, ，或 **www.contso.com**。

> [AZURE.NOTE] 因為 A 記錄會對應至靜態 IP 位址，所以無法自動解析變更 web 應用程式的 IP 位址。 設定 web 應用程式; 的自訂網域名稱設定時會提供搭配 A 記錄的 IP 位址不過，這個值可能會變更，如果您刪除和重新建立您的 web 應用程式，或將應用程式服務計劃模式變更為回 **免費**。

###別名記錄 (CNAME 記錄)

CNAME 記錄會對應 *特定* DNS 名稱，例如 **mail.contoso.com** 或 **www.contoso.com**, ，到其他 (正式) 網域名稱。 在應用程式服務 Web 應用程式，正式網域名稱是 **& lt; yourwebappname >。 azurewebsites.net** web 應用程式的網域名稱。 CNAME 建立之後，建立一個別名 **& l t; yourwebappname >。 azurewebsites.net** 網域名稱。 CNAME 項目會解析為 IP 位址的您 **& l t; yourwebappname >。 azurewebsites.net** 網域名稱，如果 web 應用程式的 IP 位址變更，您不需要採取任何動作。

> [AZURE.NOTE] 某些網域註冊機構只允許您對應子網域，當使用 CNAME 記錄，例如 **www.contoso.com**, ，而不是根名稱，例如 **contoso.com**。 如需 CNAME 記錄的詳細資訊，請參閱註冊機構提供的文件、<a href="http://en.wikipedia.org/wiki/CNAME_record">維基百科 CNAME 記錄條目</a>，或 <a href="http://tools.ietf.org/html/rfc1035">IETF 網域名稱 - 實作與規格</a>文件。

###Web 應用程式 DNS 詳細規格

若要搭配 Web 應用程式使用 A 記錄，您必須先建立下列其中一筆 CNAME 記錄：

* **若是根網域或萬用字元子網域** -的 DNS 名稱 **awverify** 至  **awverify.lt; yourwebappname & gt;。azurewebsites.net**。

* **對於特定子網域** -的 DNS 名稱 **awverify.& l t; 子網域 >** 至 **awverify.lt; yourwebappname & gt;。azurewebsites.net**。 例如， **awverify.blogs** 如果 A 記錄屬於 **blogs.contoso.com**。

此 CNAME 記錄可用來驗證您擁有正在嘗試使用的網域。 這是建立指向 Web 應用程式之虛擬 IP 位址的 A 記錄以外的動作。

您可以找到的 IP 位址，並將 **awverify** 名稱和 **。 azurewebsites.net** 名稱 web 應用程式執行下列步驟:

1. 在瀏覽器中開啟 [Azure 入口網站](https://portal.azure.com)。

2. 在 **Web 應用程式** 刀鋒視窗中，按一下 [web 應用程式的名稱，然後選取 **所有設定**, ，然後選取 **自訂網域和 SSL** 從頁面底部。

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. 在 **自訂網域和 SSL** 刀鋒視窗中，按一下 [ **讓外部網域**。

    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

    > [AZURE.NOTE] 您無法使用的自訂網域名稱 **免費** web 應用程式，且必須升級的應用程式服務計劃 **共用**, ，**基本**, ，**標準**, ，或 **高階** 層。 如需有關應用程式服務方案的價格層，包括如何變更定價層的 web 應用程式，請參閱 [如何調整 web 應用程式](../articles/web-sites-scale.md)。

6. 在 **讓外部網域** 刀鋒視窗中，您會看到 **awverify** 資訊、 目前指派 **。 azurewebsites.net** 網域名稱，以及虛擬 IP 位址。 儲存此資訊，在建立 DNS 記錄時會用得到。

    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)

