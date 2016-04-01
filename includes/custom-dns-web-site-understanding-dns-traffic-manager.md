網域名稱系統 (DNS) 是用來尋找網際網路上的資訊。 例如，當您在瀏覽器中輸入位址，或按一下網頁上的連結時，它會使用 DNS 將網域轉譯成 IP 位址。 IP 位址如同街道地址，但它不是很容易使用。 比方說，就能輕鬆記住的 DNS 名稱，例如 **contoso.com** 比記住如 192.168.1.88 或 2001:0:4137:1f67:24a2:3888:9cce:fea3 的 IP 位址。

DNS 系統根據 *記錄*。 記錄會關聯特定 *名稱*, ，例如 **contoso.com**, ，與 IP 位址或其他 DNS 名稱。 當應用程式 (如網頁瀏覽器) 查詢 DNS 中的名稱時，它會尋找記錄，並使用它指向為位址的任何值。 如果指向的值為 IP 位址，瀏覽器將使用該值。 如果指向其他 DNS 名稱，則應用程式必須重新解析。 所有名稱解析最終會導出一個 IP 位址。

建立 Azure 網站時，會將 DNS 名稱自動指派給網站。 此名稱將採用的形式 **& lt; e & gt;。azurewebsites.net**。 當您將網站新增為 Azure 流量管理員端點時，您的網站，即可以透過存取 **& lt; e & gt;。t** 網域。

> [AZURE.NOTE] 當您的網站設定為流量管理員端點時，您會使用 **。 trafficmanager.net** 位址建立 DNS 記錄時。

> 您只能搭配使用 CNAME 記錄與流量管理員

另有多種類型的記錄，每個都有自己的功能與限制，但對於設定為流量管理員端點的網站，我們只需要關注一種; *CNAME* 記錄。

###CNAME 或別名記錄

CNAME 記錄會對應 *特定* DNS 名稱，例如 **mail.contoso.com** 或 **www.contoso.com**, ，到其他 （正式） 網域名稱。 在 Azure 網站使用流量管理員中，正式網域名稱是 **& lt; p >。 trafficmanager.net** 流量管理員設定檔的網域名稱。 CNAME 建立之後，建立一個別名 **& lt; p >。 trafficmanager.net** 網域名稱。 CNAME 項目會解析為 IP 位址的您 **& lt; p >。 trafficmanager.net** 網域名稱，網站的 IP 位址變更時，如果您不需要採取任何動作。

一旦流量到達流量管理員，流量管理員接著會使用針對其設定的負載平衡方法，將流量路由到您的網站。 這對網站的訪客而言完全是透明的。 訪客只會在其瀏覽器中看到自訂的網域名稱。

> [AZURE.NOTE] 某些網域註冊機構只允許您對應子網域，當使用 CNAME 記錄，例如 **www.contoso.com**, ，而不是根名稱，例如 **contoso.com**。 如需 CNAME 記錄的詳細資訊，請參閱註冊機構提供的文件、<a href="http://en.wikipedia.org/wiki/CNAME_record">維基百科 CNAME 記錄條目</a>，或 <a href="http://tools.ietf.org/html/rfc1035">IETF 網域名稱 - 實作與規格</a>文件。


