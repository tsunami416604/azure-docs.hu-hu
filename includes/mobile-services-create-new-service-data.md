

接下來，您將建立新的行動服務來取代記憶體內部資料儲存區清單。 依照下列步驟即可建立新的行動服務。

1. 登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。 
2.  在導覽窗格的底部，按一下 [ **+ 新增**。

    ![plus-new](./media/mobile-services-create-new-service-data/plus-new.png)

3.  展開 **計算** 和 **行動服務**, ，然後按一下 [ **建立**。

    ![mobile-create](./media/mobile-services-create-new-service-data/mobile-create.png)

    這會顯示 **新的行動服務** ] 對話方塊。

4.  在 **建立的行動服務** 頁面上，選取 **建立免費的 20 MB SQL Database**, ，然後輸入新的行動服務中的子網域名稱 **URL** 文字方塊並等候進行名稱驗證。 一旦名稱驗證完成之後，按一下向右鍵按鈕前往下一頁。   

    ![mobile-create-page1](./media/mobile-services-create-new-service-data/mobile-create-page1.png)

    這會顯示 **指定資料庫設定** 頁面。

    
    > [AZURE.NOTE] As part of this tutorial, you create a new SQL Database instance and server. You can reuse this new database and administer it as you would any other SQL Database instance. If you already have a database in the same region as the new mobile service, you can instead choose **Use existing Database** and then select that database. The use of a database in a different region is not recommended because of additional bandwidth costs and higher latencies.

5.  在 **名稱**, ，輸入名稱的新資料庫，然後輸入 **登入名稱**, ，這是系統管理員登入新的 SQL 資料庫伺服器類型名稱並確認密碼，然後按一下檢查按鈕以完成程序。

    ![mobile-create-page2](./media/mobile-services-create-new-service-data/mobile-create-page2.png)

    
    > [AZURE.NOTE] When the password that you supply does not meet the minimum requirements or when there is a mismatch, a warning is displayed.  
    >
    > We recommend that you make a note of the administrator login name and password that you specify; you will need this information to reuse the SQL Database instance or the server in the future.

您現在已經建立可供行動應用程式使用的新行動服務。 接下來，您將新增要用來儲存應用程式資料的資料表。 應用程式將使用此資料表來取代記憶體內部收集。


