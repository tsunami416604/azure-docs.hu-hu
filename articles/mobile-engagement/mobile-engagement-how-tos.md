---
title: Az Azure Mobile Engagement felhasználói felület - a Reach hogyan
description: Felhasználói felület és Azure Mobile Engagement áttekintése
services: mobile-engagement
documentationcenter: ''
author: piyushjo
manager: erikre
editor: ''
ms.assetid: 30af87e6-c816-4cce-8609-6cbd3e83de14
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: 0c536d378023ee4a9675bb2d67262fb485c8052b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2018
---
# <a name="how-to-get-started-using-and-managing-pushes-to-reach-out-to-your-end-users"></a>Első lépések, használata és kezelése a végfelhasználók számára elérhetők a leküldéses értesítések
> [!IMPORTANT]
> Az Azure Mobile Engagement kivonja a 3/31/2018. Ezen a lapon hamarosan után törlődni fog.
> 

Ha az SDK az alkalmazás teljesen integrálva van, is használatának megkezdésében a leküldéses értesítések küldéséhez a felhasználók számára az alkalmazás a felhasználói felület Reach szakasza.  

## <a name="do-your-first-push-notification-campaign"></a>Hajtsa végre az első leküldéses Értesítéses kampány
* Győződjön meg arról, hogy a Reach integrálva van az alkalmazás az SDK-val. 
* Az alkalmazás kiválasztása

![First1][1]

* Nyissa meg az "Reach" szakaszban, és kattintson a "közlemény"

![First2][2]

* Hozzon létre egy új kampányt, és adjon neki nevet
  
![First3][3]

* Válassza ki, hogyan az értesítés kézbesítési, mint alkalmazásbeli csak

![First4][4]

* A leküldéses üzenet létrehozása

![First5][5]

* A cím írhat az értesítésre kattinthat (nem kötelező).
* Írás a leküldéses üzenet tartalma.
* Lemezkép feltöltése Vegye figyelembe, hogy a fájl mérete nem haladhatja meg 32768 bájt.
* Akkor is választhatók ki további beállításokat, de ez az oktatóanyag a fókusz, az azt láthatja, hogy később.
* Jelölje ki a tartalomtípus csak értesítés

![First6][6]

* A leküldéses kampány létrehozásához, és akkor jelenik meg a kampány listáján.

![First7][7]

## <a name="test-your-push-notification-campaign"></a>A leküldéses értesítési kampány tesztelése
![Test1][8]

* Regisztrálja az eszközt.
* Kattintson az eszköz kíván leküldeni a jelölőnégyzetet.
* Kattintson a "Test" gombra a leküldéses küldeni az eszközön.

![Test2][9]

* Aktiválja a kampányt.

![Teszt3][10]

* Most, hogy létrehozta a kampány csak azt az értesítés a felhasználóknak leküldött aktiválni kell.

## <a name="send-personalized-pushes"></a>Személyre szabott leküldéses értesítések küldése
* Ez a példa egy leküldéses, ha egy egyéni visszatérítési kódja bekerültek a leküldéses értesítési hoz létre.

![Personalize1][11]

Személyre szabás működésével úgy lecserélésével a mutató által egy app-info címke a akkor kell győződjön meg arról, hogy a felhasználó rendelkezik-e a megfelelő app-info első. Ebben a példában a megcélzott felhasználók rendelkeznek egy meghatározott rebate_code nevű alkalmazás info címke.
Ahogy fent látni a leküldéses értesítési tartalom a jelölő ${rebate_code}, amely tájékoztatja arról, hogy azt a tényleges tartalom a app-info címke által cserélni kívánt magában foglalja.

> [!WARNING]
> Ha a felhasználó nincs definiálva a app-info címke, a felhasználó nem kap a leküldéses.

* Eredmény

![Personalize2][12]

### <a name="you-can-further-personalize-the-text-your-notification"></a>További személyre szabhatja a szöveget az értesítés
![Personalize3][13]

* Az értesítés címe
* És az üzenet tartalma.
* Válassza ki a közlemény (szöveges vagy webes nézetben)

![Personalize4][14]

### <a name="the-body-of-an-announcement-may-also-be-personalized-with"></a>A szervezet bejelentés előfordulhat, hogy is az általunk:
* A műveleti URL-cím kell szeretné testre szabni a kezdőlapja
* A cím
* Az üzenet törzsét.

## <a name="differentiate-your-push-notification-in-or-out-of-app"></a>A leküldéses értesítési megkülönböztetéséhez (a vagy alkalmazásból)
* Válassza ki az értesítési fog leküldéses, válassza ki az alkalmazását, nyissa meg a "Elérni" részt, vagy a leküldéses kampány létrehozásához, és nyissa meg az "Értesítések" szakaszhoz.
* Kattintson a "szállítási mód" szeretné.
* Kattintson a "Korlátozása tevékenységek" jelölőnégyzetet, ha azt szeretné, hogy az értesítést az adott tevékenységei (képernyői) történik.

