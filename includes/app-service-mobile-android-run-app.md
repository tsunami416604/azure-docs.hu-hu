
1. 請瀏覽 [Azure Portal]。 按一下 [ **全部瀏覽** > **行動應用程式** > 您剛剛建立的後端。 在行動裝置應用程式設定中，按一下 [ **快速入門** > **Android)**。 在 **設定用戶端應用**, ，按一下 [ **下載**。 這樣會為已預先設定成連接到您後端的應用程式，下載完整的 Android 專案。 

2. 開啟的專案使用 **Android Studio**, ，並使用 **（Eclipse ADT，Gradle 等） 的匯入專案**。 請確認選取此匯入選項，以避免發生 JDK 錯誤。

3. 按下 **執行 'app'** ] 按鈕以建置專案並在 Android 模擬器中啟動應用程式。

4. 在應用程式中，輸入有意義的文字，例如 _完成教學課程_ ，然後按一下 [新增] 按鈕。 這會將 POST 要求傳送至先前部署的 Azure 後端。 後端會將要求中的資料插入 TodoItem SQL 資料表，並將新儲存之項目的相關資訊傳回給行動應用程式。 行動應用程式會以清單顯示此資料。 

    ![](./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png)

[Azure Portal]: https://portal.azure.com/


