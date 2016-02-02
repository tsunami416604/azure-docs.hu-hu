接下來，您需要變更您註冊通知的時間，以確認使用者在嘗試註冊前已經驗證。


1. 在 Android Studio 中的專案總管]，開啟 ToDoActivity.java 檔案並尋找 `onCreate` 方法。 將下列程式碼從 `onCreate` 方法的開頭 `createTable` 方法。

     NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

  `CreateTable` 方法時，會呼叫 `驗證` 方法完成。 您的整個 `createTable` 方法看起來應該類似如下。

     private void createTable() {
    
         NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
    
         // Get the Mobile Service Table instance to use
         mToDoTable = mClient.getTable(ToDoItem.class);
    
         mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
    
         // Create an adapter to bind the items with the view
         mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
         ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
         listViewToDo.setAdapter(mAdapter);
    
         // Load the items from the Mobile Service
         refreshItemsFromTable();
     }   







