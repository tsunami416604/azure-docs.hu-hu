---
title: Bevezetés az Azure Advisor |} Microsoft Docs
description: Azure Advisor segítségével optimalizálhatja az Azure-környezetekhez.
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 0aa97a7557706f0b704d4e31a84e9d356d1ad087
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="introduction-to-azure-advisor"></a>Bevezetés az Azure Advisor

További tudnivalók az Azure Advisor főbb képességei, és gyakran feltett kérdésekre adott válaszok.

## <a name="what-is-advisor"></a>Mi az az Advisor?
Az Advisor egy személyre szabott felhő tanácsadó, amelynek segítségével hajtsa végre az ajánlott eljárások az Azure-környezetekhez optimalizálása érdekében. Az Advisor elemzi az erőforrások konfiguráció- és használattelemetriáját, és megoldási javaslatokat tesz, amelyek segítségével javítható az Azure-erőforrások költséghatékonysága, teljesítménye, rendelkezésre állása és biztonsága.

Az Advisor szolgáltatásban a következőket teheti:
* Szerezze be a proaktív, hajtható végre, és személyre szabott ajánlott eljárások a. 
* A teljes Azure csökkentése érdekében lehetőségek meghatározása teljesítmény, biztonsági és az erőforrások magas rendelkezésre állás javítása televíziózással töltenek.
* A javasolt műveletek beágyazott javaslatok beszerzése.

Az Advisor keresztül érheti el a [Azure-portálon](https://aka.ms/azureadvisordashboard). Jelentkezzen be a [portal](https://portal.azure.com), keresse meg **Advisor** a navigációs menü, vagy keresse meg azt a **minden szolgáltatás** menü.

Az Advisor irányítópult megjeleníti az előfizetések személyre szabott javaslatok.  Szűrőket adott előfizetések és erőforrástípusok javaslatok megjelenítése.  A javaslatok négy kategóriába oszthatók: 

* **Magas rendelkezésre állású**: Győződjön meg arról, az üzleti szempontból kritikus fontosságú alkalmazások folytonosságának javításához. További információkért lásd: [Advisor magas rendelkezésre állású javaslatok](advisor-high-availability-recommendations.md).
* **Biztonsági**: veszélyek és biztonsági problémák vezethet biztonsági rések észlelése. További információkért lásd: [Advisor biztonsági javaslatok](advisor-security-recommendations.md).
* **Teljesítmény**: az alkalmazások a sebesség növelése érdekében. További információkért lásd: [Advisor teljesítmény javaslatok](advisor-performance-recommendations.md).
* **Költség**: optimalizálása és a teljes Azure kiadásait. További információkért lásd: [Advisor költség javaslatok](advisor-cost-recommendations.md).

  ![Az Advisor javaslat típusok](./media/advisor-overview/advisor-dashboard.png)

Egy kategóriát az adott kategóriába javaslatok listájának megjelenítéséhez kattintson, és válassza ki a javaslatra kattintva olvashat azokról bővebben.  Lehetőség előnyeit, vagy hárítsa el a problémát a végrehajtható műveletek is olvashat.

![Az Advisor javaslat kategória](./media/advisor-overview/advisor-ha-category-example.png) 

Válassza ki ahhoz a javaslat megvalósításához ajánlott beavatkozást.  Egy egyszerű felületen nyílik meg, amely lehetővé teszi a javaslat megvalósítását, vagy tekintse meg a dokumentációt, amely segítséget nyújt végrehajtására.  Ajánlást bevezetése után is igénybe vehet az Advisor ismeri fel, amely naponta.

Ha nem kíván azonnal intézkedhet ajánlása, halassza el az adott időszakra vonatkozóan, vagy zárja be azt.  Ha nem szeretne kapni egy előfizetéshez vagy erőforráscsoporthoz javaslatok, konfigurálhatja az Advisor csak a megadott előfizetésekhez és erőforráscsoportokhoz javaslatok létrehozni.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-do-i-access-advisor"></a>Hogyan érhetem el az Advisor?
Az Advisor keresztül érheti el a [Azure-portálon](https://aka.ms/azureadvisordashboard). Jelentkezzen be a [portal](https://portal.azure.com), keresse meg **Advisor** a navigációs menü, vagy keresse meg azt a **minden szolgáltatás** menü.

Javaslatokat biztosít a virtuális gép erőforrás-felületen keresztül is megtekintheti. Válassza ki a virtuális gépet, és görgessen a menü javaslatokat biztosít. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Milyen engedélyekkel Advisor elérésére van?
 
Érheti el, az Advisor-javaslatokra *tulajdonos*, *közreműködő*, vagy *olvasó* az előfizetés.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Milyen erőforrásokat Advisor nyújt javaslatokat?

Az Advisor virtuális gépek rendelkezésre állási készletek, alkalmazásátjárót, alkalmazásszolgáltatások, SQL Server-kiszolgálók és Redis Cache vonatkozó javaslatokkal szolgál.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Halassza el vagy hagyja figyelmen kívül az ajánlás?

Halassza el, vagy hagyja figyelmen kívül az ajánlás olyan környezetekben, kattintson a **elhalasztás** hivatkozásra. Megadhat egy elhalasztás időszak vagy select **soha** elvetni a javaslat.

## <a name="next-steps"></a>További lépések

Az Advisor-javaslatokra kapcsolatos további információkért lásd:

* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Magas rendelkezésre állású javaslatokat biztosít](advisor-high-availability-recommendations.md)
* [Biztonsági javaslatokat biztosít](advisor-security-recommendations.md)
* [Teljesítmény javaslatokat biztosít](advisor-performance-recommendations.md)
* [Költség javaslatokat biztosít](advisor-cost-recommendations.md)
