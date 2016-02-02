<properties 
   pageTitle="Azure Mobile Engagement 使用者介面 - 我的帳戶" 
   description="了解如何使用 Azure Mobile Engagement 管理帳戶設定檔和測試裝置" 
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
   ms.date="11/22/2015"
   ms.author="piyushjo"/>


# 如何管理帳戶設定檔和測試裝置

本文說明 **Mobile Engagement** 入口網站的**首頁**。 使用 **Mobile Engagement** 入口網站可監視與管理您的行動應用程式。 請注意，若要開始使用入口網站，您必須先建立 **Azure Mobile Engagement** 帳戶。 如需詳細資訊，請參閱 [建立 Azure Mobile Engagement 帳戶](mobile-engagement-create-account.md)。

若要前往 [我的帳戶] 頁面上，按一下您位於頁面頂端的帳戶。

您可以在 UI 的 [我的帳戶] 區段檢視和變更與您帳戶相關聯的設定，包括您的設定檔設定和測試裝置識別碼。 這些設定所包含的項目，也可以透過裝置 API 存取。

![MyAccount1][7]

## 設定檔：

您可以檢視或變更下列任何帳戶設定，如下所示。 您也可以授與其他使用者權限來使用您的應用程式根據使用者的電子郵件地址，從 [首頁](mobile-engagement-interface-home.md)。

![MyAccount2][8]

## 裝置：

您可以檢視、新增或移除用來測試**觸達**或**推送**活動之測試裝置的測試裝置識別碼。 內容說明如何尋找每個平台裝置的裝置識別碼 (iOS、 Android、 Windows Phone 等。) 當您按一下 [新裝置] 會顯示。

![MyAccount3][9]

若要使用「推送 API」或「裝置 API」，您需要知道使用者的唯一裝置識別碼 (deviceid 參數)。 有幾種方法可以取得此識別碼：

1. 從您的後端，可以使用裝置 API 的 "Get" 功能來取得裝置識別碼的完整清單。
2. 從您的應用程式，可以使用 SDK 取得 (在 Android 上，呼叫 Agent 類別的 getDeviceID() 函數；在 iOS 上，讀取 Agent 類別的 deviceid 屬性)。
3. 從觸達通知，如果與通知相關聯的動作 URL 包含 {deviceid} 模式，它將會自動取代為觸發動作的裝置識別碼。
http://<example>.com/registeruser? deviceid = {deviceid} & xxxxxxxxxxxxxxxx&otherparam = myparamdata 
將會取代為：
http://<example>.com/registeruser? deviceid = XXXXXXXXXXXXXXXX xxxxxxxxxxxxxxxx&otherparam = myparamdata
4. 從觸達 Web 宣告，如果宣告的 HTML 程式碼包含 {deviceid} 模式，則會自動替換為顯示 Web 通知的裝置識別碼。
以下是我的裝置識別碼: {deviceid}
將會取代為：
以下是我的裝置識別碼: XXXXXXXXXXXXXXXX
5.  在您的裝置上開啟您的應用程式，然後執行應用程式中已被標記的事件。
依序從 UI - 您的應用程式 - [監視] - [事件] - [詳細資料]，在清單中尋找執行的事件。
在 [監視] 中按一下此事件。
您應該會在已執行此事件的裝置清單中找到裝置識別碼。
然後，您就可以複製此裝置識別碼，並且依序在 UI - [我的帳戶] - [裝置] - [新裝置] - [選取您的裝置平台] 中註冊此識別碼。
>(請注意，當 iOS 停用 IDFA 時，如果您解除安裝後又重新安裝您的應用程式，裝置識別碼可能會隨時間而變更)。

## 疑難排解指南

-  [疑難排解指南-服務 ][link 24]

## 另請參閱

-  [UI 文件-首頁 ][link 13]





