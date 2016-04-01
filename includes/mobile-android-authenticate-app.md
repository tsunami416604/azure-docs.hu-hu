
1. 在 **專案總管] 中** 在 Android Studio 中，開啟 ToDoActivity.java 檔案並新增下列 import 陳述式。

        import java.util.concurrent.ExecutionException;
        import java.util.concurrent.atomic.AtomicBoolean;

        import android.content.Context;
        import android.content.SharedPreferences;
        import android.content.SharedPreferences.Editor;

        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.authentication.MobileServiceUser;

2. 將下列方法加入 **ToDoActivity** 類別 ︰ 
    
        private void authenticate() {
            // Login using the Google provider.
            
            ListenableFuture<MobileServiceUser> mLogin = mClient.login(MobileServiceAuthenticationProvider.Google);
    
            Futures.addCallback(mLogin, new FutureCallback<MobileServiceUser>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }           
                @Override
                public void onSuccess(MobileServiceUser user) {
                    createAndShowDialog(String.format(
                            "You are now logged in - %1$2s",
                            user.getUserId()), "Success");
                    createTable();  
                }
            });     
        }


    這會建立新的方法來處理驗證程序。 使用者透過 Google 登入來驗證。 將出現對話方塊來顯示已驗證的使用者的識別碼。 必須通過驗證才能繼續。

    > [AZURE.NOTE] 如果您使用 Google 以外的識別提供者，變更值傳遞至 **登入** 上述方法有下列幾種 ︰ _MicrosoftAccount_, ，_Facebook_, ，_Twitter_, ，或 _windowsazureactivedirectory_。

3. 在 **onCreate** 方法，會具現化的程式碼後面加入下列程式碼行 `MobileServiceClient` 物件。

        authenticate();

    此呼叫會啟動驗證程序。

4. 移動之後的其餘程式碼 `authenticate();` 中 **onCreate** 至新 **createTable** 方法，如下 ︰

        private void createTable() {
    
            // Get the table instance to use.
            mToDoTable = mClient.getTable(ToDoItem.class);
    
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
    
            // Create an adapter to bind the items with the view.
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
    
            // Load the items from Azure.
            refreshItemsFromTable();
        }

9. 從 **執行** ] 功能表上，然後按一下 [ **執行應用程式** 來啟動應用程式，並以您選擇的身分識別提供者登入。 

    成功登入後，應用程式應會正確無誤地執行，而且您應能夠查詢後端服務並更新資料。

