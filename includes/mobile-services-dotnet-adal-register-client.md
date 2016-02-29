## <a name="register-app-aad"></a>向 Azure Active Directory 註冊您的用戶端應用程式

1. 瀏覽至 **Active Directory** 中 [Azure 傳統入口網站](https://manage.windowsazure.com/), ，然後按一下您的目錄。

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-select-aad.png)

2. 按一下 [ **應用程式** ] 索引標籤的頂端，然後按一下以 **新增** 應用程式。 

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-aad-applications-tab.png)

3. 按一下 [ **加入我的組織正在開發的應用程式**。

4. 在 [新增應用程式精靈] 中，輸入 **名稱** 您的應用程式，然後按一下 [  **原生用戶端應用程式** 型別。 接著，按一下以繼續。

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-selection.png)

5. 在 **重新導向 URI** 方塊中，輸入登入/完成您的行動服務的端點。 此值應與 https://todolist.azure-mobile.net/login/done 類似。

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-redirect-uri.png)

6. 按一下 [ **設定** 索引標籤，原生應用程式並複製 **用戶端識別碼**。 稍後您將會需要此資訊。

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-client-id.png)

7. 將頁面向下的捲動 **其他應用程式的權限** 區段，然後按一下 **新增應用程式** ] 按鈕。 選擇 **其他** 從顯示功能表，然後搜尋 todo。 按一下 [ **TodoList** 以新增您稍早註冊的行動服務，然後按一下核取記號，即可。 授與行動服務應用程式的存取權限。 然後按一下 [ **儲存**

   ![](./media/mobile-services-dotnet-adal-register-client/mobile-services-native-add-permissions.png)

已在 AAD 中設定您的行動服務接收來自應用程式的單一登入。

