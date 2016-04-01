
1. 瀏覽至 [Google Cloud Console](https://console.developers.google.com/project), ，使用您的 Google 帳戶認證登入。 
 
2. 按一下 [ **建立專案**, ，輸入專案名稱，然後按一下 [ **建立**。 如果要求，請執行簡訊驗證，然後按一下 [ **建立** 一次。

    ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-new-project.png)   

     在新的型別 **專案名稱** 按一下 **建立專案**。

3. 請記下中的專案號碼 **專案** 一節。 您必須設定這個值稱為 *PROJECT_ID* 變數中的用戶端。

4. 在專案儀表板中，按一下 [ **使用 Google Api** > **Cloud Messaging for Android**, ，然後在下一頁按一下 **啟用 API**。 

5. 在 API 管理員中，按一下 [ **認證** > **加入認證** > **API 金鑰**。 

    ![](./media/mobile-services-enable-google-cloud-messaging/mobile-services-google-create-server-key.png)

6. 在 **建立新的金鑰**, ，按一下 [ **伺服器金鑰**, ，輸入您的金鑰的名稱，然後按一下 [ **建立**。

7. 請記下的 **API 金鑰** 值。

    您將使用此 API 金鑰值，讓 Azure 能夠使用 GCM 進行驗證，並代表您的應用程式傳送推播通知。



