

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

    
    > [AZURE.NOTE] 本教學課程的一部分，您可以建立新的 SQL 資料庫執行個體和伺服器。 您可以如同任何其他 SQL Database 執行個體般重新使用這個新資料庫，並加以管理 如果您已經有資料庫做為新的行動服務的相同區域中，您可以改選 [ **使用現有的資料庫** ，然後選取該資料庫。 不建議您使用位在不同區域的資料庫，因為這會需要額外的頻寬成本和產生更高的延遲。

5.  在 **名稱**, ，輸入名稱的新資料庫，然後輸入 **登入名稱**, ，這是系統管理員登入新的 SQL 資料庫伺服器類型名稱並確認密碼，然後按一下檢查按鈕以完成程序。

    ![mobile-create-page2](./media/mobile-services-create-new-service-data/mobile-create-page2.png)

    
    > [AZURE.NOTE] 當您提供的密碼不符合最低需求或不相符時，會顯示警告。  
    >
    > 建議您記下您所指定的系統管理員登入名稱和密碼；您日後需要此資訊才能重複使用 SQL 資料庫執行個體或伺服器。

您現在已經建立可供行動應用程式使用的新行動服務。 接下來，您將新增要用來儲存應用程式資料的資料表。 應用程式將使用此資料表來取代記憶體內部收集。



