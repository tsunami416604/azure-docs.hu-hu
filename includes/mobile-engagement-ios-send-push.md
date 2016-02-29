###將推播憑證的存取權授與給 Mobile Engagement

若要讓 Mobile Engagement 以您的名義傳送推播通知，您需要授與它對您憑證的存取權。 這是藉由設定和輸入您的憑證到 Mobile Engagement 入口網站中而完成。 請確定您取得.p12 憑證中所述 [Apple 的文件](https://developer.apple.com/library/prerelease/ios/documentation/IDEs/Conceptual/AppDistributionGuide/AddingCapabilities/AddingCapabilities.html#//apple_ref/doc/uid/TP40012582-CH26-SW6)

1. 瀏覽至您的 Mobile Engagement 入口網站。 請確定您使用正確，然後按一下 [ **Engage** 底部的按鈕:

    ![](./media/mobile-engagement-ios-send-push/engage-button.png)

2. 按一下 [ **設定** Engagement 入口網站中的頁面。 從該處按一下 **原生推送** 區段以上傳 p12 憑證:

    ![](./media/mobile-engagement-ios-send-push/engagement-portal.png)

3. 選取您的 p12，將它上傳並輸入您的密碼：

    ![](./media/mobile-engagement-ios-send-push/native-push-settings.png)

##<a id="send"></a>將通知傳送至您的應用程式

現在，我們將建立簡單的推播通知活動，它會傳送推播至我們的應用程式：

1. 瀏覽至 **到達** 中您的 Mobile Engagement 入口網站] 索引標籤。

2. 按一下 [ **新公告** 建立推送活動

    ![](./media/mobile-engagement-ios-send-push/new-announcement.png)

3. 設定行銷活動的第一個欄位：

    ![](./media/mobile-engagement-ios-send-push/campaign-first-params.png)

    -   提供 **名稱** 活動 
    -   選取 **傳遞時間** 為 **僅限應用程式超出**: 這是主要為一些文字的簡單 Apple 推播通知類型。
    -   在通知文字中，先輸入 **標題** 這將成為推送第一行。
    -   然後輸入您 **訊息** 這將會是第二行

4. 向下的捲動，在內容] 區段中選取 **僅限通知**

    ![](./media/mobile-engagement-ios-send-push/campaign-content.png)

5. 您已完成最基本的行銷活動設定。 現在向下捲動並按一下 **建立** ] 按鈕以儲存您的推送通知活動。 

6. 最後-按一下 **啟動** 傳送推播通知。 

    ![](./media/mobile-engagement-ios-send-push/campaign-activate.png)

7. 您會在 iOS 裝置上的通知中心中收到類似下列的通知：

    ![](./media/mobile-engagement-ios-send-push/iphone-notification.png)

8. 若您擁有與此 iOS 裝置配對的 Apple Watch，您將會在 Apple Watch 上看見該通知：

    ![](./media/mobile-engagement-ios-send-push/apple-watch.png)


 

 
