---
title: Fontos változások a Azure Security Center
description: Előfordulhat, hogy a Azure Security Center közelgő módosításaival tisztában kell lennie a szolgáltatással, és amelyre szükség lehet a tervezéshez
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2020
ms.author: memildin
ms.openlocfilehash: df863372cbf7abfb6fee145b7d13bb00d8deb074
ms.sourcegitcommit: 8a1ba1ebc76635b643b6634cc64e137f74a1e4da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2020
ms.locfileid: "94380165"
---
# <a name="important-upcoming-changes-to-azure-security-center"></a>A Azure Security Center fontos jövőbeli módosításai

> [!IMPORTANT]
> Az ezen az oldalon található információk a kiadás előtti termékekhez vagy szolgáltatásokhoz kapcsolódnak, amelyek a kereskedelmi forgalomba hozatal előtt jelentős mértékben módosíthatók, ha még nem. A Microsoft nem vállal semmilyen kifejezett vagy vélelmezett szavatosságot az itt megadott információk tekintetében.

Ezen az oldalon megismerheti a Security Center tervezett módosításokat. Ismerteti a termék tervezett módosításait, amelyek hatással lehetnek a biztonságos pontszámra vagy munkafolyamatokra.

Ha a legújabb kibocsátási megjegyzéseket keresi, a [Azure Security Center újdonságai](release-notes.md)között találhatja meg őket.


## <a name="planned-changes"></a>Tervezett változások

### <a name="system-updates-should-be-installed-on-your-machines-recommendation-getting-sub-recommendations"></a>"A rendszerfrissítéseket telepíteni kell a gépekre" javaslat a javaslatok beszerzésére

#### <a name="summary"></a>Összefoglalás

| Szempont | Részletek |
|---------|---------|
|Bejelentés dátuma | 9. november 2020  |
|A módosítás dátuma  |  November 2020. közepe |
|Hatás     | Az ajánlás aktuális verziójáról a cserére való áttérés során a biztonságos pontszám változhat. |
|  |  |

A **rendszerfrissítések** továbbfejlesztett verzióját kell telepíteni a gépekre vonatkozó javaslatra. Az új verzió *lecseréli* a jelenlegi verziót a rendszerfrissítések biztonsági ellenőrzése területen, és a következő újdonságokat javítja:

- Aljavaslatok minden hiányzó frissítéshez
- A Azure Portal Azure Security Center oldalain újratervezett élmény
- Bővített adatok az Azure Resource Graph javaslatához

#### <a name="transition-period"></a>Átmeneti időszak

A 36 óra (körülbelül) átmeneti időszakot eredményez. A lehetséges fennakadások csökkentése érdekében ütemezjük a frissítést egy hétvégi időszakra. Az áttérés során a biztonságos pontszámok is befolyásolhatják.

#### <a name="redesigned-portal-experience"></a>Az átalakított portál felülete

A **rendszerfrissítésekre** vonatkozó ajánlás részletei lapon telepíteni kell a számítógépeken az alább látható megállapításokat tartalmazó listát. Egyetlen keresés kiválasztásakor megnyílik a részletek ablaktábla a Szervizelési információkra mutató hivatkozással és az érintett erőforrások listájával.

:::image type="content" source="./media/upcoming-changes/system-updates-should-be-installed-subassessment.png" alt-text="A frissített javaslathoz tartozó alárendelt javaslatok egyikének megnyitása a portálon":::


#### <a name="richer-data-from-azure-resource-graph"></a>Gazdagabb adatok az Azure Resource Graph-ból

Az Azure Resource Graph egy Azure-szolgáltatás, amely hatékony erőforrás-feltárást tesz lehetővé. Az ARG használatával nagy léptékű lekérdezéseket végezhet az adott előfizetések között, így hatékonyan szabályozhatja a környezetét. 

Azure Security Center esetében az ARG és a [Kusto lekérdezési nyelv (KQL)](https://docs.microsoft.com/azure/data-explorer/kusto/query/) használatával kérdezheti le a biztonsági testhelyzetek széles körét.

Ha a **rendszerfrissítések** aktuális verzióját szeretné lekérdezni a gépekre, az ARG-ből elérhető egyetlen információ az, hogy a javaslatot szervizelni kell a gépen. A frissített verzió kiadása után a következő lekérdezés visszaküldi a hiányzó rendszerfrissítéseket a számítógép szerint csoportosítva.

```kusto
securityresources
| where type =~ "microsoft.security/assessments/subassessments"
| where extract(@"(?i)providers/Microsoft.Security/assessments/([^/]*)", 1, id) == "4ab6e3c5-74dd-8b35-9ab9-f61b30875b27"
| where properties.status.code == "Unhealthy"
```

## <a name="next-steps"></a>Következő lépések

A termék legutóbbi módosításaival kapcsolatban tekintse [meg az Újdonságok a Azure Security Center?](release-notes.md)című témakört.