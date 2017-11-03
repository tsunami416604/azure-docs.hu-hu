---
title: "Az Azure Mobile Engagement felhasználói felület - a Reach-kampány"
description: "Laern hogyan hozhatja létre és kezelheti a leküldéses értesítési kampányokra, Azure Mobile Engagement segítségével"
services: mobile-engagement
documentationcenter: 
author: piyushjo
manager: erikre
editor: 
ms.assetid: 2fe124a2-a86f-4136-81ba-a9d298ec798a
ms.service: mobile-engagement
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: mobile-multiple
ms.workload: mobile
ms.date: 08/19/2016
ms.author: piyushjo
ms.openlocfilehash: fc88db8db11d1ed12fa95c2087c9a32b21bf4de5
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="how-to-create-and-manage-push-notification-campaigns"></a>Hozzon létre, és leküldéses értesítéses kampányokkal kezelése
A Reach szakasza a felhasználói felület segítségével hozzon létre egy új leküldéses kampány egy összetett képlettel minden küldött leküldéses értesítés szükséges információ megadásával. A beállítások a leküldéses kampány kis mértékben változhat a négy típusok: közlemények, szavazások, Adatleküldések és Csempék (csak Windows Phone).

### <a name="option-applies-to"></a>A beállítás a következőkre vonatkozik:
* Nyelv: Az összes (közlemények, szavazások, Adatleküldések, Mozaik)
* A kampány: Összes (közlemények, szavazások, Adatleküldések, Mozaik)
* Értesítés: Közlemények, szavazások
* Tartalom: Az egyes kampány egyedi
* A célközönség: Összes (közlemények, szavazások, Adatleküldések, Mozaik)
* Időkereten belül: közlemények, szavazások, Csempéket
* Teszt: Az összes (közlemények, szavazások, Adatleküldések, Mozaik)

![Reach-Campaign1][20]

## <a name="languages"></a>Nyelvek
A nyelvek legördülő menü segítségével egy másik verziója a leküldéses küldeni az eszközöket, amelyek más nyelvekre vannak beállítva. Alapértelmezés szerint minden eszköz függetlenül attól, milyen nyelven használandó be lettek állítva ugyanazon leküldéses fog kapni. Az eszköz egy másik nyelv beállítása a felhasználók az alapértelmezett nyelve a leküldéses fog kapni. A leküldéses kampány beállítások lehetővé teszik, hogy adjon meg másik tartalom a kiválasztott további nyelvek. 

![Reach-Campaign2][21]

### <a name="language-differences-apply-to"></a>Nyelvi különbségek vonatkoznak:
* Nyelvek: Egyedi nyelvek lehet kiválasztani mellett az alapértelmezett nyelv
* Kampány: Az összes nyelven azonos
* Értesítés: Egyedi, az egyes nyelvekhez mellett az alapértelmezett nyelv
* Tartalom: Egyedi, az egyes nyelvekhez mellett az alapértelmezett nyelv
* A célközönséget: Előfordulhat, hogy egy külön nyelvi feltételek alapján úgy szűri
* Időkereten belül: ugyanazt az összes nyelven
* Teszt: Küldhet nyelvspecifikus egyszerre

### <a name="supported-languages"></a>Támogatott nyelvek:
* Arab (ar) 
* Bolgár (bg) 
* Katalán (ca) 
* Kínai (zh) 
* Horvát (hr) 
* Czech (CS) 
* Dán (da) 
* Holland (Hollandia) 
* Angol (en) 
* Finn (fi) 
* Francia (fr) 
* Német (de) 
* Görög (el) 
* Héber (ő) 
* Hindi (nagy) 
* Magyar (hu) 
* Indonéziai (id) 
* Olasz () 
* Japán (ja) 
* Koreai (ko) 
* Lett (lv) 
* Litván (lt) 
* Maláj (macrolanguage) (ms) 
* Norvég Bokmål (NetBIOS) 
* Lengyel (pl) 
* Portugál (pt) 
* Román (ro) 
* Orosz (ru) 
* Szerb (sr) 
* Szlovák (sk) 
* Szlovén (SA) 
* Spanyol (es) 
* Svéd (sv) 
* Tagalog (tl) 
* Thai (CS) 
* Török (m) 
* Ukrán (uk) 
* Vietnami (VI.) 

## <a name="campaign"></a>Kampány
A kampány szakasz segítségével állítsa be a nevét és kategóriáját, valamint a kampány, ha azt tervezi, hogy hagyja figyelmen kívül a célközönséget a szakasz a leküldéses kampány, és inkább ezt a kampányt Reach API (és néhány eleme az alacsony szintű Push API) keresztül küldeni. Kategóriák vezérlő alkalmazáson belüli értesítések előre meghatározott beállítások alapján egyéni értesítési sablon használható. A meglévő "kategóriák" a Reach API-n keresztül listája kaphat.

