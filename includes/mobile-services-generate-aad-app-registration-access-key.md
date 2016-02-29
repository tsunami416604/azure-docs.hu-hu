1. 按一下 [ **應用程式** 目錄頁面上的索引標籤 [Azure 傳統入口網站](https://manage.windowsazure.com/)。
  
2. 按一下整合的應用程式登錄。

3. 按一下 [ **設定** 上的應用程式頁面和向下的捲動 **金鑰** 頁面區段。 
4. 按一下 [ **1 年** 針對新金鑰的持續時間。 然後按一下 [ **儲存** ，入口網站會顯示新的金鑰值。
5. 複製 **用戶端識別碼** 和 **金鑰** 儲存後顯示。 請注意，儲存後金鑰值只會顯示一次。 

    ![](./media/mobile-services-generate-aad-app-registration-access-key/client-id-and-key.png)

6. 捲動到 [整合式應用程式組態] 頁面的底部，並啟用 **讀取目錄資料** 權限的應用程式，按一下 **儲存**。

    ![](./media/mobile-services-generate-aad-app-registration-access-key/app-perms.png)


7. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，瀏覽回到您的行動服務，然後按一下 **設定** ] 索引標籤。 向下捲動至 **應用程式設定** 區段並新增下列應用程式設定，然後按一下 **儲存**。 

    <table border="1">
    <tr>
    <th>應用程式設定名稱</th><th>說明</th>
    </tr>
    <tr>
    <td>AAD_CLIENT_ID</td><td>在上述步驟中，從整合應用程式中複製的用戶端識別碼。</td>
    </tr>
    <tr>
    <td>AAD_CLIENT_KEY</td><td>在上述步驟中，AAD 整合應用程式中產生的應用程式金鑰。</td>
    </tr>
    <tr>
    <td>AAD_TENANT_DOMAIN</td><td>AAD 網域名稱。 應類似 "mydomain.onmicrosoft.com"</td>
    </tr>
    </table><br/>

 
    ![](./media/mobile-services-generate-aad-app-registration-access-key/aad-app-settings.png)
  
