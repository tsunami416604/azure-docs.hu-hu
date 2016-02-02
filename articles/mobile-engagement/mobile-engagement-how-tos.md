<properties 
   pageTitle="Azure Mobile Engagement 使用者介面：觸達作法"
   description="Azure Mobile Engagement 的使用者介面概觀" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="05/12/2015"
   ms.author="piyushjo"/>


# 如何開始使用及管理推送通知以推送給使用者

一旦 SDK 完全整合至應用程式後，您便可以開始使用 UI 的 [Reach] 區段，將通知推送給應用程式使用者。

## 進行第一個推送通知活動

-    Confirm that your Reach is integrated into your app with the SDK. 

-    Select your application


![First1][1]

-    Go to the "Reach" Section and Click "New announcement"


![First2][2]

-    Create a new campaign and name it

 ![First3][3]

-    Select how the notification should be delivered, as In-app only


![First4][4]

-    Create the message you want to push


![First5][5]

-    You may write a title on the notification (Optional).

-    Write push message content.

-    You can upload an image. Be aware that the size of the file cannot exceed 32,768 bytes.

-    You also have the ability to select further options, but for the focus of this tutorial, we will see that later.

-    Select the content type as Notification only


![First6][6]

-    Create your push campaign and it will appear in your campaign list.


![First7][7]

## 測試推送通知活動

![Test1][8]

-    Register your device.

-    Click on the checkbox of the device you want to push.

-    Click on the "Test" button to send the push to the device.


![Test2][9]

-    Activate the campaign


![Test3][10]

-    Now that you have created your campaign you just need to activate it for the notification to be pushed to your users.


## 傳送個人化的推送

-    This example creates a push where a custom rebate code is entered into the push notification.


![Personalize1][11]

個人化運作方式是取代應用程式資訊標記中的標記，因此，您必須確定使用者已先定義適當的應用程式資訊。 在此範例中，目標使用者已定義名為 rebate_code 的應用程式資訊標記。
如您所見，在推送通知內容上方包含標記 ${rebate_code}，這表示它將由應用程式資訊標記的實際內容所取代。

> 警告：如果未定義使用者的應用程式資訊標記，使用者將不會收到推送內容。

-    Result


![Personalize2][12]

### 您可以進一步個人化通知的文字

![Personalize3][13]

-    Including the title of the notification,

-    And the content of the message.

-    Choose the type of announcement (Text view or Web view)


![Personalize4][14]

### 公告的內文也可以個人化：

-    The action URL, should you want to customize the landing page

-    The title,

-    The body of the message.



## 區分您的推送通知 (在應用程式內外)

-    Choose the type of notification you will push, select your application, go to the "Reach" section, select or create a push campaign and go to the "Notification" section.

-    Click on the "delivery mode" you want.

-    Click on the "Restrict Activities" checkbox when you want the notification occurs on specific activities (screens).


![Differentiate1][15]

### 「僅限應用程式外」傳遞模式

![Differentiate2][16]

「僅限應用程式外」傳遞模式會在應用程式關閉時提供推送通知。 這是標準的推送通知。
當您選取「僅限應用程式外」時，您必須已提供應用程式建置所在平台 (APN 或 GCM) 上的憑證。

### 另請參閱

