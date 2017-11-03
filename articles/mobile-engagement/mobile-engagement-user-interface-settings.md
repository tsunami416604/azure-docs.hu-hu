---
title: "Az Azure Mobile Engagement felhasználói felület – beállítások"
description: "A globális beállítások használata az Azure Mobile Engagement az alkalmazás kezelése"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 858f4cb4-14de-4bb5-826f-28cadbfc928b
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: af5c81df2b9f288161b38625d3ac2adde8fb195d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-manage-the-global-settings-of-your-application"></a>Az alkalmazás a globális beállítások kezelése
A **beállítások** menüpontok egy alkalmazás vary, az alkalmazás és az engedélyek, az alkalmazás rendelkezik platformtól függően érhető el. A beállítások a következőket tartalmazzák: részletek, projektek, natív leküldés, leküldési sebességét, Tag (app-info) és a kereskedelmi nyomás. A beállítások szakasz Tag (app-info) menüpont kezelheti az alkalmazás (az SDK használatával) vagy a kiszolgáló (az eszköz API-val). 

> [!NOTE]
> Sok szakasza a **a Mobile Engagement** portál felhasználói felületének tartalmaz a **megjelenítése SÚGÓ** gombra. Nyomja le az erre a gombra kattintva szakasz környezetfüggő ismertetése.
> 
> 

## <a name="details"></a>Részletek
Az alkalmazás és a szerepkörengedélyek a tulajdonos nevét és az alkalmazás leírását módosíthatja megtekintése. 

Elemzés konfiguráció lehetővé teszi a nap, hét indítsa el a és a nap és a megőrzési időtartam megtekintése és módosítása.

  ![settings1][46]

## <a name="projects"></a>Projektek
Az alkalmazás megjelenik az összes projekt kiválasztását teszi lehetővé. 

Keresse meg a projekt is, és megtekintheti a nevét, leírását, tulajdonos és a szerepköri jogosultságok bármely része az alkalmazás projekt.

További információkért lásd: [felhasználói felületének dokumentációja – kezdőlap][Link 13]

  ![settings3][48]

## <a name="native-push"></a>Natív leküldéssel
Lehetővé teszi a natív leküldéses regisztrálni egy új tanúsítványt vagy a delete és a meglévő tanúsítványt. Natív leküldéses lehetővé teszi, hogy az Azure Mobile Engagement leküldéses az alkalmazáshoz, tetszőleges időpontban, még ha az nem futna. 

Miután megadta a hitelesítő adatokat vagy tanúsítványokat legalább egy natív leküldés szolgáltatáshoz, kiválaszthatja a "Minden alkalommal" a LEKÜLDÉSES API elérését a kampányok, és használja a "bejelentő" paraméter létrehozásakor.

### <a name="apple-push-notification-service-apns"></a>Apple Push Notification szolgáltatás (APNS)
Az Apple Push Notification szolgáltatással natív leküldés engedélyezéséhez szüksége lesz a tanúsítvány regisztrálása. Akkor adja meg a tanúsítvány típusát (fejlesztés) fejlesztési vagy éles (termék). Majd kell feltölti a tanúsítványt, és a jelszót.

További információkért lásd:: [SDK-dokumentáció - (iOS) – az Apple leküldéses értesítések az alkalmazás előkészítése][Link 5]

![settings4][49]

### <a name="windows-push-notification-service-wpns"></a>A Windows leküldéses értesítéseket kezelő szolgáltatása (WPNS)
A Windows leküldéses értesítéseket kezelő szolgáltatással való natív leküldés engedélyezéséhez meg kell adnia az alkalmazás hitelesítő adatait. Szüksége lesz a csomag biztonsági azonosítóját (SID) és a titkos kulcsot.

![settings5][50]

### <a name="google-cloud-messaging-for-android-gcm"></a>A Google Cloud Messaging (GCM) Android rendszerhez
A GCM használatával natív leküldés engedélyezéséhez kövesse az utasításokat a Google kell. Ezután be kell illesztenie egy egyszerű kiszolgálói API-kulcsot, IP-korlátozások nélkül konfigurált. Szükséges integrációs az SDK-val Android v1.12.0 +.

