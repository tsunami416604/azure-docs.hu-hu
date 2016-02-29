本教學課程的最後階段是建立並執行新的應用程式。

### 將專案載入 Android Studio 並同步 Gradle

1. 瀏覽至您儲存此壓縮專案檔案的位置，並將檔案展開到您電腦上的 Android Studio 專案目錄。

2. 開啟 Android Studio。 如果您正在使用專案，而它出現的話，請關閉專案 ([檔案] => [關閉專案])。

3. 選取 **開啟現有的 Android Studio 專案**, ，瀏覽至專案位置，然後按一下 **[確定]。**這會載入專案並開始與 Gradle 同步。

    ![](./media/mobile-services-android-get-started/android-studio-import-project.png)

4. 等候 Gradle 同步活動完成。 您如果看到 "failed to find target" 錯誤訊息，這是因為 Android Studio 中使用的版本與範例的不相符。 若要修正此問題最簡單的方法是按一下 **安裝遺漏的平台和同步處理專案** 錯誤訊息中的連結。 您可能會收到其他的版本錯誤訊息，只要重複此程序直到沒有錯誤訊息出現即可。
    - 如果您想要執行「最新最強」的 Android 版本，則有另一個方法可以修正此問題。 您可以更新 **targetSdkVersion** 中 *build.gradle* 檔案中 *應用程式* 目錄，使其符合版本已安裝在您的電腦，您可以按一下 [探索 **SDK Manager** 圖示，以及查看哪一個版本會列出。 接著按 **同步處理專案與 Gradle 檔案**。 您可能會收到 Build Tools 的版本錯誤訊息，請用相同的方法來修復。

### 執行應用程式

您可以使用模擬器或實際裝置來執行 App。

1. 若要在裝置執行，使用 USB 纜線將裝置連接到電腦。 您必須 [設定開發裝置](https://developer.android.com/training/basics/firstapp/running-app.html)。 如果您在 Windows 電腦上開發，則必須也下載並安裝 USB 驅動程式。

2. 若要使用 Android 模擬器執行，您至少必須定義一個 Android 虛擬裝置 (AVD)。 按一下 [AVD Manager] 圖示來建立和管理這些裝置。

3. 從 **執行** ] 功能表上，按一下 [ **執行** 來啟動專案。 然後從出現的對話方塊選擇裝置或模擬器。

4. 應用程式出現時，請輸入有意義的文字，例如 _完成教學課程_, ，然後按一下 [ **新增**。

    ![](./media/mobile-services-android-get-started/mobile-quickstart-startup-android.png)

    如此會傳送 POST 要求到 Azure 中代管的新行動服務。 要求中的資料會插入 TodoItem 資料表中。 行動服務會傳回資料表中儲存的項目，而該資料會顯示在清單中。

    > [AZURE.NOTE] 您可以檢閱存取行動服務來查詢和插入資料，在 ToDoActivity.java 檔案中找到的程式碼。

8. 在 [Azure 傳統入口網站中，按一下 [ **資料** 標籤，然後按一下 **TodoItems** 資料表。

    ![](./media/mobile-services-android-get-started/mobile-data-tab1.png)

    如此可讓您瀏覽由應用程式插入資料表中的資料。

    ![](./media/mobile-services-android-get-started/mobile-data-browse.png)

