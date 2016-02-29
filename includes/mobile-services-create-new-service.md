

依照下列步驟即可建立新的行動服務。

1.  登入 [Azure 傳統入口網站](https://manage.windowsazure.com/)。 在導覽窗格的底部，按一下 [ **+ 新增**。 展開 **計算** 和 **行動服務**, ，然後按一下 [ **建立**。

    ![](./media/mobile-services-create-new-service/mobile-create.png)

    這會顯示 **建立行動服務** ] 對話方塊。

2.  在 **建立行動服務** 對話方塊中，選取 **建立免費的 20 MB SQL Database**, ，請選取 **JavaScript** 中新的行動服務執行階段，然後輸入子網域名稱 **URL** 文字方塊。 按一下向右鍵按鈕以移至下一個頁面。

    ![](./media/mobile-services-create-new-service/mobile-create-page1.png)

    這會顯示 **指定資料庫設定** 頁面。
    
    >[AZURE.NOTE]As part of this tutorial, you create a new SQL Database instance and server. 您可以如同任何其他 SQL Database 執行個體般重新使用這個新資料庫，並加以管理 如果您已經有資料庫做為新的行動服務的相同區域中，您可以改選 [ **使用現有的資料庫** ，然後選取該資料庫。 不建議您使用位在不同區域的資料庫，因為這會需要額外的頻寬成本和產生更高的延遲。

3.  在 **名稱**, ，輸入名稱的新資料庫，然後輸入 **登入名稱**, ，這是系統管理員登入新的 SQL 資料庫伺服器類型名稱並確認密碼，然後按一下檢查按鈕以完成程序。
    ![](./media/mobile-services-create-new-service/mobile-create-page2.png)

您現在已經建立可供行動應用程式使用的新行動服務。


