
本教學課程中的最後一個選擇性步驟，是簽入與行動服務相關聯的 SQL Database 及檢閱儲存的資料。 

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [管理您的行動服務相關聯的資料庫。
 
    ![sign-in to manage SQL Database](./media/mobile-services-dotnet-backend-view-sql-data/manage-sql-azure-database.png)

2. 在入口網站中執行查詢，以檢視 Windows 市集應用程式所做的變更。 您的查詢會與下列查詢相類似，但使用您的資料庫名稱，而不是 <code>todolist</code>。</p>

        SELECT * FROM [todolist].[todoitems]

    ![query SQL Database for stored items](./media/mobile-services-dotnet-backend-view-sql-data/sql-azure-query.png)

    請注意，資料表含有 Id、__createdAt、__updatedAt 及 __version 等欄。 這些欄支援離線資料同步處理，並採用 [EntityData](http://msdn.microsoft.com/library/microsoft.windowsazure.mobile.service.entitydata.aspx) 基底類別。 如需詳細資訊，請參閱 [開始使用離線資料同步]。

