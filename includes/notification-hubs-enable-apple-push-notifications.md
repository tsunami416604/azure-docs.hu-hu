

##產生憑證簽署要求檔案

Apple Push Notification Service (APNS) 使用憑證來驗證您的通知服務。 遵循下列指示來建立必要的推播憑證，以便傳送和接收通知。 如需這些概念的詳細資訊請參閱官方 [Apple 推播通知服務](http://go.microsoft.com/fwlink/p/?LinkId=272584) 文件。

產生憑證簽署要求 (CSR) 檔案，這將由 Apple 用來產生簽署的推播憑證。

1. 在您的 Mac 上，執行「鑰匙圈存取」工具。 您可以從開啟 **公用程式** 資料夾或 **其他** 啟動平台上的資料夾。

2. 按一下 [ **鑰匙圈存取**, ，依序展開 **憑證助理**, ，然後按一下 [ **要求憑證從憑證授權單位...**。

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-request-cert-from-ca.png)

3. 選取您 **使用者電子郵件地址** 和 **一般名稱** , ，請確定 **儲存至磁碟** 已選取，然後按一下 [ **繼續**。 保留 **CA 電子郵件地址** 留空，因為它不是必要欄位。

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-csr-info.png)

4. 輸入中的憑證簽署要求 (CSR) 檔案的名稱 **另存新檔**, 中, 選取位置 **其中**, ，然後按一下 [ **儲存**。

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-save-csr.png)

    這會在選取的位置中儲存 CSR 檔案，預設的位置是在桌面上。 請記住為檔案選擇的位置。

接著，您將向 Apple 註冊您的應用程式，並啟用推播通知，然後上傳這個匯出的 CSR 建立推播憑證。

##針對推播通知註冊應用程式

若要將推播通知傳送至 iOS 應用程式，您必須向 Apple 註冊您的應用程式，並註冊進行推播通知。  

1. 如果您尚未註冊您的應用程式，瀏覽至 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 佈建入口網站</a> Apple 開發人員中心，使用您的 Apple ID 登入按一下 **識別碼**, ，然後按一下 [ **應用程式識別碼**, ，並依 **+** 號註冊新的應用程式。

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids.png)


2. 更新新應用程式的下列三個欄位，然後按一下 [ **繼續**:

    * **名稱**: 輸入您的應用程式中的描述性名稱 **名稱** 欄位 **應用程式識別碼描述** 一節。
    
    * **配套識別碼**: 下 **明確的應用程式識別碼** 區段中，輸入 **Bundle Identifier** 形式 `<Organization Identifier>.<Product Name>` 中所述 [應用程式散發指南](https://developer.apple.com/library/mac/documentation/IDEs/Conceptual/AppDistributionGuide/ConfiguringYourApp/ConfiguringYourApp.html#//apple_ref/doc/uid/TP40012582-CH28-SW8)。  *組織識別碼* 和 *產品名稱* 使用必須符合當您建立 XCode 專案時，您將使用的組織識別碼和產品名稱。 在下面的快照中 *NotificationHubs* 做為組織識別碼和 *GetStarted* 做為產品名稱。 確定這符合您將在 XCode 專案中使用的值，讓您使用正確的發行設定檔搭配 XCode。 
    
    * **推播通知**: 檢查 **推播通知** 選項 **應用程式服務** 一節。

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-appid-info.png)

    這將產生您的應用程式識別碼，並要求您確定此資訊。 按一下 [ **提交**


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-confirm-new-appid.png)


    Once you click **Submit**, you will see the **Registration complete** screen, as shown below. Click **Done**.


    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-registration-complete.png)


3. 在開發人員中心中，於 [App IDs] 下找出您剛才建立的 App ID，並在該列上按一下。

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-ios-appids2.png)

    按一下應用程式識別碼，將顯示應用程式詳細資料。 按一下 [ **編輯** 底部的按鈕。

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-edit-appid.png)

4. 捲動到畫面底部，按一下 **建立憑證...** ] 區段下的按鈕 **Development Push SSL Certificate**。

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-create-cert.png)

    這將顯示 [Add iOS Certificate] 助理。

    > [AZURE.NOTE] 本教學課程使用開發憑證。 註冊生產憑證時，將使用同一個程序。 只要確定在傳送通知時使用相同的憑證類型即可。

5. 按一下 [ **選擇檔案**, ，瀏覽至儲存在第一個工作中，您建立之 CSR 檔案的位置，然後按一下 [ **產生**。

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-cert-choose-csr.png)

6. 在入口網站建立憑證之後，請按一下 **下載** ] 按鈕，然後按一下 [ **完成**。

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-appid-download-cert.png)

    這會下載憑證並儲存到您電腦中的 [下載] 資料夾。

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-downloaded.png)

    > [AZURE.NOTE] 根據預設，下載的檔案開發憑證名為 **aps_development.cer**。

7. 按兩下下載的推播憑證 **aps_development.cer**。

    這樣會將新的憑證安裝在金鑰鏈中，如下所示：

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-cert-in-keychain.png)

    > [AZURE.NOTE] 您的憑證中的名稱可能會不同，但將會加 **Apple Development iOS Push Services:**。

8. 在 Keychain Access 中，以滑鼠右鍵按一下您在中建立的新推播憑證 **憑證** 類別。 按一下 [ **匯出**, 、 為檔案命名、 選取 **.p12** 格式，然後再按一下 **儲存**。

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-export-cert-p12.png)

    記下匯出的 .p12 憑證的檔案名稱和位置。 將用來啟用 APNS 驗證功能。

    >[AZURE.NOTE] 本教學課程將建立 QuickStart.p12 檔案。 Your file name and location might be different.


##建立應用程式的佈建設定檔

1. 回到 <a href="http://go.microsoft.com/fwlink/p/?LinkId=272456" target="_blank">iOS 佈建入口網站</a>, ，請選取 **佈建設定檔**, ，請選取 **所有**, ，然後按一下 [ **+** ] 按鈕以建立新的設定檔。 這會啟動 **Add iOS Provisiong Profile** 精靈

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-new-provisioning-profile.png)

2. 選取 **iOS App Development** 下 **開發** 為佈建設定檔類型，然後按一下 **繼續**。 


3. 接下來，選取您剛從建立的應用程式識別碼 **應用程式識別碼** 下拉式清單中，然後按一下 [ **繼續**

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-select-appid-for-provisioning.png)


4. 在 **選取憑證** 畫面上，選取 [一般開發憑證用於程式碼簽署，並按一下 [ **繼續**。 這不是您剛才建立的推播憑證。

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-cert.png)


5. 接下來，選取 **裝置** 進行測試，然後按一下 **繼續**

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-select-devices.png)


6. 最後，選取 [在設定檔的名稱 **設定檔名稱**, ，按一下 [ **產生**。

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-name-profile.png)


7. 當新的佈建設定檔建立好時，按一下它來下載並安裝在 Xcode 開發電腦上。 然後按一下 [ **完成**。

    ![](./media/notification-hubs-enable-apple-push-notifications/notification-hubs-provisioning-profile-ready.png)



