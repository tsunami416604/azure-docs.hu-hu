---
title: Webes elemzés
description: .
services: Azure, Marketplace, Cloud Partner Portal,
author: pbutlerm
manager: Ricardo.Villalobos
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: pabutler
ms.openlocfilehash: d47a67be157f71a30a6ae155790ed78a78fd6743
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73814642"
---
<a name="web-analytics"></a>Webes elemzés
=============

Ez a cikk útmutatást nyújt a webes elemzések megismeréséhez és használatához, hogy a lehető leghatékonyabban fejlessze vállalkozását. Jelenleg ez az Áttekintés lap minden AppSource-ajánlathoz elérhető.

Most, hogy létrehozta és közzétette az ajánlatát, az utazás következő lépése a\' sikerességének nyomon követése és mérése. A **Web Analytics**szolgáltatással a piactéren pontosan megtekintheti, hogy az egyes ajánlatok milyen jól zajlanak. Az utazás megkezdéséhez navigáljon a Cloud Partner Portal bal oldalán található elemzések lapra, és tekintse meg az új Analitika lapot.

![Webanalitikai lap](./media/si-getting-started/WebAnalytics1.png)

Megjelenik egy, a Microsoft Power BI szolgáltatással létrehozott kiadói AZONOSÍTÓhoz tartozó, részletes irányítópult, amely lehetővé teszi, hogy minden egyes, a naponta frissülő\'-információt megjelenítse.

<a name="microsoft-campaigns"></a>**Microsoft-kampányok**
-----------------------

Az ajánlatok növelése és az ajánlatok növekedésének nyomon követése érdekében engedélyezjük a **Microsoft-kampányok** használatát a Cloud Partner Portalon. A kampányok egy újonnan támogatott szolgáltatás a piactéren, amely lehetővé teszi, hogy nyomon kövesse az ügyfelek által az alkalmazás részletei lapra küldött különböző csatornákat.

### <a name="how-to-make-a-campaign"></a>**Kampány létrehozása**

A kampányok leírásának legegyszerűbb módja, ha olyan egyéni szót/kifejezést ad hozzá az URL-címhez, amely a piactéren az alkalmazás részleteit tartalmazó oldalon landol. A Google, a Bing, a LinkedIn és számos más webhely azt javasolja, hogy hozzon létre egy hirdetést, amely a saját webhelyéről a kívánt webhelyre mutat.

Ezek az erőfeszítések általában segítik az új ügyfelek bevezetését a termékbe, és elengedhetetlen az egyes csatornák sikerének mérése. Ebben az esetben a kampányok beérkeznek.

Kétféle módon hozhatja ki saját kampányát.

1. Adja hozzá az URL-címhez a lekérdezési paraméter **mktcmpid** , amely leírja, hogy mi a kampány, és milyen lapok/események érkeznek ezek az ügyfelek.

Például a következőt használhatja: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign>

1. (Speciális): használja az URL-cím támogatott, általános kampány-azonosítóinak egyikét. Azt szeretnénk, hogy a használni kívánt további ref-címkékkel legyenek elszállásolva, ezért a további címkék automatikus felismerését a következő módon támogatjuk:
    
    1. **UTM\_kampány**
    2. **UTM\_forrása**
    3. **ref**
    4. **src**

Például a következőt használhatja: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=NewCampaign>

A kampány-azonosítók több kombinációját is kiválaszthatja, így több forrást is megadhat a kampányhoz, például hogy az ügyfél honnan származik (e-mail, blog, közösségi média forrása stb.).

Például:

1. Hírlevél ajánlója: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=newsletter>
2. LinkedIn referrer: <https://appsource.microsoft.com/product/dynamics-365/contoso.offername?mktcmpid=NewCampaign&src=LinkedIn>

### <a name="ensuring-campaigns-pass-through-all-your-pages"></a>**A kampányok továbbítása az összes oldalon**

Előfordulhat, hogy a kampányok olyan közbenső oldallal rendelkeznek, amelyre a forgalmat irányítja, és így folytatja az ügyfelek küldését a piactéren. Fontos, hogy átadja a kezdeti kampány-azonosítókat a piactérnek küldött utolsó URL-címnek.

Például:

1. A marketing-alkalmazott hirdetéseket vásárol a Google-tól, hogy átirányítsa a forgalmat a vállalati\'s kezdőlapra <https://contoso.com>. A kezdőlapon \"próbálja ki a termék\" hivatkozást, amely a <https://appsource.com>re mutat.
2. A felhasználó rákattint az ad-re, és landol a vállalat\'s kezdőlapján.
    1.  Hivatkozó URL-cím = google.com
    2.  Kezdőlap URL-címe = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
3. A felhasználó a \"kipróbálása a termék\" hivatkozásra kattint, és bekerül a AppSource.
    1. Hivatkozó URL = <https://contoso.com/?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName>
    2. Kezdőlap URL-címe (**Győződjön meg róla, hogy ez az URL-cím utm\_kampányt és a utm\_forrást adtak hozzá ehhez az URL-címhez**) = [https://appsource.microsoft.com/product/dynamics-365/contoso.offername?**UTM\_kampány = MyCampaignAdName & UTM\_forrás = MySourceAdName** ](https://appsource.microsoft.com/product/dynamics-365/contoso.offername?utm_campaign=MyCampaignAdName&utm_source=MySourceAdName)

<a name="how-to-evaluate-the-success-of-a-campaign"></a>Kampány sikerességének kiértékelése
-----------------------------------------

### <a name="page-visits-by-campaign"></a>**Oldalak látogatása kampány alapján**

![WebAnalytics2](./media/si-getting-started/WebAnalytics2.png)

Ez az egyes napi oldalak meglátogatásának lebontása a kampányból származnak.

### <a name="conversion-rate-by-campaign"></a>**Konverziós arány kampány szerint**

![WebAnalytics3](./media/si-getting-started/WebAnalytics3.png)

A teljes ajánlat konverziós arányának megjelenítéséhez hasonlóan ebben a diagramban láthatja a különböző kampányok működésének részletezését. Ennek a diagramnak a segítségével azonosíthatja, hogy a magasabb konverziós arányú kampányok hová érkeznek.

### <a name="distribution-by-campaign"></a>**Eloszlás kampány szerint**

![WebAnalytics4](./media/si-getting-started/WebAnalytics4.png)

Hasonlóan ahhoz, ahogy megtekintjük az ügyfelek tartományait, ez a diagram lehetővé teszi, hogy megtekintse az adatelosztást egy kampány alapján, amelyet a felhasználók a piactéren kapnak. \_a nem kampány azt jelenti, hogy az ügyfél nem rendelkezik kampány-AZONOSÍTÓval az URL-címben, amikor a piactérre navigáltak.

<a name="next-steps"></a>**Következő lépések**
--------------

Most, hogy lehetősége van az ajánlatok sikeres nyomon követésére, javasoljuk, hogy hozzon létre saját kampányait.

Ha kérdése/funkciója van, a jobb felső sarokban található visszajelzések használatával ossza meg őket.

![Visszajelzés Cloud Partner Portal](./media/si-getting-started/WebAnalytics5.png)
