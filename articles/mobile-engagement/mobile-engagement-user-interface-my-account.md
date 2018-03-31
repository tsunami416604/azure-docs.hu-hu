---
title: Az Azure Mobile Engagement felhasználói felület - fiókomat
description: Az Azure Mobile Engagement segítségével fiók profil és a vizsgálati eszközök kezelése
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 22832678-3959-4b8c-9fb2-f2ff5974e5d1
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 89ef4f97a03ab0334f5474fb5b0ad2fd44f23518
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-manage-your-account-profile-and-test-devices"></a>A fiók profil és a vizsgálati eszközök kezelése
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

Ez a cikk ismerteti a **Home** oldalán a **a Mobile Engagement** portálon. Használja a **a Mobile Engagement** portal felügyeletét és kezelését a mobile apps szolgáltatásban. 

Eléréséhez a **fiókomat** lapján kattintson az oldal tetején a fiókhoz.

A felhasználói felület saját fiók szakasza, ahol megtekintheti és módosíthatja a beállításokat a fiókjához, beleértve a profil beállításait és eszközazonosítókat tesztelése. Ezek a beállítások az eszköz API-n keresztül is elérhető elemeket tartalmazhat.

![MyAccount1][7]  

## <a name="profile"></a>Profil:
Megtekintheti vagy módosíthatja az alább látható fiók beállításait. Egy másik felhasználónak engedélyt használhassa az alkalmazást, az e-mail cím a alapján is biztosíthat a [Home](mobile-engagement-user-interface-home.md).

![MyAccount2][8]  

## <a name="devices"></a>Eszközök:
Megtekintheti, adja hozzá, vagy távolítsa el a vizsgálati eszközök teszteléséhez használható eszközök eseményazonosítók tesztelése a **elérni** vagy **leküldéses** kampányok. Környezetfüggő utasításokkal szolgál az Eszközazonosítót az eszközök az egyes platformokon található (iOS, Android, Windows Phone, stb.) Ha kattintson az "Új eszköz" jelennek meg. 

![MyAccount3][9]  

A leküldéses API vagy a Device API-val kell tudni, hogy a felhasználók az eszköz egyedi azonosítója (deviceid paraméter). Több módon is lekéréséhez:

1. A háttérrendszerből a Device API-val a "Get" funkciója segítségével eszközazonosítók teljes listájának.
2. Az alkalmazásból az SDK segítségével is. (Az Android, az ügynök osztályt, és IOS rendszerű eszközökön, olvassa el a az ügynök osztály DeviceID getDeviceID() függvény hívható.)
3. A Reach-közlemények Ha a műveleti URL-cím a közlemény társított {deviceid} mintát tartalmaz, automatikusan felülírja az időt. a művelet eszköz azonosítója.
   http://<example>.com/registeruser? deviceid = {deviceid} & otherparam = myparamdata cseréli: http://<example>.com/registeruser? deviceid = XXXXXXXXXXXXXXXX & otherparam = myparamdata 
4. A Reach-webalkalmazás közlemények Ha a közlemény a HTML-kódot tartalmaz {deviceid} mintát azt automatikusan felülírja a webes hirdetmény megjelenítő eszköz azonosítója.
   Ez az eszközazonosító: {deviceid} cseréli: Ez az eszközazonosító: XXXXXXXXXXXXXXXX
5. Indítsa el az alkalmazást az eszközön, és végezze el az alkalmazást, amely címkézett esemény.
   A "Részletek – az alkalmazás - figyelő - események - UI" az esemény hajtotta végre, ha a listában található.
   Ez a figyelő az esemény kattintva.
   Keresse meg az Eszközazonosítót az eszközöket, amelyek végeztek el ezt az eseményt a listájában.
   Ezután másolja le az eszköz Azonosítót, és regisztrálja azt a "UI - fiókom - eszközök – új eszköz - kiválasztása eszközplatformhoz".
   >(Vegye figyelembe, hogy ha idfa-JÁT le van tiltva az iOS, az Eszközazonosítót az idő múlásával választva módosíthatja távolítsa el, majd telepítse újra az alkalmazást.)

## <a name="troubleshooting-guide"></a>Hibaelhárítási útmutatója
* [Hibaelhárítási útmutató - szolgáltatás][Link 24]

## <a name="see-also"></a>Lásd még
* [Felhasználói felületének dokumentációja – kezdőlap][Link 13]

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