![Differentiate1][15]

### <a name="out-of-app-only-delivery-mode"></a>"Csak az alkalmazáson kívül" szállítási mód
![Differentiate2][16]

"Csak az alkalmazáson kívül" kézbesítési módot biztosít a leküldéses értesítést, ha az alkalmazáshoz be van zárva. Ez az a szabványos leküldéses értesítést.
Kiválasztásakor "csak az alkalmazáson kívül" kell már megadta a platform, amely az alkalmazás (APNS vagy GCM) van építve a tanúsítványokat.

### <a name="see-also"></a>Lásd még
* [Apple Push Notification szolgáltatás – tanúsítványok](http://developer.apple.com/library/mac/#documentation/NetworkingInternet/Conceptual/RemoteNotificationsPG/ApplePushService/ApplePushService.html#//apple_ref/doc/uid/TP40008194-CH100-SW9), [Google Cloud Messaging – tanúsítvány](http://developer.android.com/google/gcm/index.html) 

### <a name="in-app-only-delivery-mode"></a>"az alkalmazáson belüli csak" szállítási mód
![Differentiate3][17]

"Alkalmazásbeli csak" szállítási mód leküldéses értesítéseket biztosít, az alkalmazás futtatásakor.
Erre az értesítésre nem kell végighaladnia az APNS-GCM rendszer.
Az alkalmazáson belüli kézbesítési rendszer használhatja, ha a végfelhasználók eléréséhez.
Teljes körű testre szabhatja az értesítés, és döntse el, ahol tevékenység (képernyő) az értesítés jelenik meg.

### <a name="anytime-delivery-mode"></a>"Bármikor" szállítási mód
Kiválaszthatja az "Bármikor" szállítási módban, biztosítja, hogy elérni a végfelhasználó használni tudja, hogy az alkalmazás fut-e.
"Bármikor" kiválasztásakor kell már megadta a tanúsítványokat a platform, amely az alkalmazás van építve a (APNS vagy GCM). 

## <a name="schedule-a-push-campaign"></a>A leküldéses kampány ütemezése
### <a name="plan-to-start-a-campaign"></a>Tervezze meg a kampány indítására
![Shedule1][18]

A 21 a március, és rendelkezik egy értesítést, hogy tervezett a a 22. a március éjfélkor. Nem kell egy leküldéses ehhez a felület elé maradnak! Megtervezheti, hogy előzetesen a pontos percet a rendszer értesítést küld.

* UN-ellenőrzése a "None" jelölőnégyzetet, és válassza ki a kezdési idő 
* Válassza ki a dátum és időpont elindítja a leküldéses kampány.

### <a name="plan-to-end-a-campaign"></a>Tervezze meg a kampány befejezéséhez
![Shedule2][19]

Azt szeretné, hogy a kampány du. 3:00 meg a 25 a március leállítására, de Ön tudja róla, hogy akkor van rá.
A felület leküldéses elé marad nincs! Megtervezheti, hogy előzetesen a pontos percet a kampány befejeződik.

* Kattintson a "None" a jelölőnégyzet jelölését, vagy válasszon egy befejezési időpontja
* Válassza ki a dátum és időpont befejezi a leküldéses kampány.

### <a name="end-a-campaign-manually"></a>A kampány manuálisan végén
![Shedule3][20]

Alapértelmezés szerint a "None"-jelölőnégyzeteket.
Ez azt jelenti, hogy a kampány megkezdődik, amint aktiválja a reach szakaszban, és amikor a reach szakasz leáll.

> [!NOTE]
> A záró dátum nélküli létrehozott kampányok a leküldéses helyileg tárolja az eszközön, és szerepel, hogy a következő megnyitásakor az alkalmazás akkor is, ha a kampány manuálisan véget ér.

## <a name="enhance-a-push-notification-with-a-text-view"></a>A szöveges nézet a leküldéses értesítés javítása
### <a name="what-is-a-text-view"></a>Mi az a szöveg nézetet?
![TextView1][21]

A szöveges nézet olyan szöveges tartalommal előugró ablak. Az előugró ablak akkor jelenik meg, miután a végfelhasználó a leküldéses értesítésre kattintott.
A szöveges nézet lehetővé teszi a végfelhasználó használni tudja a további tartalmat. Ez egyben a lehetőség van a művelet, például az alkalmazás-Áruházbeli, a weblap megnyitásakor egy földrajzi helyét keresés indítása e-mail, küldés átirányítása egy lap Ugrás hívása stb...

### <a name="example-text-view"></a>Példa: Szöveges nézet
* A leküldéses értesítési kampány létrehozásához a "Reach" szakaszban, és nevezze el a kampány

![TextView2][22]

* Az üzenet, amely megjelenik majd írni az értesítésre kattinthat.
* Válassza ki a "text" tartalomtípusa bejelentés

![TextView3][23]

> [!NOTE]
> Amikor a szöveges nézet, azt mindig tartalmaz egy értesítési először. 

* Adja meg a szöveget (kiválasztását követően a szöveg közlemény tartalmát, a szakasz jelenik meg, lehetővé téve adja meg a megjeleníteni kívánt szöveg.)

![TextView4][24]

* A cím az üzenet tetején megjelenő írni.
* A szöveges nézet fő tartalmának írása.
* Írjon a tartalmat, amely megjelenik majd az akciógombra kattinthat (Akciógomb lehetővé teszi, hogy az alkalmazás megfelelően egy adott művelet, például az alkalmazás, az alkalmazás-áruházra vagy bármilyen típusú források megadhat egy lap megnyitása).
* Írás a tartalmat, amely megjelenik majd a Kilépés gombra (a Kilépés gombra kattintva, a szöveges nézet eltűnik.)
* A leküldéses értesítési kampány létrehozásához, és akkor jelenik meg a kampány listában.

![TextView5][25]

* Aktiválja a leküldéses értesítési kampány történő küldéséhez a szöveges nézet a felhasználók számára.

![TextView6][26]

* Eredmény

![TextView7][27]

* A felhasználó kap a értesítési, majd kattintson rá.
* A szöveges nézet jelenik meg egy előugró ablak, a felhasználó használhatja őket.

## <a name="enhance-a-push-notification-with-a-web-view"></a>A webes nézet a leküldéses értesítés javítása
### <a name="what-is-a-web-view"></a>Mi az a webes nézet?
![WebView1][28]

A webes nézet egy előugró ablak webtartalmakkal együtt. Ez az előugró ablak akkor jelenik meg, ha a végfelhasználó a leküldéses értesítésre kattintott.
A webes nézet több interakciót tesz lehetővé a végfelhasználóval.
Ez az App Store áruházból, például az átirányítási teendő bevezetését lehetőséget is egy weblap megnyitásakor, egy e-mailek küldéséhez, a földrajzi helyét keresés indítása stb...

### <a name="example-web-view"></a>Példa: Webes nézet
* A leküldéses kampány létrehozásához a "Reach" szakaszban, és nevezze el a kampányt.

![WebView2][29]

* Az üzenet, amely megjelenik majd írni az értesítésre kattinthat.
* Jelölje ki a közlemény tartalomtípus "web"

![WebView3][30]

### <a name="about-announcement-types"></a>Kapcsolatos bejelentés típusok:
* Csak értesítés: egy egyszerű standard szintű értesítési. Azt jelenti, hogy ha a felhasználó kattint, további nézet nélkül jelenik meg, de csak a hozzá tartozó műveletet hajtja végre.
* Szöveg közlemény: egy értesítés, amely kapcsolatba lép a felhasználó számára a szöveges nézet, tekintse meg a legyen.
* Webes hirdetmény: egy értesítés, amely kapcsolatba lép a felhasználó számára, tekintse meg a következő a webes nézet.
  Válassza ki a "Web közlemény" tartalmat.

> [!NOTE]
> Amikor a webes nézet, azt mindig tartalmaz egy értesítési először.

* Adja meg a webes tartalom (kiválasztását követően a webes hirdetmény tartalmat, a szakasz jelenik meg, lehetővé téve adja meg a webes nézet tartalom megjeleníteni kívánt.)

![WebView4][31]

* A cím az üzenet (nem kötelező) tetején megjelenő írni.
* Ide írhatja a HTML kódot.
* Kattintson a Szerkesztés módban gombra kattintva edition váltson, és megjelenését például a forrás.
* Írjon a tartalmat, amely megjelenik majd az akciógombra kattinthat (Akciógomb lehetővé teszi, hogy az alkalmazás megfelelően egy adott művelet, például az alkalmazás-Áruházbeli vagy bármilyen típusú adatforrások biztosíthat egy lap megnyitása).
* Írás a tartalmat, amely megjelenik majd a Kilépés gombra (a Kilépés gombra kattintva, a webes nézet eltűnik).
* Eredmény

![WebView5][32]

* A felhasználó az értesítést fogadó, és kattintson rá.
* A szöveges nézet jelenik meg egy előugró ablak, a felhasználó használhatja őket.

<!--Image references-->
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

