---
title: Bevezetés az Azure Advisor |} A Microsoft Docs
description: Az Azure Advisor használata az Azure-környezetek optimalizálására.
services: advisor
documentationcenter: NA
author: kasparks
manager: ''
ms.assetid: ''
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kasparks
ms.openlocfilehash: d1b91d62eaafa60426c837cd0e40faaf7b60600b
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52844583"
---
# <a name="introduction-to-azure-advisor"></a>Bevezetés az Azure Advisor

További tudnivalók az Azure Advisor rejlő lehetőségeket, és gyakran feltett kérdésekre adott válaszok.

## <a name="what-is-advisor"></a>Mi az Advisor?
Az Advisor egy személyre szabott felhőalapú tanácsadó, amely segítséget nyújt az Azure-környezetek optimalizálására vonatkozó ajánlott eljárásokat. Az Advisor elemzi az erőforrások konfiguráció- és használattelemetriáját, és megoldási javaslatokat tesz, amelyek segítségével javítható az Azure-erőforrások költséghatékonysága, teljesítménye, rendelkezésre állása és biztonsága.

Az Advisor szolgáltatással a következőket teheti:
* Proaktív, gyakorlatban hasznosítható lekérése, és személyre szabott ajánlott eljárások. 
* Javíthatja a teljesítményt, biztonsági és az erőforrások magas rendelkezésre állású meghatározása a teljes Azure csökkentésére költségek.
* A javasolt műveleteket beágyazott javaslatokat kaphat.

Az Advisor keresztül érheti el a [az Azure portal](https://aka.ms/azureadvisordashboard). Jelentkezzen be a [portál](https://portal.azure.com), keresse meg **Advisor** a navigációs menü, vagy keressen rá a a **minden szolgáltatás** menü.

Az Advisor irányítópult személyre szabott ajánlatokat az összes előfizetés jeleníti meg.  Az adott előfizetésekre és -erőforrástípusok javaslatok megjelenítése szűrőket is alkalmazhat.  A javaslatok négy kategóriába oszthatók: 

* **Magas rendelkezésre állású**: Győződjön meg arról, és javíthatja az üzleti szempontból kritikus fontosságú alkalmazások folytonosságát. További információkért lásd: [Advisor magas rendelkezésre állás – javaslatok](advisor-high-availability-recommendations.md).
* **Biztonsági**: fenyegetések és biztonsági rések vezethet biztonsági rések észlelése. További információkért lásd: [Advisor biztonsági javaslatok](advisor-security-recommendations.md).
* **Teljesítmény**: az alkalmazások a sebesség javítása érdekében. További információkért lásd: [Advisor teljesítményajánlásainak](advisor-performance-recommendations.md).
* **Költség**: optimalizálása, és csökkentheti a teljes Azure költségeit. További információkért lásd: [Advisor díjakkal kapcsolatos ajánlások](advisor-cost-recommendations.md).

  ![Advisor-ajánlást típusok](./media/advisor-overview/advisor-dashboard.png)

Azt a kategóriát, az adott kategórián belül a javaslatok listájának megjelenítéséhez kattintson, és válasszon ki egy javaslatot, ha többet szeretne.  Emellett tudhat meg azokról a műveletekről, kihasználhatja a lehetőséget, vagy a probléma megoldásához hajthat végre.

![Az Advisor-ajánlást kategória](./media/advisor-overview/advisor-ha-category-example.png) 

Válassza ki egy javaslatot a javaslat megvalósítása a javasolt műveletet.  Egy egyszerű felületen nyílik meg, amely lehetővé teszi a javaslat megvalósítása, vagy tekintse meg, dokumentáció, amely segítséget nyújt végrehajtása.  A javaslat megvalósítása, miután, akár is igénybe vehet az Advisor ismeri fel, amely naponta.

Ha nem szeretne azonnal cselekedni ajánlása, egy adott időtartamra elhalaszthatja azt, vagy zárja be azt.  Ha nem szeretne egy adott előfizetésen vagy erőforráscsoporton vonatkozó javaslatokat kapni, konfigurálhatja az Advisor csak a megadott előfizetésekhez és erőforráscsoportokhoz vonatkozó ajánlások előállításához.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-do-i-access-advisor"></a>Hogyan érhetem el az Advisor?
Az Advisor keresztül érheti el a [az Azure portal](https://aka.ms/azureadvisordashboard). Jelentkezzen be a [portál](https://portal.azure.com), keresse meg **Advisor** a navigációs menü, vagy keressen rá a a **minden szolgáltatás** menü.

A virtuális gép erőforrás-felületen keresztül is megtekintheti az Advisor-javaslatok. Válassza ki a virtuális gép, és görgessen a menüben az Advisor-javaslatok. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Milyen engedélyekkel van szükségem az Advisor eléréséhez?
 
Advisor-javaslatok, elérheti *tulajdonosa*, *közreműködői*, vagy *olvasó* előfizetés.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Milyen erőforrások Advisor nyújt ajánlásokat?

Az Advisor virtuális gépek, a rendelkezésre állási csoportok, az application Gateway átjárók, alkalmazásszolgáltatások, SQL Server-kiszolgálók és Redis Cache vonatkozó javaslatokkal szolgál.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Halassza el vagy elvetheti a javaslatot?

Halassza el, vagy hagyja figyelmen kívül a javaslatot, kattintson a **elhalasztás** hivatkozásra. Megadhat egy időszak, vagy válassza elhalasztás **soha** elvetése a javaslat.

## <a name="next-steps"></a>További lépések

Az Advisor-javaslatok kapcsolatos további információkért lásd:

* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Az Advisor magas rendelkezésre állás – javaslatok](advisor-high-availability-recommendations.md)
* [Az Advisor biztonsági javaslatok](advisor-security-recommendations.md)
* [Advisor-teljesítményajánlások](advisor-performance-recommendations.md)
* [Az Advisor díjakkal kapcsolatos ajánlások](advisor-cost-recommendations.md)
