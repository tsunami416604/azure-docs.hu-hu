1.  登入線上 [Microsoft Azure Preview 入口網站](https://portal.azure.com/)。
2.  在 Jumpbar 中按一下 [ **新增**, ，然後按一下 [ **資料 + 儲存體**, ，然後按一下 [ **Azure DocumentDB**。 
  
    ![若要建立資料庫時，反白顯示 [新增] 按鈕、 資料 + 儲存體，在 [建立] 分頁中，以及資料中的 Azure DocumentDB Azure 預覽入口網站的螢幕擷取畫面 + 儲存體] 刀鋒視窗](media/documentdb-create-dbaccount/ca1.png)  

3. 在 **新增 DocumentDB 帳戶** 刀鋒視窗中，指定 DocumentDB 帳戶所需的設定。 
 
    ![[新增 DocumentDB] 刀鋒視窗的螢幕擷取畫面](media/documentdb-create-dbaccount/ca3.png) 


    - In the **ID** box, enter a name to identify the DocumentDB account.  When the **ID** is validated, a green check mark appears in the **ID** box. The **ID** value becomes the host name within the URI. The **ID** may contain only lowercase letters, numbers, and the '-' character, and must be between 3 and 50 characters. Note that *documents.azure.com* is appended to the endpoint name you choose, the result of which will become your DocumentDB account endpoint.
    

    - The **Account Tier** lens is locked because DocumentDB supports a single standard account tier. For more information, see [DocumentDB pricing](http://go.microsoft.com/fwlink/p/?LinkID=402317&clcid=0x409).
    
    - For **Subscription**, select the Azure subscription that you want to use for the DocumentDB account. If your account has only one subscription, that account is selected by default.

    - In **Resource Group**, select or create a resource group for your DocumentDB account.  By default, a new Resource group will be created.  You may, however, choose to select an existing resource group to which you would like to add your DocumentDB account. For more information, see [Using the Azure Preview Portal to manage your Azure resources](resource-group-portal.md).
 
    - Use **Location** to specify the geographic location in which to host your DocumentDB account.   

4.  一旦設定新的 DocumentDB 帳戶選項，按一下 **建立**。  建立 DocumentDB 帳戶可能需要數分鐘。  若要檢查狀態，您可以在「開始面板」上監視進度。  
    ![儀表板-線上資料庫建立者上建立磚的螢幕擷取畫面](media/documentdb-create-dbaccount/ca4.png)  
  
    或者，您也可以從 [通知] 中樞監視進度。  

    ![快速建立資料庫 - 通知中樞的螢幕擷取畫面，顯示正在建立 DocumentDB 帳戶](media/documentdb-create-dbaccount/ca5.png)  

    ![通知中樞的螢幕擷取畫面，顯示已成功建立 DocumentDB 帳戶並部署到資源群組 - 線上資料庫建立者通知](media/documentdb-create-dbaccount/ca6.png)

5.  建立 DocumentDB 帳戶之後，即可在線上入口網站中使用預設設定。 請注意，DocumentDB 帳戶的預設一致性設定為 **工作階段**。  您可以按一下來調整預設一致性設定 **預設一致性** 磚 **DocumentDB 帳戶** 刀鋒視窗。

    ![[資源群組]  刀鋒視窗的螢幕擷取畫面 - 開始進行應用程式開發](media/documentdb-create-dbaccount/ca7.png)  

[How to: Create a DocumentDB account]: #Howto
[Next steps]: #NextSteps
[documentdb-manage]:../articles/documentdb/documentdb-manage.md


