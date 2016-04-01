

若要透過 Apple Push Notification Service (APNS) 註冊要進行推播通知的應用程式，您必須在 Apple 開發人員入口網站上為專案建立新的推播憑證、應用程式識別碼和佈建設定檔。 應用程式識別碼會包含可讓您的應用程式傳送及接收推播通知的組態設定。 這些設定將會包含傳送及接收推播通知時，向 Apple Push Notification Service (APNS) 進行驗證所需的推播通知憑證。 如需這些概念的詳細資訊請參閱官方 [Apple 推播通知服務](http://go.microsoft.com/fwlink/p/?LinkId=272584) 文件。


####產生推播憑證的憑證簽署要求檔案

這些步驟會引導您建立憑證簽署要求。 這將會用來產生用於 APNS 的推播憑證。

1. 在您的 Mac 上，執行「鑰匙圈存取」工具。 您可以從開啟 **公用程式** 資料夾或 **其他** 啟動平台上的資料夾。

2. 按一下 [ **鑰匙圈存取**, ，依序展開 **憑證助理**, ，然後按一下 [ **要求憑證從憑證授權單位...**。

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. 選取您 **使用者電子郵件地址** 和 **一般名稱** , ，請確定 **儲存至磁碟** 已選取，然後按一下 [ **繼續**。 保留 **CA 電子郵件地址** 留空，因為它不是必要欄位。

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. 輸入中的憑證簽署要求 (CSR) 檔案的名稱 **另存新檔**, 中, 選取位置 **其中**, ，然後按一下 [ **儲存**。

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-save-csr.png)

    這會在選取的位置中儲存 CSR 檔案，預設的位置是在桌面上。 請記住為檔案選擇的位置。


####針對推播通知註冊應用程式

為您的 Apple 應用程式建立新的明確應用程式識別碼，並同時為它設定推播通知。  

1. 瀏覽至 [iOS 佈建入口網站](http://go.microsoft.com/fwlink/p/?LinkId=272456) Apple 開發人員中心，使用您的 Apple ID 登入按一下 **識別碼**, ，然後按一下 [ **應用程式識別碼**, ，並依 **+** 號註冊新的應用程式。

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-ios-appids.png)

2. 更新新應用程式的下列三個欄位，然後按一下 [ **繼續**:

    * **名稱**︰ 輸入您的應用程式中的描述性名稱 **名稱** 欄位 **應用程式識別碼描述** 一節。
    
    * **配套識別碼**︰ 下 **明確的應用程式識別碼** 區段中，輸入 **Bundle Identifier** 形式 `<Organization Identifier>.<Product Name>` 中所述 [應用程式散發指南](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8)。 這必須符合也用於應用程式的 XCode 或 Xamarin 專案的識別碼。    
     
    * **推播通知**︰ 檢查 **推播通知** 選項 **應用程式服務** 一節。

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

3.  [確認您的應用程式識別碼的畫面中，檢閱設定，而且之後已經確認它們按一下 **送出**

4.  一旦您已送出新的應用程式識別碼，您會看到 **註冊完成** 畫面。 按一下 [ **完成**。

5. 在開發人員中心中，於 [App IDs] 下找出您剛才建立的 App ID，並在該列上按一下。 按一下應用程式識別碼列，將顯示應用程式詳細資料。 按一下 [ **編輯** 底部的按鈕。

6. 捲動到畫面底部，按一下 **建立憑證...** ] 區段下的按鈕 **Development Push SSL Certificate**。

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    這將顯示 [新增 iOS 憑證] 助理。

    > [AZURE.NOTE] 本教學課程使用開發憑證。 註冊生產憑證時，將使用同一個程序。 只要確定在傳送通知時使用相同的憑證類型即可。

7. 按一下 [ **選擇檔案**, ，瀏覽至您儲存 CSR 推播憑證的位置。 然後按一下 [ **產生**。

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

8. 在入口網站建立憑證之後，請按一下 **下載** ] 按鈕。

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    這會下載簽署憑證，並將它儲存到您電腦中的 [下載] 資料夾。

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE] 根據預設，下載的檔案開發憑證名為 **aps_development.cer**。

9. 按兩下下載的推播憑證 **aps_development.cer**。 這樣會將新的憑證安裝在金鑰鏈中，如下所示：

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE] 您的憑證中的名稱可能會不同，但將會加 **Apple Development iOS Push Services:**。

10. 在 Keychain Access 中，以滑鼠右鍵按一下您在中建立的新推播憑證 **憑證** 類別。 按一下 [ **匯出**, 、 為檔案命名、 選取 **.p12** 格式，然後再按一下 **儲存**。

    請記住匯出的 .p12 推播憑證的檔案名稱和位置。 透過在 Azure 傳統入口網站將它上傳，即可將它用來向 APNS 進行驗證。



####建立應用程式的佈建設定檔

1. 回到 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 佈建入口網站</a>, ，請選取 **佈建設定檔**, ，請選取 **所有**, ，然後按一下 [ **+** ] 按鈕以建立新的設定檔。 這會啟動 **Add iOS Provisiong Profile** 精靈

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. 選取 **iOS App Development** 下 **開發** 為佈建設定檔類型，然後按一下 **繼續**。 


3. 接下來，選取您剛從建立的應用程式識別碼 **應用程式識別碼** 下拉式清單中，然後按一下 [ **繼續**

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. 在 **選取憑證** 畫面上，選取您的開發憑證用於程式碼簽署，並按一下 [ **繼續**。 這是簽署憑證，不是您剛才建立的推播憑證。

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. 接下來，選取 **裝置** 進行測試，然後按一下 **繼續**

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. 最後，選取 [在設定檔的名稱 **設定檔名稱**, ，按一下 [ **產生**。

    ![](./media/notification-hubs-xamarin-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)