További információkért lásd: 

* [SDK-dokumentáció Android GCM integrálása][Link 5]
* [Google fejlesztői GCM útmutató](http://developer.android.com/guide/google/gcm/gs.html)

### <a name="amazon-device-messaging-for-android-adm"></a>Amazon eszköz Messaging (ADM) Android rendszerhez
ADM használatával natív leküldés engedélyezéséhez meg kell adnia Amazon <OAuth credentials> egy ügyfél-azonosító és a titkos Ügyfélkulcs (az SDK-val való integráció szükséges Android v2.1.0 +).

További információkért lásd: 

* [SDK-dokumentáció Android ADM integrálása][Link 5]
* [Amazon fejlesztői ADM dokumentáció](https://developer.amazon.com/sdk/adm/credentials.html#Getting)

![settings6][51]

## <a name="push-speed"></a>Leküldési sebesség
Az alkalmazás aktuális leküldési sebességét jeleníti meg, és megadhatja az alkalmazás leküldési sebességét.

  ![settings7][52]

## <a name="tag-app-info"></a>Tag (app-info)
![settings11][56]

## <a name="commercial-pressure"></a>A kereskedelmi nyomás
![settings12][57]

## <a name="see-also"></a>Lásd még:
* [Alapfogalmak][Link 6]
* [Hibaelhárítási útmutató szolgáltatás][Link 24]

<!--Image references-->
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

<!--Link references-->
[Link 1]: mobile-engagement-user-interface.md
[Link 2]: mobile-engagement-troubleshooting-guide.md
[Link 3]: mobile-engagement-how-tos.md
[Link 4]: http://go.microsoft.com/fwlink/?LinkID=525553
[Link 5]: http://go.microsoft.com/fwlink/?LinkID=525554
[Link 6]: http://go.microsoft.com/fwlink/?LinkId=525555
[Link 7]: https://account.windowsazure.com/PreviewFeatures
[Link 8]: https://social.msdn.microsoft.com/Forums/azure/home?forum=azuremobileengagement
[Link 9]: http://azure.microsoft.com/services/mobile-engagement/
[Link 10]: http://azure.microsoft.com/documentation/services/mobile-engagement/
[Link 11]: http://azure.microsoft.com/pricing/details/mobile-engagement/
[Link 12]: mobile-engagement-user-interface-navigation.md
[Link 13]: mobile-engagement-user-interface-home.md
[Link 14]: mobile-engagement-user-interface-my-account.md
[Link 15]: mobile-engagement-user-interface-analytics.md
[Link 16]: mobile-engagement-user-interface-monitor.md
[Link 17]: mobile-engagement-user-interface-reach.md
[Link 18]: mobile-engagement-user-interface-segments.md
[Link 19]: mobile-engagement-user-interface-dashboard.md
[Link 20]: mobile-engagement-user-interface-settings.md
[Link 21]: mobile-engagement-troubleshooting-guide-analytics.md
[Link 22]: mobile-engagement-troubleshooting-guide-apis.md
[Link 23]: mobile-engagement-troubleshooting-guide-push-reach.md
[Link 24]: mobile-engagement-troubleshooting-guide-service.md
[Link 25]: mobile-engagement-troubleshooting-guide-sdk.md
[Link 26]: mobile-engagement-troubleshooting-guide-sr-info.md
[Link 27]: ../mobile-engagement-how-tos-first-push.md
[Link 28]: ../mobile-engagement-how-tos-test-campaign.md
[Link 29]: ../mobile-engagement-how-tos-personalize-push.md
[Link 30]: ../mobile-engagement-how-tos-differentiate-push.md
[Link 31]: ../mobile-engagement-how-tos-schedule-campaign.md
[Link 32]: ../mobile-engagement-how-tos-text-view.md
[Link 33]: ../mobile-engagement-how-tos-web-view.md

