---
title: Webalkalmazás-elemzés |} A Microsoft Docs
description: .
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Ricardo.Villalobos
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pbutlerm
ms.openlocfilehash: 20a4e24800b2fa05990d294cb374841c50c4f79b
ms.sourcegitcommit: 9eaf634d59f7369bec5a2e311806d4a149e9f425
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2018
ms.locfileid: "48809744"
---
<a name="web-analytics"></a>Webes elemzés
=============

Ez a cikk ismerteti, és ismerje meg, és a webes elemzési használja a legjobb útmutatást terjessze ki üzleti tevékenységét. Jelenleg ez Insights lap bármely AppSource ajánlat érhető el.

Most, hogy létrehozott és közzétett az ajánlat,-e a következő részére, örömét nyomon követésére, és mérheti annak\' sikeres. A **Webstatisztika**, láthatja, hogy pontosan milyen jól az ajánlatok mindegyike állapotát a marketplace-en hozzáadtuk. Megkezdheti a folyamatot, keresse meg a Cloud Partner portálra, az új Analytics lap bal oldalán Insights lap.

![WebAnalytics lap](./media/si-getting-started/WebAnalytics1.png)

Egy informatív irányítópulton megjelenik a Gyártóazonosítóval, amely a Microsoft Power BI-val állították össze, és tekintse meg az ajánlatok mindegyike lehetővé teszi a\' adatok naponta frissülnek.

<a name="microsoft-campaigns"></a>**A Microsoft a kampányok**
-----------------------

Annak érdekében, hogy növelje az ajánlatok, és nyomon követheti az ajánlatok növekedését, használhat engedélyeztük **Microsoft kampányok** a Cloud Partner portálra szóló. A kampányok, amelyeket egy újonnan támogatott szolgáltatás a Marketplace-en, amely lehetővé teszi a különböző csatornákon, az alkalmazás részleteit megjelenítő oldalon a vevők küldő nyomon követéséhez.

### <a name="how-to-make-a-campaign"></a>**A kampány biztosítása**

A kampányok leírására legegyszerűbb módja a, hogy ad hozzá egy egyéni szót vagy kifejezést az URL-címet, hajtanak végre az alkalmazás részletek oldalán a Marketplace-en. A Google, a Bing, a LinkedIn és a sok más helyekre javasoljuk, hogy az alkalmazásban a hirdetmény forráshierarchiából, a hivatkozásra helyük a kívánt helyre.

Általában ezen erőfeszítés kiszélesítése meghajtó új ügyfeleket érdekében a termékbe, és fontos, hogy az egyes csatornák állapotát sikerének mérésében. Ez a kampányok fontossá.

Kétféleképpen saját kampány létrehozásához.

1. Az URL-cím hozzáadása a lekérdezési paraméter **mktcmpid** , amely leírja, mi az a kampány és melyik lapot vagy eseményt ezen ügyfelek használják.

Például használhatja: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign>

2. (Speciális): az URL-címben a támogatott, általános kampány azonosítók egyikével. Szeretnénk kell használnia, így a konvenciókhoz, és ismeri fel automatikusan ezeket további címkékkel is nyújtunk támogatást további ref címkékkel kell elhelyezésére:
    
    1. **UTM\_kampány**
    2. **UTM\_forrás**
    3. **REF**
    4. **src**

Például használhatja: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign>

Több kombinációit választhatja a kampány azonosítóit a részletekben további a több forrásból, például, hogy az ügyfél, honnan származnak a (e-mailt, blog, közösségi média forrás, stb.) a kampány forgalom vezetési azonosításához.

Példa:

1. Hírlevél hivatkozó:  <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter>
2. LinkedIn hivatkozó:  <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn>

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**Biztosítása kampányok haladnak keresztül az összes oldal**

Előfordulhat, hogy egy olyan forgatókönyvet, ahol a kampányok tartozik egy köztes lap eredményező, forgalom, majd a piactéren az ügyfelek küldendő folytatódik. Fontos a kezdeti kampány azonosítók továbbítja a végső URL, amelyeket elküldhet a Marketplace-en.

Például:

1. Marketing alkalmazott ads vásárol Google meghajtót-forgalom a vállalat\'s kezdőlapja <https://contoso.com>. A kezdőlap rendelkezik egy \"próbálja meg a termék\" mutató hivatkozás <https://appsource.com>.
2. Egy felhasználó rákattint az ad és a saját vállalati hajtanak végre\'s kezdőlapja.
    1.  Átirányítási URL-cím = google.com
    2.  Kezdőlap URL-címe = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
3. A felhasználó kattint a \"próbálja meg a termék\" hivatkozásra, és az appsource-ban kerül.
    1. Átirányítási URL-címe =  <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
    2. Kezdőlap URL-címe (**ügyeljen arra, hogy az URL-cím utm\_kampány és az utm\_hozzá az URL-cím forrás**) = [ https://appsource.microsoft.com/en-us/product/dynamics-365/contoso.offername? **utm\_kampány = MyCampaignAdName & utm\_forrás = MySourceAdName**](https://appsource.microsoft.com/en-us/product/dynamics-365/contoso.offername?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName)

<a name="how-to-evaluate-the-success-of-a-campaign"></a>A kampány sikeres kiértékelése
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Kampány szerinti látogatásokat lap**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Ez a táblázat összefoglalja az egyes a napi látogatások, hogy honnan származnak a kampány célközönségeként.

### <a name="conversion-rate-by-campaign"></a>**Kampány konverziós arány**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

Hasonló hogyan bemutatjuk a teljes ajánlat az átváltási árfolyam láthatja a táblázat összefoglalja a különböző kampányok állapotát ezen a diagramon. Ez a diagram kell segítségével azonosíthatja, ahol a magasabb konverziós ráta kampányok érkeznek.

### <a name="distribution-by-campaign"></a>**ELOSZLÁS kampány szerint**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

Hogyan áttekintjük az ügyfelek tartományainak hasonlóan, ezen a diagramon lehetővé teszi tekintse meg az adatokat a felhasználók a piactéren elérhető kampányai eloszlása. \_NoCampaign azt jelenti, hogy az ügyfél nincs a kampány azonosító az URL-cím akkor nyit meg, a Marketplace-en, amikor.

<a name="next-steps"></a>**Következő lépések**
--------------

Most, hogy ajánlatait igényei sikeres követését, szeretnénk javasoljuk, hogy a saját kampányok létrehozását.

Kérdések/funkcióra vonatkozó javaslata van, ha megosztaná keresztül visszajelzéseket, a jobb felső sarokban található.

![A Cloud Partner Portalon visszajelzés](./media/si-getting-started/WebAnalytics5.png)
