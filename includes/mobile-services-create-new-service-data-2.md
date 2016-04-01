若要能夠在新行動服務中儲存應用程式資料，您必須先在相關聯的 SQL 資料庫執行個體中建立新的資料表。

1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，按一下 [ **行動電話服務**, ，然後按一下您剛才建立的行動服務。

2. 按一下 [ **資料** 索引標籤，然後按一下 [ **+ 建立**。

    這會顯示 **建立新資料表** ] 對話方塊。

3. 在 **資料表名稱** 類型 _TodoItem_, ，然後按一下核取按鈕。 這會建立新的儲存體資料表 **TodoItem** 包含預設權限集。 這表示擁有應用程式金鑰 (隨應用程式散佈) 的人都可以存取與變更資料表中的資料。 

    >[AZURE.NOTE] 在行動服務快速入門中使用相同的資料表名稱。 However, each table is created in a schema that is specific to a given mobile service. 目的是為了防止多個行動服務使用相同資料庫時產生資料衝突。

4. 按一下新 **TodoItem** 資料表，並驗證其中不含資料列。

5. 按一下 [ **資料行** ] 索引標籤。 驗證是否已為您自動建立下列預設資料行： 
    
    <table border="1" cellpadding="10">
    <tr>
    <th>資料行名稱</th>
    <th>類型</th>
    <th>索引</th>
    </tr>
    <tr>
    <td>id</td>
    <td>字串</td>
    <td>已編製索引</td>
    </tr>
    <tr>
    <td>__createdAt</td>
    <td>日期</td>
    <td>已編製索引</td>
    </tr>
    <tr>
    <td>__updatedAt</td>
    <td>日期</td>
    <td><font color="transparent">-</font></td>
    </tr>
    <tr>
    <td>__version</td>
    <td>時間戳記 (MSSQL)</td>
    <td><font color="transparent">-</font></td>
    </tr>   
    </table>    
        

    This is the minimum requirement for a table in Mobile Services. 

    > [AZURE.NOTE] When dynamic schema is enabled on your mobile service, new columns are created automatically when JSON objects are sent to the mobile service by an insert or update operation.

現在您已準備好將新的行動服務作為應用程式的資料儲存區使用。


