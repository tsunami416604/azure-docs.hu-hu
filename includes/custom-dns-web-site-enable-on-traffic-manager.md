傳播網域名稱的記錄後，您應該要能夠使用瀏覽器來確認您自訂的網域名稱可用來存取 Azure App Service 中的 Web 應用程式。

> [AZURE.NOTE] 可能需要一些時間，CNAME 才能傳播至整個 DNS 系統。 您可以使用 <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> 之類的服務來驗證 CNAME 已生效。

若尚未新增 Web 應用程式作為流量管理員端點，則必須先執行此作業，名稱解析才能正常運作，因為自訂網域名稱會路由至流量管理員。 接著會將流量管理員路由至您的 Web 應用程式。 使用中的資訊 [新增或刪除端點](../traffic-manager/traffic-manager-endpoints.md) 將 web 應用程式新增為流量管理員設定檔中的端點。

> [AZURE.NOTE] 如果未列出您的 web 應用程式，新增端點時，請確認已將它設定為 **標準** App Service 方案模式。 您必須使用 **標準** web 應用程式才能與流量管理員搭配使用的模式。

1. 在瀏覽器中開啟 [Azure 入口網站](https://portal.azure.com)。

2. 在 **Web 應用程式** 索引標籤上，按一下 web 應用程式，然後選取名稱 **設定**, ，然後選取 **自訂網域和 SSL**

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. 在 **自訂網域和 SSL** 刀鋒視窗中，按一下 [ **讓外部網域**。

    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4. 使用 **網域名稱** 文字方塊來輸入要與此 web 應用程式產生關聯的流量管理員網域名稱 (contoso.trafficmanager.net)。

    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)

5. 按一下 [ **儲存** 來儲存網域名稱設定。

    當組態完成之後時，自訂網域名稱會列示在 **網域名稱** web 應用程式的一節。

此時，您應該能夠在瀏覽器中輸入流量管理員網域名稱 (contoso.trafficmanager.net)，並且能成功移至您的 Web 應用程式。


