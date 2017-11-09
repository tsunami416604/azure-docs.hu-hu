---
title: "Bevezetés az Azure Advisor |} Microsoft Docs"
description: "Azure Advisor segítségével optimalizálhatja az Azure-környezetekhez."
services: advisor
documentationcenter: NA
author: KumudD
manager: carmonm
editor: 
ms.assetid: 
ms.service: advisor
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/16/2016
ms.author: kumud
ms.openlocfilehash: 906450f75557820bb27762707c3328b08b23cccb
ms.sourcegitcommit: ce934aca02072bdd2ec8d01dcbdca39134436359
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2017
---
# <a name="introduction-to-azure-advisor"></a>Bevezetés az Azure Advisor

További tudnivalók az Azure Advisor főbb képességei, és gyakran feltett kérdésekre adott válaszok.

## <a name="what-is-advisor"></a>Mi az az Advisor?
Az Advisor egy személyre szabott felhő tanácsadó, amelynek segítségével hajtsa végre az ajánlott eljárások az Azure-környezetekhez optimalizálása érdekében. Az erőforrás-konfigurációhoz és használat telemetriai adatai elemzi, és az, amelyek javítják a költséghatékonyság, a teljesítmény, a magas rendelkezésre állású és az Azure-erőforrások biztonsági megoldások javasolja.

Az Advisor szolgáltatásban a következőket teheti:
* Szerezze be a proaktív, hajtható végre, és személyre szabott ajánlott eljárások a. 
* A teljes Azure csökkentése érdekében lehetőségek meghatározása teljesítmény, biztonsági és az erőforrások magas rendelkezésre állás javítása televíziózással töltenek.
* A javasolt műveletek beágyazott javaslatok beszerzése.

Az Advisor keresztül érheti el a [Azure-portálon](https://aka.ms/azureadvisordashboard). Jelentkezzen be a [portal](https://portal.azure.com), keresse meg **Advisor** a navigációs menü, vagy keresse meg azt a **további szolgáltatások** menü.

Az Advisor irányítópult megjeleníti az előfizetések személyre szabott javaslatok.  Szűrőket adott előfizetések és erőforrástípusok javaslatok megjelenítése.  A javaslatok négy kategóriába oszthatók: 

* **Magas rendelkezésre állású**: Győződjön meg arról, az üzleti szempontból kritikus fontosságú alkalmazások folytonosságának javításához. További információkért lásd: [Advisor magas rendelkezésre állású javaslatok](advisor-high-availability-recommendations.md).
* **Biztonsági**: veszélyek és biztonsági problémák vezethet biztonsági rések észlelése. További információkért lásd: [Advisor biztonsági javaslatok](advisor-security-recommendations.md).
* **Teljesítmény**: az alkalmazások a sebesség növelése érdekében. További információkért lásd: [Advisor teljesítmény javaslatok](advisor-performance-recommendations.md).
* **Költség**: optimalizálása és a teljes Azure kiadásait. További információkért lásd: [Advisor költség javaslatok](advisor-cost-recommendations.md).

  ![Az Advisor javaslat típusok](./media/advisor-overview/advisor-dashboard.png)

> [!NOTE]
> Az előfizetéshez, előfizetés Azure Advisor használandó *tulajdonos* kell indítsa el az Advisor irányítópulton.  Ez a művelet regisztrálja az előfizetés az Advisor szolgáltatásban.  Bármely előfizetés meg *tulajdonos*, *közreműködő*, vagy *olvasó* férhetnek hozzá az előfizetés az Advisor-javaslatokra. 

Egy kategóriát az adott kategóriába javaslatok listájának megjelenítéséhez kattintson, és válassza ki a javaslatra kattintva olvashat azokról bővebben.  Lehetőség előnyeit, vagy hárítsa el a problémát a végrehajtható műveletek is olvashat.

![Az Advisor javaslat kategória](./media/advisor-overview/advisor-ha-category-example.png) 

Válassza ki ahhoz a javaslat megvalósításához ajánlott beavatkozást.  Egy egyszerű felületen nyílik meg, amely lehetővé teszi a javaslat megvalósítását, vagy tekintse meg a dokumentációt, amely segítséget nyújt végrehajtására.  Ajánlást bevezetése után is igénybe vehet az Advisor ismeri fel, amely naponta.

Ha nem kíván azonnal intézkedhet ajánlása, emlékeztet, hogy egy adott időszakra vonatkozóan, vagy zárja be azt.  Ha nem szeretne kapni egy előfizetéshez vagy erőforráscsoporthoz javaslatok, konfigurálhatja az Advisor csak a megadott előfizetésekhez és erőforráscsoportokhoz javaslatok létrehozni.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-do-i-access-advisor"></a>Hogyan érhetem el az Advisor?
Az Advisor keresztül érheti el a [Azure-portálon](https://aka.ms/azureadvisordashboard). Jelentkezzen be a [portal](https://portal.azure.com), keresse meg **Advisor** a navigációs menü, vagy keresse meg azt a **további szolgáltatások** menü.

Javaslatokat biztosít a virtuális gép erőforrás-felületen keresztül is megtekintheti. Válassza ki a virtuális gépet, és görgessen a menü javaslatokat biztosít. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Milyen engedélyekkel Advisor elérésére van?

Az előfizetéshez tartozó Advisor-javaslatokra megszerzéséhez először regisztrálnia kell az előfizetés az Advisor szolgáltatásban. Egy előfizetés regisztrálva van, ha az előfizetés *tulajdonos* elindítja az Advisor irányítópulton. Ez az műveletet egyszer kell elvégezni. Az előfizetés regisztrálása után érheti el, az Advisor-javaslatokra *tulajdonos*, *közreműködő*, vagy *olvasó* az előfizetés.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Milyen erőforrásokat Advisor nyújt javaslatokat?

Az Advisor virtuális gépek rendelkezésre állási készletek, alkalmazásátjárót, alkalmazásszolgáltatások, SQL Server-kiszolgálók, SQL-adatbázisok és Redis Cache vonatkozó javaslatokkal szolgál.

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>Emlékeztet vagy hagyja figyelmen kívül az ajánlás?

Emlékeztet, vagy hagyja figyelmen kívül az ajánlás olyan környezetekben, kattintson a **emlékeztet** hivatkozásra. Megadhat egy időtartamot időszak vagy select **soha** elvetni a javaslat.

## <a name="next-steps"></a>Következő lépések

Az Advisor-javaslatokra kapcsolatos további információkért lásd:

* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Magas rendelkezésre állású javaslatokat biztosít](advisor-high-availability-recommendations.md)
* [Biztonsági javaslatokat biztosít](advisor-security-recommendations.md)
* [Teljesítmény javaslatokat biztosít](advisor-performance-recommendations.md)
* [Költség javaslatokat biztosít](advisor-cost-recommendations.md)
