---
title: Azure számlázás és költség felügyeleti automatizálási esetekben |} Microsoft Docs
description: Megtudhatja, hogyan közös számlázási és költségkezelésére forgatókönyvek különböző megfeleltetve API-k.
services: billing
documentationcenter: ''
author: Erikre
manager: dougeby
editor: ''
tags: billing
ms.assetid: 204b15b2-6667-4b6c-8ea4-f32c06f287fd
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 6/07/2018
ms.author: erikre
ms.openlocfilehash: ac0c4c0bad98c3057fb554e6011f1c601cd97dee
ms.sourcegitcommit: 3c3488fb16a3c3287c3e1cd11435174711e92126
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/07/2018
ms.locfileid: "34851273"
---
# <a name="billing-and-cost-management-automation-scenarios"></a>Számlázási és költségű felügyeleti automatizálási esetekben

A számlázási és költségű felügyeleti hely gyakori forgatókönyvei neve alább, és különböző API-kat azokra a használt leképezve. Minden elérhető API és a funkciókat kínálnak összegzését a forgatókönyvhöz a API leképezési alatt található. 

## <a name="common-scenarios"></a>Gyakori forgatókönyvek 

Használhatja a számlázási és költségkezelésére különféle forgatókönyvekhez, amik válasz költség-és használati API-k kapcsolatos kérdésekre.  Gyakori helyzetek röviden lejjebb tekinthetők meg.

- **Egyeztetés számla** -volt Microsoft díjat számítanak, kérek a megfelelő összeg?  A számlázási és I számolhatja azt saját?

- **Kereszt-díjak** - most, hogy tudható, hogy milyen mértékű I vagyok felszámított, akik a saját szervezetem kell fizetnie kell?

- **Optimalizálás költség** -tudható, hogy milyen mértékű I már lett felszámított, azonban Hogyan kaphatok további a pénz I vagyok ebből az Azure-on kívül?

- **Költségek nyomon követése** – mennyi I vagyok költségeik és az Azure használatával időbeli megtekintése. Mik a trendek? Hogyan sikerült I kell módon jobban?

- **A hónap során töltött Azure** – mekkora az aktuális hónap dátumhoz tartozó töltött? Van a kiadások és/vagy Azure használatát végezze el a módosításokat? A hónap során mikor vagyok I fel Azure a legtöbb?

- **Riasztások beállítása** – szeretnék fogyasztás erőforrás-alapú vagy pénzügyi alapú riasztás beállítása.

## <a name="scenario-to-api-mappings"></a>Az API-hozzárendelések forgatókönyv

|         API/forgatókönyv        | Számla egyeztetés    | Kereszt-díjak    | Költség optimalizálása    | Költségek nyomon követése    | Mid hónap töltött    | Riasztások    |
|:---------------------------:|:-------------------------:|:----------------:|:--------------------:|:----------------:|:------------------:|:---------:|
| Költségkeret                     |                           |                  |           X          |                  |                    |     X     |
| Piactér                |             X             |         X        |           X          |         X        |          X         |     X     |
| Árlista                 |             X             |         X        |           X          |         X        |          X         |           |
| Foglalási javaslatok |                           |                  |           X          |                  |                    |           |
| Foglalás részletei         |                           |                  |           X          |         X        |                    |           |
| Foglalási az oldalon       |                           |                  |           X          |         X        |                    |           |
| Használat részletei               |             X             |         X        |           X          |         X        |          X         |     X     |
| Elszámolási időszakok             |             X             |         X        |           X          |         X        |                    |           |
| Számlák                    |             X             |         X        |           X          |         X        |                    |           |
| RateCard                    |             X             |                  |           X          |         X        |          X         |           |
| Az értékelés nélküli használata               |             X             |                  |           X          |                  |          X         |           |

> [!NOTE]
> A forgatókönyvhöz az alábbi API-hozzárendelések a nem tartalmazzák a vállalati felhasználása API-k. Ahol lehetséges, adjon hasznosítani az általános felhasználás API-k soron nettó új fejlesztési forgatókönyvre megoldására.

## <a name="api-summaries"></a>Ezekkel az API

### <a name="consumption"></a>Használat
(*Webes közvetlen + vállalati ügyfelek összes API-k esetében, kivéve azokat hívott ki alatt*)

