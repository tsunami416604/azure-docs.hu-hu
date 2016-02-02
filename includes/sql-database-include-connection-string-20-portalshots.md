


### 從 Azure 入口網站取得連接字串

使用 [Azure 預覽入口網站](http://portal.azure.com/) 來取得用戶端程式與 Azure SQL 資料庫互動所需的連接字串:


1. 按一下 [瀏覽]**** > [SQL 資料庫]****。

2. 在 [SQL 資料庫]**** 刀鋒視窗左上角附近的篩選文字方塊中輸入您的資料庫名稱。

3. 按一下資料庫的資料列。

4. 刀鋒視窗中顯示為您的資料庫後，visual 方便您可以按一下摺疊您用來瀏覽和資料庫篩選的刀鋒視窗的標準最小化控制項。

    ![篩選以隔離您的資料庫][10-filterdatabase]

5. 在您資料庫的刀鋒視窗上，按一下 [顯示資料庫連接字串]****。

6. 如果您想要使用 ADO.NET 連線庫，請複製標示為 **ADO** 的字串。

    ![複製資料庫的 ADO 連接字串][20-copyadoconnectionstring]

7. 以其中一種格式，將連接字串資訊貼入您的用戶端程式碼中。



如需詳細資訊，請參閱:<br/>[連接字串和組態檔](http://msdn.microsoft.com/library/ms254494.aspx)。










[10-filterdatabase]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-a.png 
[20-copyadoconnectionstring]: ./media/sql-database-include-connection-string-20-portalshots/connqry-connstr-b.png 

