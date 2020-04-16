---
title: Web Analytics
description: Ez a cikk útmutatást nyújt a Web Analytics tanulásához és használatához a vállalkozás legjobb növekedéséhez.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bd6d6375b19689c582c0f129378c76936e624e04
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416283"
---
<a name="web-analytics"></a>Web Analytics
=============

Ez a cikk útmutatást nyújt a Web Analytics tanulásához és használatához a vállalkozás legjobb növekedéséhez. Jelenleg ez az Insights lap minden AppSource-ajánlathoz elérhető.

Most, hogy megépítette és közzétette az ajánlatát, az utazás következő\' része a siker nyomon követése és mérése. A **Web Analytics**segítségével pontosan láthatjuk, hogy az egyes ajánlatok milyen jól teljesítenek a piacon. Az utazás megkezdéséhez keresse meg a Cloud Partner Portal bal oldalán található Insights lapot az új Analytics lap megtekintéséhez.

![WebAnalytics lap](./media/si-getting-started/WebAnalytics1.png)

A Microsoft Power BI-val létrehozott, a Publisher-azonosítóhoz egy gazdag irányítópultot fog\' látni, amely lehetővé teszi az ajánlatok minden egyes, naponta frissülő adatának megtekintését.

<a name="microsoft-campaigns"></a>**Microsoft-kampányok**
-----------------------

Az ajánlatok bővítéséhez és az ajánlatok növekedésének nyomon követéséhez lehetővé tettük a **Microsoft-kampányok** használatát a Cloud Partner Portálon. A kampányok a piactér újonnan támogatott funkciói, amelyek lehetővé teszik, hogy nyomon kövesse azokat a különböző csatornákat, amelyek az ügyfeleket az alkalmazás részleteit tartalmazó oldalra küldik.

### <a name="how-to-make-a-campaign"></a>**Hogyan készítsünk egy kampányt**

A legegyszerűbb módja a kampányok leírásának, ha olyan egyéni szót/kifejezést ad hozzá az URL-címhez, amely az alkalmazás részletes oldalán landol a piactéren. A Google, a Bing, a LinkedIn és sok más webhely arra ösztönzi Önt, hogy építsen egy hirdetést, linket a webhelyéről a kívánt webhelyre.

Általánosságban elmondható, hogy ezek az erőfeszítések segítenek az új ügyfeleket a termékbe vezetni, és alapvető fontosságú, hogy felmérjük az egyes csatornák sikerének mérését. Itt jönnek a képbe a kampányok.

Két módon hozhat létre saját kampányt.

1. Adja hozzá az URL-címhez az **mktcmpid** lekérdezési paramétert, amely leírja, hogy mi a kampány, és milyen oldalból/eseményből érkeznek ezek az ügyfelek.

Használhatja például a következőket:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign```

1. (Speciális): Használja az egyik támogatott, általános kampányazonosítónkat az URL-ben. Azt akarjuk, hogy alkalmazkodjanak a további ref címkéket, hogy meg kell használni, ezért támogatjuk az egyezményt, hogy automatikusan felismeri ezeket a további címkéket:
    
    1. **utm\_kampány**
    2. **utm\_forrás**
    3. **Ref**
    4. **src**

Használhatja például a következőket:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign```

Választhat, hogy több ilyen kampányazonosító kombinációjával is rendelkezik, hogy tovább azonosítsa a kampány forgalmát hajtó több forrást, például azt, hogy honnan érkezett az ügyfél (e-mail, blog, közösségi média forrás stb.).

Például:

1. Hírlevél ajánlója:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter```
2. LinkedIn-ajánló:```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn```

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Annak biztosítása, hogy a kampányok az összes oldalon áthaladjanak**

Előfordulhat, hogy a kampányok rendelkeznek egy köztes oldallal, amelyhez forgalmat vezet, majd folytatja az ügyfelek piactérre küldését. Fontos, hogy a kezdeti kampányazonosítókat átmenjen a piactérre küldött végső URL-címbe.

Például:

1. Marketing alkalmazott vásárol hirdetéseket a Google-tól, hogy a forgalom a cég\'céloldal ```https://contoso.com```. Ez a céloldal \"egy\" megpróbál az ```https://appsource.com```én termék linket, hogy megy .
2. A felhasználó rákattint a hirdetésre,\'és a vállalat céloldalára landol.
    1.  Hivatkozó URL = google.com
    2.  Céloldal URL-címe =```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
3. A felhasználó rákattint \"a\" próbálja ki a terméket linket, és bemegy AppSource.
    1. Hivatkozó URL =```https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName```
    2. Céloldal URL-címe (**Győződjön\_meg arról, hogy az URL-címhez utm kampány és utm\_forrás van hozzáadva**) =```https://appsource.microsoft.com/product/dynamics-365/contoso.offername?**utm\_campaign=MyCampaignAdName&utm\_source=MySourceAdName**```

<a name="how-to-evaluate-the-success-of-a-campaign"></a>A kampány sikerének értékelése
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Oldallátogatások kampány szerint**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Ez a napi oldallátogatások lebontása a kampány szerint, amelyből származnak.

### <a name="conversion-rate-by-campaign"></a>**Konverziós arány kampány szerint**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

Hasonlóan ahhoz, ahogyan a teljes ajánlat konverziós arányát megmutatjuk, ebben a táblázatban láthatja a különböző kampányok működésének lebontását. Ez a diagram segít azonosítani, hogy a magasabb konverziós arányú kampányok honnan származnak.

### <a name="distribution-by-campaign"></a>**Terjesztés kampány szerint**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

Hasonlóan ahhoz, ahogyan az ügyfelek domainjeit vizsgáljuk, ez a diagram lehetővé teszi az adatok kampányonkénti terjesztését, amely alatt a felhasználók a piactérre érkeznek. \_A NoCampaign azt jelenti, hogy az ügyfél nem rendelkezett kampányazonosítóval az URL-ben, amikor a piactérre navigált.

<a name="next-steps"></a>**Következő lépések**
--------------

Most, hogy nyomon követheti ajánlatai sikerét, szeretnénk arra ösztönözni, hogy hozzon létre saját kampányokat.

Ha kérdése/funkciókérése van, ossza meg őket a visszajelzésen keresztül, amely a jobb felső sarokban található.

![Visszajelzés a Felhőpartneri portálon](./media/si-getting-started/WebAnalytics5.png)
