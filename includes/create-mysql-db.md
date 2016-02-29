本指南將示範如何使用 [ClearDB] 建立 MySQL 資料庫從 [Azure Store] ，以及如何建立 MySQL 資料庫作為連結的資源，當您建立 [Azure 網站][waws] 。 [ClearDB] 是可讓您執行和管理 MySQL 資料庫，在 Azure 資料中心，以及從任何應用程式連線至這些容錯的資料庫做為服務提供者。  

> [AZURE.NOTE] 當您建立 MySQL 資料庫做為網站建立程序的一部分時，您只能建立免費資料庫。 從 Azure 市集建立 MySQL 資料庫時，則可以建立免費資料庫或選擇付費選項。

## 做法：從 Azure 市集建立 MySQL 資料庫

若要建立 MySQL 資料庫從 [Azure Store], ，執行下列動作:

1. 登入 [Azure 管理入口網站][portal]。
2. 按一下 [ **+ 新增** 在頁面底部，然後選取 **MARKETPLACE**。

    ![從市集中選取附加元件](./media/create-mysql-db/select-store.png)

3. 選取 **ClearDB MySQL 資料庫**, ，然後按一下框架底部的箭頭。

    ![選取 ClearDB MySQL 資料庫](./media/create-mysql-db/select-cleardb-mysql.png)

4. 選取方案、輸入資料庫名稱、選取區域，然後按一下框架底部的箭頭。

    ![從市集購買 MySQL 資料庫](./media/create-mysql-db/purchase-mysql.png)

5. 按一下打勾記號完成選購。

    ![檢查並完成選購](./media/create-mysql-db/complete-mysql-purchase.png)

6. 建立資料庫之後，您可以管理從 **附加元件** 在管理入口網站] 索引標籤。

    ![在 Azure 入口網站中管理 MySQL 資料庫](./media/create-mysql-db/manage-mysql-add-on.png)

7. 您可以取得資料庫連接資訊即可 **連線資訊** 底部的頁面 (如上所示)。

    ![MySql 連接資訊](./media/create-mysql-db/mysql-conn-info.png) 


## 如何：為 Azure 網站建立 MySQL 資料庫作為連結的資源

若要建立 MySQL 資料庫作為連結的資源，當您建立 [Azure 網站][waws], ，執行下列動作:

1. 登入 [Azure 管理入口網站][portal]。
2. 按一下 [ **+ 新增** 在頁面底部，然後選取 **計算**, ，**網站**, ，和 **和資料庫一起建立**。

    ![和資料庫一起建立網站](./media/create-mysql-db/custom_create.png)

3. 提供 **URL** 您的網站，請選取 **區域** 網站，然後選擇 [ **建立新的 MySQL 資料庫** 從 **資料庫** 下拉式清單。 (選用) 您可以取代連接字串的預設名稱。 按一下頁面底部的箭頭。

    ![提供網站詳細資料](./media/create-mysql-db/provide-website-details.png) 

4. 提供資料庫 **名稱**, ，請選取 **區域** 資料庫 (這應該是您網站的區域相同)，同意 ClearDB 的法律條款，然後按一下框架底部的核取記號。

    ![提供 MySQL 詳細資料](./media/create-mysql-db/provide-mysql-details.png)

5. 建立網站之後，按一下網站的名稱，以移至網站的儀表板。

    ![移至網站儀表板](./media/create-mysql-db/go-to-website-dashboard.png)

6. 按一下 [ **設定**。

    ![移至設定索引標籤](./media/create-mysql-db/go-to-configure-tab.png)

7. 向下捲動至 **連接字串** 區段，然後按一下 **顯示連接字串**。 

    ![顯示連接字串](./media/create-mysql-db/show-conn-string.png)

8. 複製連接字串以用於應用程式中。

    ![顯示的連接字串](./media/create-mysql-db/shown-conn-string.png)

> [AZURE.NOTE] 連接字串是可存取您網站應用程式可以根據連接字串名稱。 在.NET 應用程式，連接字串位於 **connectionStrings** 物件。 在其他程式設計語言中，連接字串則可以環境變數的形式受到存取。 如需詳細資訊，請參閱 [如何設定網站][configure]。

[ClearDB]: http://www.cleardb.com/
[waws]: /documentation/services/web-sites/
[Azure Store]: ../articles/store.md
[portal]: http://manage.windowsazure.com
[configure]: ../article/app-service-web/web-sites-configure.md

