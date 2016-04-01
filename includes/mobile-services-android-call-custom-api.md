
###<a name="update-app"></a>更新應用程式以呼叫自訂 API

1. 我們將在現有的按鈕旁新增標示為 [Complete All] 的按鈕，並將兩個按鈕下移一行。 在 Android Studio 中開啟 *res\layout\activity_to_do.xml* 快速入門專案檔案，找出 **LinearLayout** 包含項目 **按鈕** 名 `buttonAddToDo`。 複製 **LinearLayout** 並將其緊貼在原始貼。 刪除 **按鈕** 從第一個項目 **LinearLayout**。

2. 在第二個 **LinearLayout**, ，刪除 **EditText** 項目，並新增下列程式碼緊接著現有 **按鈕** 項目 ︰ 

        <Button
            android:id="@+id/buttonCompleteItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="completeItem"
            android:text="@string/complete_button_text" />

    這會將新按鈕新增至頁面上不同行的現有按鈕旁邊。

3. 第二個 **LinearLayout** 現在看起來像這樣 ︰

         <LinearLayout
            android:layout_width="match_parent" 
            android:layout_height="wrap_content" 
            android:background="#71BCFA"
            android:padding="6dip"  >
            <Button
                android:id="@+id/buttonAddToDo"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="addItem"
                android:text="@string/add_button_text" />
            <Button
                android:id="@+id/buttonCompleteItem"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="completeItem"
                android:text="@string/complete_button_text" />
        </LinearLayout>
    

4. 開啟 res\values\string.xml 檔案並新增下列程式碼行：

        <string name="complete_button_text">Complete All</string>



5. 在 [專案總管] 中，以滑鼠右鍵按一下專案名稱，在 *src* 資料夾 (`com.example.{your projects name}`)，選擇 [ **新增** 然後 **類別**。 在對話方塊中，輸入 **MarkAllResult** 在類別名稱] 欄位中，選擇 [確定]，並以下列程式碼取代產生的類別定義 ︰

        import com.google.gson.annotations.SerializedName;
        
        public class MarkAllResult {
            @SerializedName("count")
            public int mCount;
            
            public int getCount() {
                return mCount;
            }
        
            public void setCount(int count) {
                    this.mCount = count;
            }
        }

    此類別是用來保留自訂 API 傳回的資料列計數值。 

6. 找出 **refreshItemsFromTable** 方法中的 **ToDoActivity.java** 檔案，並確定程式碼中的第一行 `try` 區塊看起來像這樣 ︰

        final MobileServiceList<ToDoItem> result = mToDoTable.where().field("complete").eq(false).execute().get();

    這會篩選項目，如此一來，查詢就不會傳回已完成的項目。

7. 請確定 **ToDoActivity.java** 包含下列匯入檔案的開頭 ︰

        import com.google.common.util.concurrent.FutureCallback;
        import com.google.common.util.concurrent.Futures;
        import com.google.common.util.concurrent.ListenableFuture;

8. 在 **ToDoActivity.java** 檔案中，新增下列方法 ︰

    public void completeItem(View view) {
        
        ListenableFuture<MarkAllResult> result = mClient.invokeApi( "completeAll2", MarkAllResult.class ); 
            
            Futures.addCallback(result, new FutureCallback<MarkAllResult>() {
                @Override
                public void onFailure(Throwable exc) {
                    createAndShowDialog((Exception) exc, "Error");
                }
                
                @Override
                public void onSuccess(MarkAllResult result) {
                    createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
                    refreshItemsFromTable();    
                }
            });
        }
    
    此方法會處理 **按一下** 新按鈕的事件。  **InvokeApi** 方法會在用戶端，可將 POST 要求傳送給新的自訂 API 呼叫。 如有任何錯誤，自訂 API 傳回的結果會顯示在訊息對話方塊中。

### 測試應用程式

1. 從 **執行** ] 功能表上，按一下 [ **執行應用程式** 在 Android 模擬器中，或連線的 Android 裝置中啟動專案。

    這樣會執行您的應用程式 (以 Android SDK 建立)，該應用程式利用用戶端程式庫傳送查詢，然後從您的行動服務傳回項目。


2. 在應用程式中，輸入某些文字 **Insert a TodoItem**, ，然後按一下 [ **新增**。

3. 重複前一個步驟，直到將數個 Todo 項目新增至清單為止。

4. 按一下 [ **完成所有** ] 按鈕。

    ![](./media/mobile-services-android-call-custom-api/mobile-custom-api-android-completed.png)

    出現訊息對話方塊，指出標示為完成的項目數，並重新執行篩選查詢，以便清除清單的所有項目。

