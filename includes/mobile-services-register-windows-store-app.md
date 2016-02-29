
1. 如果您尚未註冊您的應用程式，請瀏覽 [提交應用程式頁面]，Windows 市集應用程式之開發人員中心，使用您的 Microsoft 帳戶登入，然後按一下 **應用程式名稱**。

    ![](./media/mobile-services-register-windows-store-app/mobile-services-submit-win8-app.png)

2. 選取 **保留唯一名稱，以建立新的應用程式** 按一下 **繼續**, ，然後輸入您的應用程式的名稱 **應用程式名稱**, ，按一下 [ **保留應用程式名稱**, ，然後按一下 [ **儲存**。

    ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-name.png)

    This creates a new Windows Store registration for your app.

3. 在 Visual Studio 中，開啟您完成教學課程時所建立的專案 [開始使用行動服務]。

4. 在 [方案總管中以滑鼠右鍵按一下 Windows 市集應用程式專案中，按一下 **存放區**, ，然後按一下 [ **將應用程式與存放區 …**。 

    ![](./media/mobile-services-register-windows-store-app/mobile-services-store-association.png)

    這會顯示 **關聯您的應用程式與 Windows 市集** 精靈。

5. 在精靈中，按一下 [ **登入** ，然後使用您的 Microsoft 帳戶登入選取的應用程式，您在步驟 2 中註冊按 **下一步**, ，然後按一下 [ **關聯**。

    這會將所需的 Windows 市集註冊資訊新增至應用程式資訊清單。   

6. (選擇性) 若為 Windows 通用 app，請針對 Windows Phone 市集專案重複執行步驟 4 與 5。 

6. 回到新應用程式的 Windows 開發人員中心] 頁面上，按一下 [ **服務**。 

    ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit-app.png) 

7. 在 [服務] 頁面中，按一下 [ **Live 服務網站** 下 **Azure 行動服務**。

    ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-edit2-app.png) 

8. 按一下 [ **API 設定**, ，選取 [啟用 **行動或桌面用戶端應用程式**, ，提供行動服務 URL 當成 **目標網域**, ，提供的值 `https://<mobile_service>.azure-mobile.net/login/microsoftaccount/` 中 **重新導向 URL**, ，然後按一下 [ **儲存**。

    ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth-2.png)

9. 在 **應用程式設定**, ，請記下的值 **用戶端識別碼**, ，**用戶端密碼**, ，和 **封裝安全性識別碼 (SID)**。 

    ![](./media/mobile-services-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    >[AZURE.NOTE]The client secret and package SID are important security credentials. 請勿與任何人共用這些密碼，或與您的應用程式一起散發密碼。

10. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [ **行動電話服務**, ，然後按一下您的應用程式。

11. 按一下 [ **識別** 索引標籤上，輸入 **用戶端密碼** 和 **封裝 SID** 步驟 4 中從 WNS 取得的值，，然後按一下 [ **儲存**。

    ![](./media/mobile-services-register-windows-store-app/mobile-push-tab.png)

13. 按一下 [ **識別** ] 索引標籤。 請注意 **用戶端密碼** 和 **封裝 SID** 值已設定上一個步驟。 輸入 **用戶端識別碼** 您先前所做的請注意，然後按一下 [ **儲存**。

    ![](./media/mobile-services-register-windows-store-app/mobile-services-identity-tab.png)
 
現在，您已可在應用程式中使用 Microsoft 帳戶進行驗證。  

<!-- Anchors. -->

<!-- Images. -->
 

<!-- URLs. -->
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582