> [!WARNING]
> Ha használja a "Mellőzés célközönség leküldéssel fogják megkapni a felhasználók számára az API-n keresztül" beállítást a Reach-kampány "Kampány" szakaszában automatikusan nem küldi el a kampány, szüksége lesz a küldje el manuálisan a Reach API-n keresztül.

![Reach-Campaign3][22]

### <a name="option-applies-to"></a>A beállítás a következőkre vonatkozik:
* Name: összes
* Kategória: Közlemények, szavazások
* Hagyja figyelmen kívül a célközönséget, a felhasználók számára az API-n keresztül leküldéssel fogják megkapni: összes

## <a name="notification"></a>Értesítés
Az értesítési szakaszban használhatja az egyszerű beállítások beállítását a leküldéses többek között: A cím a leküldéses, az üzenet, egy alkalmazás lemezképet, vagy ha mellőzhető. A platform, az eszköz hány értesítési beállításainak vonatkoznak. Kiválaszthatja, hogy a leküldéssel fogják megkapni "alkalmazás" vagy "alkalmazás" verzióról vagy mindkettőt. (Ne feledje, hogy a felhasználók is "részt" vagy "lemondja" a "kívüli alkalmazás" leküldéses értesítések az operációs rendszer szintjét az eszközökön, és Azure Mobile Engagement nem fogja tudni bírálja felül ezt a beállítást. Továbbá ne feledje, hogy a Reach API "alkalmazásban" kezeli, és "out alkalmazás" leküldéses értesítések. A leküldéses API segítségével "kívüli alkalmazás" leküldéses értesítések kezelésére túl.) Leküldéses értesítések testre szabható a képek vagy HTML-tartalmakat, beleértve a mélyhivatkozással kapcsolásához kívül az alkalmazás vagy az alkalmazásban (Android SDK 2.1.0 vagy újabb, szükség leképezési kategóriák) egy másik helyre. Az ikon vagy iOS-jelvény módosíthatja, és küldje el a szöveget vagy webes tartalmat (html, URL-cím Tartalomhivatkozás belül vagy kívül az alkalmazás máshová előugró). Android-eszközök ring ellenőrizze vagy mozog a leküldéses is. (Ne feledje, hogy szüksége lesz a megfelelő engedélyek SDK Android-jegyzékfájl gyűrű vagy egy eszköz mozog.) Jelenleg nincs iparági szabványos az Android "összkép" méretek, óta méretek eltérőek minden olyan eszközre, de a szinte bármilyen képernyőméret használható 400 x 100 képek.

### <a name="delivery-types"></a>Kézbesítési típusok:
* Csak az alkalmazáson kívül: az értesítés kézbesíti a rendszer, amikor a felhasználó nem használja az alkalmazást.
* Az alkalmazás csak értesítési területén egy tanúsítványra van szükség az Apple vagy a Google (APNS vagy a GCM-tanúsítvánnyal).
* Alkalmazáson belüli csak: az értesítés jelenik meg, csak ha az alkalmazás futása.
* Az értesítés Capptain kézbesítési rendszert használ, a felhasználó eléréséhez. Az elrendezés/megjelenítését a leküldéses teljes mértékben testreszabható.
* Bármikor: Ez a beállítás biztosítja, hogy küldjön egy értesítés, vagy az alkalmazás fut-e.

![Reach-Campaign4][23]

### <a name="option-applies-to"></a>A beállítás a következőkre vonatkozik:
* Értesítés: Közlemények, szavazások

## <a name="content"></a>Tartalom
A tartalmi szakasz segítségével módosíthatja a közlemények, szavazások, Adatleküldések és Csempék (csak Windows Phone) tartalmát. A tartalom leküldéses kampányokra lehet kampány típusának. 

### <a name="see-also"></a>Lásd még:
* [Felhasználói felületének dokumentációja – Reach - tartalom leküldéses][Link 29]

![Reach-Campaign5][24]

## <a name="audience"></a>Célközönség
A célközönség szakasz segítségével határozza meg a kampány vagy -korlátok, a kampány testreszabott feltétel alapján korlátozni cikkek standard listáját. Korlátozza a célközönséget a szabványos beállításkészletre új vagy régi felhasználók vagy csak a natív leküldéses felhasználók leküldése teszi lehetővé. Korlátozza a felhasználók számát, akik a leküldéses kap egy kvótát állíthat be. Manuálisan módosíthatja a kifejezést hogyan szűrve van a kampány felvenni egy vagy több feltétel szükséges azon felhasználók megcélzása. Írja be a célközönség kifejezés manuálisan. Ilyen kifejezésben egyértelműen meg kell határoznia a feltételek közötti kapcsolatot. Egy olyan feltételt, amely nagybetűvel kezdődhet, és nem tartalmazhat szóközt azonosítót írja le. A feltételek közötti kapcsolatot jelentheti használatával "és", "vagy", "nem" operátor, valamint a "(",")". Példa: "Criterion1 vagy (Criterion1 és nem Criterion2)".