[1]: ./media/mobile-engagement-user-interface-navigation/navigation1.png 
[2]: ./media/mobile-engagement-user-interface-home/home1.png 
[3]: ./media/mobile-engagement-user-interface-home/home2.png 
[4]: ./media/mobile-engagement-user-interface-home/home3.png 
[5]: ./media/mobile-engagement-user-interface-home/home4.png 
[6]: ./media/mobile-engagement-user-interface-home/home5.png 
[7]: ./media/mobile-engagement-user-interface-my-account/myaccount1.png 
[8]: ./media/mobile-engagement-user-interface-my-account/myaccount2.png 
[9]: ./media/mobile-engagement-user-interface-my-account/myaccount3.png 
[10]: ./media/mobile-engagement-user-interface-analytics/analytics1.png 
[11]: ./media/mobile-engagement-user-interface-analytics/analytics2.png 
[12]: ./media/mobile-engagement-user-interface-analytics/analytics3.png 
[13]: ./media/mobile-engagement-user-interface-analytics/analytics4.png 
[14]: ./media/mobile-engagement-user-interface-monitor/monitor1.png 
[15]: ./media/mobile-engagement-user-interface-monitor/monitor2.png 
[16]: ./media/mobile-engagement-user-interface-monitor/monitor3.png 
[17]: ./media/mobile-engagement-user-interface-monitor/monitor4.png 
[18]: ./media/mobile-engagement-user-interface-reach/reach1.png 
[19]: ./media/mobile-engagement-user-interface-reach/reach2.png 
[20]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign1.png 
[21]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign2.png 
[22]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign3.png 
[23]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign4.png 
[24]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign5.png 
[25]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign6.png 
[26]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign7.png 
[27]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign8.png 
[28]: ./media/mobile-engagement-user-interface-reach-campaign/Reach-Campaign9.png 
[29]: ./media/mobile-engagement-user-interface-reach-criterion/Reach-Criterion1.png 
[30]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content1.png 
[31]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content2.png 
[32]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content3.png 
[33]: ./media/mobile-engagement-user-interface-reach-content/Reach-Content4.png 
[34]: ./media/mobile-engagement-user-interface-dashboard/dashboard1.png 
[35]: ./media/mobile-engagement-user-interface-segments/segments1.png 
[36]: ./media/mobile-engagement-user-interface-segments/segments2.png 
[37]: ./media/mobile-engagement-user-interface-segments/segments3.png 
[38]: ./media/mobile-engagement-user-interface-segments/segments4.png 
[39]: ./media/mobile-engagement-user-interface-segments/segments5.png 
[40]: ./media/mobile-engagement-user-interface-segments/segments6.png 
[41]: ./media/mobile-engagement-user-interface-segments/segments7.png 
[42]: ./media/mobile-engagement-user-interface-segments/segments8.png 
[43]: ./media/mobile-engagement-user-interface-segments/segments9.png 
[44]: ./media/mobile-engagement-user-interface-segments/segments10.png 
[45]: ./media/mobile-engagement-user-interface-segments/segments11.png 
[46]: ./media/mobile-engagement-user-interface-settings/settings1.png 
[47]: ./media/mobile-engagement-user-interface-settings/settings2.png 
[48]: ./media/mobile-engagement-user-interface-settings/settings3.png 
[49]: ./media/mobile-engagement-user-interface-settings/settings4.png 
[50]: ./media/mobile-engagement-user-interface-settings/settings5.png 
[51]: ./media/mobile-engagement-user-interface-settings/settings6.png 
[52]: ./media/mobile-engagement-user-interface-settings/settings7.png 
[53]: ./media/mobile-engagement-user-interface-settings/settings8.png 
[54]: ./media/mobile-engagement-user-interface-settings/settings9.png 
[55]: ./media/mobile-engagement-user-interface-settings/settings10.png 
[56]: ./media/mobile-engagement-user-interface-settings/settings11.png 
[57]: ./media/mobile-engagement-user-interface-settings/settings12.png 
[58]: ./media/mobile-engagement-user-interface-settings/settings13.png 
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

