

1. 如果您尚未註冊您的應用程式，瀏覽至 [Submit an app page] ，Windows 市集應用程式之開發人員中心，使用您的 Microsoft 帳戶登入，然後按一下 **應用程式名稱**。

    ![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-submit-win8-app.png)

2. 輸入您的應用程式的名稱 **應用程式名稱**, ，按一下 [ **保留應用程式名稱**, ，然後按一下 [ **儲存**。

    ![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-name.png)

    This creates a new Windows Store registration for your app.

3. 在 Visual Studio 中，開啟您完成教學課程時所建立的專案 **開始使用行動服務**。

4. 在 [方案總管中以滑鼠右鍵按一下專案，請按一下 **存放區**, ，然後按一下 [ **將應用程式與存放區 …**。 

    ![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-store-association.png)

    這會顯示 **關聯您的應用程式與 Windows 市集** 精靈。

5. 在精靈中，按一下 [ **登入** ，然後使用您的 Microsoft 帳戶登入。

6. 選取您在步驟 2 中註冊的應用程式中，按一下 **下一步**, ，然後按一下 [ **關聯**。

    ![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-select-app-name.png)

    這會將所需的 Windows 市集註冊資訊新增至應用程式資訊清單。    

7. (選用) 重複執行步驟 4-6，也會註冊通用 Windows 應用程式的 Windows Phone 市集專案。

8. 回到新應用程式的 Windows 開發人員中心] 頁面上，按一下 [ **服務**。 

    ![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-edit-app.png) 

9. 在 [服務] 頁面中，按一下 [ **Live 服務網站** 下 **Azure 行動服務**。

    ![](./media/mobile-services-javascript-backend-register-windows-store-app/mobile-services-win8-edit2-app.png)

10. 按一下 [ **驗證您的服務** 並記下的值 **用戶端密碼** 和 **封裝安全性識別碼 (SID)**。 

    ![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-win8-app-push-auth.png)

    > [AZURE.NOTE] 用戶端密碼和封裝 SID 是重要的安全性認證。 請勿與任何人共用這些密碼，或與您的應用程式一起散發密碼。

11. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [ **行動電話服務**, ，然後按一下您的應用程式。

    ![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-services-selection.png)

12. 按一下 [ **推送** 索引標籤，輸入 **用戶端密碼** 和 **封裝 SID** 值取自 WNS，然後按一下 [ **儲存**。

    >[AZURE.NOTE]當您完成本教學課程中使用舊版行動服務時，您可能會看到底部的連結 **推送** ] 索引標籤，指出 **啟用進階推送**。 立即按一下這個連結，即可升級您的行動服務來與通知中樞整合。 這個變更無法回復。 如需如何在生產行動服務中啟用進階推播通知的詳細資訊，請參閱<a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">此指引</a>。 

    ![](./media/mobile-services-notification-hubs-register-windows-store-app/mobile-push-tab.png)

    >[AZURE.NOTE]當您設定 WNS 認證的增強式推播通知在 **推播** ] 索引標籤在入口網站中，它們會共用與設定通知中心註冊您的應用程式的通知中心。

<!-- URLs. -->
[Get started with Mobile Services]: ../articles/mobile-services-windows-store-get-started.md
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582