> [!NOTE]
> A kampányok szereplő nagy célközönséget a kiszolgálóoldali célcsoportkezelés vizsgálatot lassú lehet, különösen akkor, ha egyszerre több kampányok el.

* Ha lehetséges csak start egy kampány egyszerre.
* A legtöbb csak start négy kampányok egyszerre.
* Csak az aktív felhasználókat leküldéssel ("Megszólítása csak olyan felhasználók, akik elérhetők natív leküldéssel használatával" jelölőnégyzet és a "Csak az aktív felhasználók megszólítása"), hogy csak azok a felhasználók, akik továbbra is a az alkalmazás telepítve van, és ezzel beolvasandó kell.
  Miután a célközönséget be van állítva, a szimulálás gomb segítségével megtudhatja, hogy hány felhasználó fog kapni a leküldéses. Ez fogja számítási potenciálisan célozza meg a célközönség (egy véletlenszerű felhasználói minta alapján becsült érték) ismert felhasználók száma. Vegye figyelembe, hogy azok a felhasználók, akik eltávolították az alkalmazást szintén részei a célközönségnek, de nem érhető el.

### <a name="see-also"></a>Lásd még:
* [Felhasználói felület - a Reach - dokumentáció új leküldéses feltétel][Link 28]

![Reach-Campaign6][25]

### <a name="edit-expression"></a>Szerkesztés
![Reach-Campaign7][26]

### <a name="limit-your-audience-option-applies-to"></a>A korlát a célközönség-beállítás:
* Csak egy adott felhasználók alcsoportjának megszólítása: összes (közlemények, szavazások, Adatleküldések, Mozaik)
* Csak a régi felhasználók megszólítása: összes (közlemények, szavazások, Adatleküldések, Mozaik)
* Csak az új felhasználók megszólítása: összes (közlemények, szavazások, Adatleküldések, Mozaik)
* Csak a tétlen felhasználók megszólítása: közlemények, szavazások, Csempéket
* Csak az aktív felhasználók megszólítása: összes (közlemények, szavazások, Adatleküldések, Mozaik)
* Csak olyan felhasználók, akik elérhetők natív leküldéssel megszólítása: közlemények, szavazások

## <a name="time-frame"></a>Időkereten belül
Az időszak szakasz segítségével állítható be, ha a leküldéssel fogják megkapni, vagy üresen időkeretet azonnal elindul a kampány. Ne feledje, hogy a végfelhasználók időzóna használatával előfordulhat, hogy Kezdőnap a kampány egy korábbi, mint a végfelhasználók Ázsiában kapjon, és leküldéses értesítések, amíg a világ minden időzónák felel meg az időkeret, állítsa be a kampány kis kötegeinek küldése. A végfelhasználó időzónájának használatával is lelassíthatják a kampányok mivel azt a idő kér a telefont a leküldéses megkezdése előtt.

> [!NOTE]
> Nélkül záródátumot gyorsítótárazásával kampányokra leküldéses értesítések helyileg és továbbra is megjelenítésükhöz követően manuálisan teljes kampányok. Ez a viselkedés jellemző a kampányok befejezési idő elkerülése érdekében.

### <a name="see-also"></a>Lásd még:
* [A reach - hogyan Tos – ütemezése][Link 3] 

![Reach-Campaign8][27]

### <a name="settings-apply-to"></a>Beállítások érvényesek:
* Időkereten belül: közlemények, szavazások, Csempéket

## <a name="test"></a>Tesztelés
A vizsgálat szakasza segítségével a leküldéses küld a saját vizsgálati eszköz a kampány mentéséhez. Ha konfigurálta a kampány bármilyen egyéni nyelveit, tesztelheti a leküldéses mindkét nyelven. Egy vizsgálati eszköz "Saját fiók" beállítása.

> [!NOTE]
> Nincs a kiszolgálóoldali adatok naplóz, amikor vissza gombját használja a "teszt" a leküldéses értesítések, az adatokat a valódi leküldéses kampányokra csak naplózza.

### <a name="see-also"></a>Lásd még:
* [Felhasználói felület dokumentáció - fiókomat][Link 14]

![Reach-Campaign9][28]

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
[Link 27]: mobile-engagement-user-interface-reach-campaign.md
[Link 28]: mobile-engagement-user-interface-reach-criterion.md
[Link 29]: mobile-engagement-user-interface-reach-content.md

