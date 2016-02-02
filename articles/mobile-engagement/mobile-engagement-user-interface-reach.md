<properties 
   pageTitle="Azure Mobile Engagement 使用者介面 - 觸達" 
   description="了解如何使用 Azure Mobile Engagement 透過推播通知觸及應用程式的使用者" 
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
   ms.date="11/29/2015"
   ms.author="piyushjo"/>



# 如何透過推播通知觸及應用程式的使用者

本文說明 **Mobile Engagement** 入口網站的 [觸達]**** 索引標籤。 使用 **Mobile Engagement** 入口網站可監視與管理您的行動應用程式。 請注意，若要開始使用入口網站，您必須先建立 **Azure Mobile Engagement** 帳戶。 如需詳細資訊，請參閱 [建立 Azure Mobile Engagement 帳戶](mobile-engagement-create-account.md)。

UI 的 [觸達] 區段是推送活動管理工具，您可以在此建立/編輯/啟動/完成/監視推播通知活動，並取得推播通知活動的統計資料，您也可以透過「觸達 API」(和低階推送 API 的部份元素) 存取這些功能。 請記住，無論您使用 API 或 UI，都必須先使用 SDK 針對每個平台將 Azure Mobile Engagement 和觸達整合到您的應用程式，才能使用觸達活動。
>[AZURE.NOTE] 許多 **Mobile Engagement** 入口網站 UI 的區段包含 [顯示說明]**** 按鈕。 按下此按鈕，以獲得關於區段的詳細內容資訊。


## 推播通知的四種類型

1.    Announcements - allow you to send advertising messages to users that redirect them to another location inside your app or to send them to a webpage or store outside of your app. 

2.    Polls - allow you to gather information from end users by asking them questions.

3.    Data Pushes - allow you to send a binary or base64 data file. The information contained in a data push is sent to your application to modify your users' current experience in your app. Your application needs to be able to process the data in a data push.



## 每個活動會顯示三種即時統計資料

1.    Pushed - how many pushes were sent based on the criteria specified in the campaign. 

2.    Replied - how many users reacted to the notification by either opening it from outside of app or closing it in the app. 

3.    Actioned - how many users clicked on the link in the notification to be redirected to a new location in the app, to a store, or to a web browser. 


> [AZURE.NOTE]您可以透過 [觸達 API Stats] 取得更詳細的活動統計資料。



## 活動詳細資料

您可以停留在活動名稱上或按一下以開啟活動，來編輯、複製、刪除活動或啟動尚未啟動的活動。 您可以停留在活動名稱上或按一下以開啟活動，來複製已經啟動的活動。 不過，活動一旦啟動，您就無法變更。

![Reach1][18]

## 觸達意見反應

若要查看行銷活動的詳細資料或統計資料，請按一下項目。 接著，您可以從詳細資料切換至已啟動之開啟活動的統計檢視，並且將統計資料從簡單檢視切換到進階檢視，以檢視更詳細的資訊 (視您的權限而定)。 您也可以使用來自先前活動的觸達意見反應資訊，做為新活動的目標設定準則。 [觸達意見反應] 統計資料也可以透過「觸達 API」的 **Stats** 收集。 您也可以根據先前的活動，自訂推送活動的對象。


![Reach2][19]


## 另請參閱

- [概念 ][link 6]
- [疑難排解節目表服務 ][link 24]




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
[link 27]: mobile-engagement-user-interface-reach-campaign.md 
[link 28]: mobile-engagement-user-interface-reach-criterion.md 
[link 29]: mobile-engagement-user-interface-reach-content.md 

