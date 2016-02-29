

>[AZURE.NOTE]若要完成此程序，您必須使用已驗證的電子郵件地址的 Google 帳戶。 若要建立新的 Google 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>。


1. 瀏覽至 <a href="http://cloud.google.com/console" target="_blank">Google Cloud Console</a> 網站，使用您的 Google 帳戶認證登入，然後按一下 [ **建立專案**。

    ![](./media/notification-hubs-android-get-started/mobile-services-google-new-project.png)   

    >[AZURE.NOTE]當您已經有現有的專案時，將您導向至 <strong>專案</strong> 登入後的頁面。 若要從 [儀表板] 建立新的專案，請展開 [API 專案]<strong></strong>，並按一下 [其他專案]<strong></strong> 下的 [建立...]<strong></strong>，然後輸入專案名稱，並按一下 [建立專案]<strong></strong>。

2. 輸入專案名稱，接受服務條款，然後按一下 **建立**。 如果要求，請執行簡訊驗證，然後按一下 [ **建立** 一次。

3. 請記下中的專案號碼 **專案** 一節。 

    在本教學課程的稍後內容中，您會設定這個值作為用戶端中的 PROJECT_ID 變數。

4. 在左欄中，依序展開 **APIs & auth**, ，按一下 [ **Api** 然後向下捲動並按一下切換開關以啟用 **Google Cloud Messaging for Android** 並接受服務條款。 

    ![](./media/notification-hubs-android-get-started/mobile-services-google-enable-GCM.png)

5. 按一下 [ **認證**, ，然後按一下 [ **建立新的金鑰** 

    ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key.png)

6. 在 **建立新的金鑰**, ，按一下 [ **伺服器金鑰**。 在下一個視窗中按一下 [ **建立**。

    ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key2.png)

7. 請記下的 **API 金鑰** 值。

    ![](./media/notification-hubs-android-get-started/mobile-services-google-create-server-key3.png) 

    您將使用此 API 金鑰值，讓 Azure 能夠使用 GCM 進行驗證，並代表您的應用程式傳送推播通知。


