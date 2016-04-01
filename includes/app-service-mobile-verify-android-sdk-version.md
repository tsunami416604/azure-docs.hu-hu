由於持續進行開發，因此 Android Studio 中安裝的 Android SDK 版本可能與程式碼中的版本不相符。 此教學課程參照的 Android SDK 為 23 版，是撰寫本文時的最新版本。 隨著新修訂版 SDK 發行，版本號碼可能會隨之增加，我們建議您使用最新的可用版本。

版本不符合的兩個徵兆為：

1. 當您建置或重新建置專案時，您可能會收到之類的 Gradle 錯誤訊息 「**找不到目標 Google inc.不**」。

2. 錯誤訊息可能是程式碼中必須根據 `import` 陳述式解析的標準 Android 物件所產生。

若出現其中任一項，則 Android Studio 中安裝的 Android SDK 版本可能與下載專案的 SDK 目標不相符。  若要確認版本，請進行下列變更：


1. 在 Android Studio 中，按一下 [ **工具** = > **Android** = > **SDK Manager**。 若尚未安裝最新版的 SDK 平台，則按一下以安裝它。 記下版本號碼。

2. 在 [專案總管] 索引標籤中，在 **Gradle 指令碼**, ，開啟檔案 **build.gradle (modeule ︰ 應用程式)**。 請確認 **compileSdkVersion** 和 **buildToolsVersion** 設定最新安裝的 SDK 版本。 標記外觀如下：
 
            compileSdkVersion 'Google Inc.:Google APIs:23'
            buildToolsVersion "23.0.0"
    
3. 在 Android Studio 專案總管] 中以滑鼠右鍵按一下專案節點，選擇 [ **屬性**, ，然後在左欄中選擇 **Android**。 請確認 **專案建置目標** 設為相同的 SDK 版本 **targetSdkVersion**。

4. 與 Eclipse 的情況不同，在 Android Studio 中，不再使用資訊清單檔案來指定目標 SDK 和最低的 SDK 版本。


