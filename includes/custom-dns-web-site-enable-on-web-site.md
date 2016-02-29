在網域名稱的記錄傳播完成後，您必須將這些記錄與 Web 應用程式產生關聯。 利用下列步驟，使用網頁瀏覽器啟用網域名稱。

> [AZURE.NOTE] 它可能需要一些時間才能傳播至整個 DNS 系統先前步驟中建立 CNAME 記錄。 完成 CNAME 傳播前，您無法將網域名稱新增至 Web 應用程式。 如果您使用 A 記錄，不能將 A 記錄網域名稱加入至您的 web 應用程式，直到 **awverify** 先前步驟中建立的 CNAME 記錄傳播完成之後。
>
> 您可以使用服務例如 <a href="http://www.digwebinterface.com/">http://www.digwebinterface.com/</a> 若要確認 CNAME 可用。

1. 在瀏覽器中開啟 [Azure 管理入口網站](https://portal.azure.com)。

2. 在 **Web 應用程式** 索引標籤上，按一下 web 應用程式，然後選取名稱 **設定**, ，然後選取 **自訂網域和 SSL**

    ![](./media/custom-dns-web-site/dncmntask-cname-6.png)

3. 在 **自訂網域和 SSL** 刀鋒視窗中，按一下 [ **讓外部網域**。

    ![](./media/custom-dns-web-site/dncmntask-cname-7.png)

4. 使用 **網域名稱** 文字方塊輸入此 web 應用程式關聯的網域名稱。

    ![](./media/custom-dns-web-site/dncmntask-cname-8.png)

5. 按一下 [ **儲存** 來儲存網域名稱設定。

    當組態完成之後時，自訂網域名稱會列示在 **網域名稱** web 應用程式的一節。

此時，您應該能夠在瀏覽器中輸入自訂網域名稱，並且能成功移至您的 Web 應用程式。

