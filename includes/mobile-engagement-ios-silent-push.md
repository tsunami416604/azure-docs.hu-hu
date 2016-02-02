> [AZURE.IMPORTANT] 若要從 Mobile Engagement 接收推播通知，您必須啟用 `遠端的無訊息通知` 應用程式中。 您需要將遠端通知值新增到 Info.plist 檔案中的 UIBackgroundModes 陣列。

1. 開啟 `info.plist` 專案中的檔案
2. 以滑鼠右鍵按一下清單中第一個項目 (`資訊屬性清單`)，並新增新的資料列

    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push1.png)

3. 新的資料列中輸入 `需要背景模式`

    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push2.png)

4. 按一下向左鍵以展開資料列
5. 將下列的值加入至項目 0 `應用程式下載內容推播通知以`

    ![](./media/mobile-engagement-ios-silent-push/xcode-plist-add-silent-push3.png)

6. 變更後，info.plist XML 應該包含下列索引鍵和值：

        <key>UIBackgroundModes</key>
        <array>
        <string>remote-notification</string>
        </array>

7. 如果您使用 **Xcode 7 +** 和 **iOS 9 +**：
    - 在 [目標] > [目標名稱] > [功能]，啟用 [推播通知]****。





