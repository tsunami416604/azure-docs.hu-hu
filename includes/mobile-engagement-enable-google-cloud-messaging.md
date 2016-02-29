>[AZURE.NOTE] 若要完成此程序，您必須使用已驗證的電子郵件地址的 Google 帳戶。 若要建立新的 Google 帳戶，請前往 <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>。

1. 瀏覽至 [Google Cloud Console](https://console.developers.google.com/project) 並使用您的 Google 帳戶認證登入，然後按一下 **建立專案**。

    ![](./media/mobile-engagement-enable-google-cloud-messaging/new-project.png)   

    ![](./media/mobile-engagement-enable-google-cloud-messaging/new-project-2.png)   

2. 輸入專案名稱，接受服務條款，然後按一下 **建立**。 如果要求，請執行簡訊驗證，然後按一下 [ **建立** 一次。

3. 請記下中的專案號碼 **專案** 一節。 稍後在本教學課程中，您會需要將此號碼填入 Android 資訊清單檔案中。 

    ![](./media/mobile-engagement-enable-google-cloud-messaging/project-number.png)   

4. 在左欄中，依序展開 **APIs & auth**, ，按一下 [ **Api** 然後向下捲動並按一下 [ **Cloud Messaging for Android**。 然後在下一頁按一下 **啟用 API** 並接受服務條款。 

    ![](./media/mobile-engagement-enable-google-cloud-messaging/enable-GCM.png)

    ![](./media/mobile-engagement-enable-google-cloud-messaging/enable-gcm-2.png)

5. 按一下 [ **認證**, ，然後按一下 [ **新增認證**]-> [**API 金鑰** 

    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key.png)

6. 在 **建立新的金鑰**, ，按一下 [ **伺服器金鑰**。 在下一個視窗中按一下 [ **建立**。

    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key5.png)


    ![](./media/mobile-engagement-enable-google-cloud-messaging/create-server-key6.png) 

7. 請記下的 **API 金鑰** 值。 您稍後會使用此 API 金鑰值來設定 [原生推送] 區段。