-  [Apple 推播通知服務憑證](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), ，Google Cloud Messaging – 憑證] (http://developer.android.com/google/gcm/index.html)

### 「僅限應用程式內」傳遞模式

![Differentiate3][17]

「僅限應用程式內」傳遞模式會在應用程式執行時提供推送通知。
針對此通知，您不需要經歷 APNS 和 GCM 系統。
您可以使用應用程式內傳遞系統來觸達使用者。
您完全可以自訂通知，並決定通知將會出現在哪個活動 (畫面)。

### 「隨時」傳遞模式

您可以選擇「隨時」傳遞模式，確保您不論應用程式是否正在執行都可觸達使用者。
當您選取「隨時」時，您必須已提供應用程式建置所在平台 (APN 或 GCM) 上的憑證。

## 排定推送活動

### 計畫開始活動

![Shedule1][18]

時間是 3 月 21 日，您要進行公告，且計畫是在 3 月 22 日午夜。 
您不需要待在介面前就可以推送！ 您可以事先規劃確切在哪一分鐘會傳送通知。
-    Un-check the "None" checkbox and select a start time 

-    Choose the date and the time you want to start the push campaign.


### 計畫結束活動

![Shedule2][19]

您想要在 3 月 25 日下午 3 點停止活動，但您知道您到時候不會在場。
您不需要待在介面前就可以推送！ 您可以事先規劃活動確切在哪一分鐘停止。
-    Click on the "None" checkbox or select a end time

-    Choose the date and the time you want to finish the push campaign.


### 手動結束活動

![Shedule3][20]

根據預設，已選取 [無] 核取方塊。
這表示只要在 [Reach] 區段啟動活動它便會開始，在 [Reach] 區段停止它便會結束。

> 注意：建立時若未設定活動的結束日期，推送內容會儲存在本機裝置上，並在下一次應用程式開啟時顯示，即使活動已手動結束也會顯示。

## 使用文字檢視增強推送通知

### 什麼是文字檢視？

![TextView1][21]

文字檢視是具有文字內容的快顯視窗。 使用者按下推送通知之後，就會出現這個快顯視窗。
文字檢視可讓您呈現更多的內容給使用者。 這也是呈現動作呼叫的機會，例如跳至您的應用程式頁面、重新導向至市集、開啟網頁、傳送電子郵件、開始地理當地語系化的搜尋等等...

### 範例：文字檢視

-    Create your Push notification campaign in the "Reach" section and give your campaign a name


![TextView2][22]

-    Write the message that will appear on the notification.

-    Select the Announcement Content Type of “text”


![TextView3][23]

> 注意：當您推送文字檢視時，它一律是先有通知。

- 定義文字 (選取文字公告內容之後，子區段會出現，可讓您定義您想要顯示的文字)。

![TextView4][24]

-    Write the title that will appear at the top of the message.

-    Write the main content of the text view.

-    Write the content that will appear on the action button (an action button enables the application to make a specific action such as opening a page of the application, redirecting to an App store or any kind of sources you can provide).

-    Write the content that will appear on the exit button (by clicking on the exit button, the text view will disappear.)

-    Create your push notification campaign and it will appear on the campaign list.


![TextView5][25]

-    Activate your push notification campaign to send the text view to your users.


![TextView6][26]

-    Result


![TextView7][27]

-    The user receives the notification and click on it.

-    The text view appears as a pop-up allowing the user to interact with it.


## 使用網頁檢視增強推送通知

### 什麼是網頁檢視？

![WebView1][28]

網頁檢視是具有網頁內容的快顯視窗。 使用者按下推送通知時，就會出現這個快顯視窗。
網頁檢視可讓您與使用者有更多互動。
這也是呈現動作呼叫的機會，例如重新導向至應用程式市集、開啟網頁、傳送電子郵件、開始地理當地語系化的搜尋等等...

### 範例：網頁檢視

-    Create your Push campaign in the "Reach" section and give your campaign a name.


![WebView2][29]

-    Write the message that will appear on the notification.

-    Select the Announcement Content Type as “web”


![WebView3][30]

### 關於公告類型：

- 僅通知：這是簡單的標準通知。 表示如果使用者按一下通知，不會出現任何其他檢視，但是會發生與它關聯的動作。
- 文字通知：這是一種吸引使用者查看文字檢視的通知。
- Web 通知：這是一種吸引使用者查看 Web 檢視的通知。
選取「網頁公告」內容。

> 注意：當您推送網頁檢視時，其一律會先有通知。

- 定義網頁內容 (選取網頁公告內容之後，子區段會出現，可讓您定義您想要顯示的網頁檢視內容)。


![WebView4][31]

-    Write the title that will appear at the top of the message (optional).

-    Write your HTML code here.

-    Click on the source editing mode button to switch edition and see how it looks like.

-    Write the content that will appear on the action button (an action button enables the application to make a specific action such as opening a page of the application, redirecting to a Store or any kind of sources you can provide).

-    Write the content that will appear on the exit button (by clicking on the exit button, the web view will disappear).

-    Result


![WebView5][32]

-    The user receive the notification and click on it.

-    The text view appears as a pop-up allowing the user to interact with it.





[1]: ./media/mobile-engagement-how-tos/First1.png 
[2]: ./media/mobile-engagement-how-tos/First2.png 
[3]: ./media/mobile-engagement-how-tos/First3.png 
[4]: ./media/mobile-engagement-how-tos/First4.png 
[5]: ./media/mobile-engagement-how-tos/First5.png 
[6]: ./media/mobile-engagement-how-tos/First6.png 
[7]: ./media/mobile-engagement-how-tos/First7.png 
[8]: ./media/mobile-engagement-how-tos/Test1.png 
[9]: ./media/mobile-engagement-how-tos/Test2.png 
[10]: ./media/mobile-engagement-how-tos/Test3.png 
[11]: ./media/mobile-engagement-how-tos/Personalize1.png 
[12]: ./media/mobile-engagement-how-tos/Personalize2.png 
[13]: ./media/mobile-engagement-how-tos/Personalize3.png 
[14]: ./media/mobile-engagement-how-tos/Personalize4.png 
[15]: ./media/mobile-engagement-how-tos/Differentiate1.png 
[16]: ./media/mobile-engagement-how-tos/Differentiate2.png 
[17]: ./media/mobile-engagement-how-tos/Differentiate3.png 
[18]: ./media/mobile-engagement-how-tos/Schedule1.png 
[19]: ./media/mobile-engagement-how-tos/Schedule2.png 
[20]: ./media/mobile-engagement-how-tos/Schedule3.png 
[21]: ./media/mobile-engagement-how-tos/TextView1.png 
[22]: ./media/mobile-engagement-how-tos/TextView2.png 
[23]: ./media/mobile-engagement-how-tos/TextView3.png 
[24]: ./media/mobile-engagement-how-tos/TextView4.png 
[25]: ./media/mobile-engagement-how-tos/TextView5.png 
[26]: ./media/mobile-engagement-how-tos/TextView6.png 
[27]: ./media/mobile-engagement-how-tos/TextView7.png 
[28]: ./media/mobile-engagement-how-tos/WebView1.png 
[29]: ./media/mobile-engagement-how-tos/WebView2.png 
[30]: ./media/mobile-engagement-how-tos/WebView3.png 
[31]: ./media/mobile-engagement-how-tos/WebView4.png 
[32]: ./media/mobile-engagement-how-tos/WebView5.png 
[link 1]: mobile-engagement-user-interface.md 
[link 2]: mobile-engagement-troubleshooting-guide.md 
[link 3]: mobile-engagement-how-tos.md 
[link 4]: http://go.microsoft.com/fwlink/?LinkID=525553 
[link 5]: http://go.microsoft.com/fwlink/?LinkID=525554 
[link 6]: http://go.microsoft.com/fwlink/?LinkId=525555 
[link 7]: https://account.windowsazure.com/PreviewFeatures 
[link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement 
[link 9]: http://azure.microsoft.com/services/mobile-engagement/ 
[link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/ 
[link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/ 
[link 12]: mobile-engagement-user-interface-navigation.md 
[link 13]: mobile-engagement-user-interface-home.md 
[link 14]: mobile-engagement-user-interface-my-account.md 
[link 15]: mobile-engagement-user-interface-analytics.md 
[link 16]: mobile-engagement-user-interface-monitor.md 
[link 17]: mobile-engagement-user-interface-reach.md 
[link 18]: mobile-engagement-user-interface-segments.md 
[link 19]: mobile-engagement-user-interface-dashboard.md 
[link 20]: mobile-engagement-user-interface-settings.md 
[link 21]: mobile-engagement-troubleshooting-guide-analytics.md 
[link 22]: mobile-engagement-troubleshooting-guide-apis.md 
[link 23]: mobile-engagement-troubleshooting-guide-push-reach.md 
[link 24]: mobile-engagement-troubleshooting-guide-service.md 
[link 25]: mobile-engagement-troubleshooting-guide-sdk.md 
[link 26]: mobile-engagement-troubleshooting-guide-sr-info.md 
[link 27]: ../mobile-engagement-how-tos-first-push.md 
[link 28]: ../mobile-engagement-how-tos-test-campaign.md 
[link 29]: ../mobile-engagement-how-tos-personalize-push.md 
[link 30]: ../mobile-engagement-how-tos-differentiate-push.md 
[link 31]: ../mobile-engagement-how-tos-schedule-campaign.md 
[link 32]: ../mobile-engagement-how-tos-text-view.md 
[link 33]: ../mobile-engagement-how-tos-web-view.md 

