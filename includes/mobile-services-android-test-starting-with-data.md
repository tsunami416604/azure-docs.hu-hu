現在，應用程式已更新為使用行動服務做為後端儲存，您可以使用 Android 模擬器或 Android 手機，在行動服務上測試應用程式。

1. 從 **執行** ] 功能表上，按一下 [ **執行** 來啟動專案。

    這樣會執行您的應用程式 (以 Android SDK 建立)，該應用程式利用用戶端程式庫傳送查詢，然後從您的行動服務傳回項目。

5. 同樣地，輸入有意義的文字，然後按一下 [ **新增**。

    這會傳送新項目以插入至行動服務。

    您可以重新啟動應用程式，以確認變更持續存留至 Azure 中的資料庫。 您也可以檢查使用 Azure 傳統入口網站的資料庫: 後續的兩個步驟執行此動作以檢視您的資料庫中的變更。


4. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [管理您的行動服務相關聯的資料庫。

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png)

5. 在傳統入口網站中執行查詢，以檢視 Windows 市集應用程式所做的變更。 您的查詢會與下列查詢類似，但使用您的資料庫名稱，而非 `todolist`。

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png)

這將結束 **開始使用資料** android 教學課程。