-   **Költségvetések** (*csak a vállalati ügyfelek*): használja a [költségvetések API](https://docs.microsoft.com/rest/api/consumption/budgets) költség vagy a használati költségvetések erőforrások, erőforráscsoport-sablonok vagy számlázási mérőszámok létrehozásához.  Miután létrehozta a költségvetések, riasztás beállítható úgy, hogy értesítése, ha a felhasználó által definiált költségvetési küszöbérték túllépése. Műveletek is beállítható úgy, hogy költségvetési összegek elérésekor következik be.
-   **Piactér**: használja a [piactér díjak API](https://docs.microsoft.com/rest/api/consumption/marketplaces) összes piactér erőforrásra kell fizetni és használati adatok eléréséhez (Azure 3. fél ajánlatok). Ezen adatok segítségével költségek egyezzen piactér erőforrások között, vagy vizsgálja meg a költségek és-használat az adott (oka) t.
-   **Árlista** (*csak a vállalati ügyfelek*): a vállalati ügyfelek használhatják a [Price Sheet API](https://docs.microsoft.com/rest/api/consumption/pricesheet) az egyéni árképzési minden beolvasása. A vállalatok segítségével ezeket az adatokat és használati adatokat és a piactér használati adatok együttes használatával használati és piactér költség számításokat. 
-   **Foglalási javaslatok**: használja a [foglalás javaslatok API](https://docs.microsoft.com/rest/api/consumption/reservationrecommendations) javaslatok lekérni a megvásárlásáról a virtuális gép fenntartott példányok. Javaslatok célja, hogy lehetővé teszi az ügyfelek várt költséghatékony elemzése és a beszerzési összegek.
-   **Foglalás részletei**: használja a [foglalás részletei API](https://docs.microsoft.com/rest/api/consumption/reservationsdetails) információ megjelenítéséhez a korábban beszerzett VM-foglalásokat, például mennyi fogyasztás van fenntartva és mennyi ténylegesen használatban van. Egy Virtuálisgép-szintű részletes adatokat tekintheti meg.
-   **Foglalási összesítések**: használja a [foglalás ezekkel az API](https://docs.microsoft.com/rest/api/consumption/reservationssummaries) összesített információk megjelenítéséhez a korábban beszerzett VM-foglalásokat, például mennyi fogyasztás van fenntartva és mennyi ténylegesen használatban van aggregált. 
-   **Használat részleteiről**: használja a [használati részletek API](https://docs.microsoft.com/rest/api/consumption/usagedetails) le kell fizetni és a használat összes Azure-1-jétől gyártók erőforrásait. Információk a részletes rekordok, amely jelenleg egyszer kibocsátott mérő / nap / formájában van. Adatok használhatók a költségek egyezzen az összes erőforrás között, vagy vizsgálja meg az adott (oka) t-költségek/használata.
-   **RateCard**: webes közvetlen az ügyfelek használhatják a [RateCard API](https://msdn.microsoft.com/library/azure/mt219005.aspx) a mérési díjszabás eléréséhez. Indítására használhatja a visszakapott információk az erőforrás-használat adataikat várt számlázási manuálisan kiszámításához. 
-   **Nem minősített használati**: használhatja a [minősítés nélküli használati API](https://msdn.microsoft.com/library/azure/mt219003.aspx) beolvasni a nyers használati információkat, mielőtt bármilyen mérési/díjszabási Azure végezhető el.

### <a name="billing"></a>Számlázás
-   **Elszámolási időszakok**: használja a [számlázási időszakok API](https://docs.microsoft.com/rest/api/billing/billingperiods) a számla és elemezheti a számlázott időszak meghatározásához azonosítót az adott időszakra. Számla azonosítót az alábbi számla API-val használható. 
-   **Számlák**: használja a [számlák API](https://docs.microsoft.com/rest/api/billing/invoices) számla a letöltési URL-cím lekérése egy adott számlázási időszak PDF formában.

### <a name="enterprise-consumption"></a>Vállalati felhasználása
*(Minden API-k vállalati csak)*

-   **Egyenleg összefoglaló**: használja a [egyenleg összefoglaló API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-balance-summary) beolvasandó havi összefoglaló információk kiegyensúlyozza, új, Azure piactér szolgáltatási díjak, módosítását, és keretét díjakat. Az információk az aktuális elszámolási időszak vagy egy az elmúlt időszakban. A vállalatok ezek az adatok segítségével hajtsa végre kézzel számított összefoglaló díjak összehasonlítása. Ez az API nem biztosít erőforrás-specifikus adatait vagy egy összesített nézet költségek.
-   **Használat részleteiről**: használja a [használati részletek API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-usage-detail) 1. fél Azure használati adatainak részletes az aktuális hónap, egy meghatározott számlázási időszak vagy egy egyéni dátumtartomány eléréséhez. Ezek az adatok segítségével manuálisan ki kell számítani a sebesség és a fogyasztás alapján számlázási és szervezet attribútum költségekkel részleg/szervezeti adatok is használhatja. Az adatok használati/költség erőforrás-specifikus nézetét biztosítja.
-   **Piactér tároló kell fizetni**: használja a [piactér tároló ingyenesen elérhető API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-marketplace-storecharge) 3. fél Azure használati adatainak részletes az aktuális hónap, egy meghatározott számlázási időszak vagy egy egyéni dátumtartomány eléréséhez. Ezek az adatok segítségével manuálisan ki kell számítani a sebesség és a fogyasztás alapján számlázási és szervezet attribútum költségekkel részleg/szervezeti adatok is használhatja. A piactér tároló ingyenesen elérhető API használati/költség erőforrás-specifikus nézetét biztosítja.
-   **Árlista**: A [Price Sheet API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-pricesheet) alkalmazható sebessége biztosít minden a megadott regisztrációs és számlázási időszak. Ezt az információt sebessége is használati adatait és a piactér használati adatok együtt manuálisan kiszámításához használandó várt számlázási.
-   **Elszámolási időszakok**: használja a [számlázási időszakok API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-billing-periods) a számlázási és a vállalati API adatok, amelyek a projektjükhöz a számlázott időszak - BalanceSummary, négy különböző API útvonal mutató tulajdonság időszakok listájának lekérdezése UsageDetails piactér díjakat és árlista.
-   **Fenntartott példány javaslatok**: A [fenntartott példány javaslatok API](https://docs.microsoft.com/rest/api/billing/enterprise/billing-enterprise-api-reserved-instance-recommendation) ellenőrzi, hogy az ügyfél 7 nap, 30 napig, vagy a virtuális gépek 60 nap, és egyetlen és megosztott beszerzési javaslatokat nyújt. Az API lehetővé teszi az ügyfelek elemzéséhez fenntartott példány várt költséghatékony, és ajánlott beszerzési összeg.

## <a name="next-steps"></a>További lépések

- Az Azure használatának programozott módon webhelynaplókat Azure számlázási API-k használatával kapcsolatos információkért lásd: [Azure számlázási API – áttekintés](billing-usage-rate-card-overview.md).