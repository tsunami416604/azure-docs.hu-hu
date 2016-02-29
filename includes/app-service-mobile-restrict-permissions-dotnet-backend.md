
根據預設，可以匿名方式叫用行動應用程式後端中的 API。 接下來，您必須限制只有經過驗證的用戶端才有存取權。  

+ **Node.js 後的端 (透過入口網站)** :  
    
    在行動應用程式的 **設定**, ，按一下 [ **簡單資料表** ，然後選取您的資料表。 按一下 [ **變更權限**, ，請選取 **驗證存取權限** 所有權限，然後按一下 [ **儲存**。 

+ **.NET 後端 (C#)**:  

    在伺服器專案中，瀏覽至 **控制器** > **TodoItemController.cs**。 新增 `[Authorize]` 屬性設定為 **TodoItemController** 類別，如下所示。 若要限制只有特定方法才能存取，也可以將此屬性套用至這些方法，而不是類別。 發佈伺服器專案。


        [Authorize]
        public class TodoItemController : TableController<TodoItem>

+ **Node.js 後的端 (透過 Node.js 程式碼)** :  
    
    如需要求資料表存取驗證，請將下行加入 Node.js 伺服器指令碼：


        table.access = 'authenticated';

    For more details, refer to [How to: Require authentication for access to tables](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-auth). To learn how to download the quickstart code project from your site, see [How to: Download the Node.js backend quickstart code project using Git](../articles/app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#download-quickstart).


