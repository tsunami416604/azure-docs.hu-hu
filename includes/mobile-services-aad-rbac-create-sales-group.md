在本節中，您將新增兩位使用者及 Sales 群組至目錄。 其中一位使用者將獲得 Sales 群組的成員資格。 另一位使用者將不會獲得此群組的成員資格。 

### 建立使用者


1. 在 [Azure 傳統入口網站](https://manage.windowsazure.com) 瀏覽至您先前設定驗證當您完成教學課程中，將驗證新增至您的應用程式的目錄。
2. 按一下 [ **使用者** 頁面的頂端。 然後按一下 [ **新增使用者** 底部的按鈕。 
3. 完成新增使用者對話方塊，以建立名為的使用者 **Bob**。 記下該使用者的暫時密碼。 
4. 建立名為的另一個使用者 **Dave**。 記下該使用者的暫時密碼。
5. 新使用者看起來應該會如下所示。

    ![](./media/mobile-services-aad-rbac-create-sales-group/users.png)    


### 建立 Sales 群組


1. 在 [目錄] 頁面中，按一下 [ **群組** 頁面的頂端。 然後按一下 [ **加入群組** 底部的按鈕。 
2. 輸入 **銷售** 群組並按對話方塊上的 [完成] 按鈕以建立群組的名稱。 

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group.png)

### 新增 Sales 群組的使用者成員資格。


1. 按一下 [ **群組** 頂端的 [目錄] 頁面。 然後按一下 [ **銷售** 群組，移至 sales 群組頁面。 
2. 在 Sales 群組頁面上，按一下 [ **新增成員**。 新增名為的使用者 **Bob** 到 sales 群組。 指定的使用者 **Dave** 不應群組的成員。

    ![](./media/mobile-services-aad-rbac-create-sales-group/group-membership.png)

3. 在 Sales 群組頁面上，按一下 [ **屬性**, ，然後將複製 **物件識別碼** sales 群組頁面的底部。 

   
    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id.png)

4. 瀏覽回到您的行動服務的組態頁面，並新增物件識別碼的應用程式設定已命名為 **AAD\_SALES\_GROUP\_ID**。 本教學課程使用群組的物件識別碼做為應用程式設定，而不是根據群組名稱查閱識別碼。 這是因為群組名稱可能會變更，但識別碼會保持相同。

    ![](./media/mobile-services-aad-rbac-create-sales-group/sales-group-id-app-setting.png)
