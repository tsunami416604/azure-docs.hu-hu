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
ms.openlocfilehash: 96925f251cf4984a11516a962740e19a7b9589dc
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="introduction-to-azure-advisor"></a>Bevezetés az Azure Advisor

Tudnivalók az Azure Advisor és annak főbb funkcióiról, és gyakran feltett kérdésekre adott válaszok.

## <a name="what-is-advisor"></a>Mi az az Advisor?
Az Advisor egy személyre szabott felhő tanácsadó, amelynek segítségével hajtsa végre az ajánlott eljárások az Azure-környezetekhez optimalizálása érdekében. Az erőforrás-konfigurációhoz és használat telemetriai adatai elemzi, és az, amelyek javítják a költséghatékonyság, a teljesítmény, a magas rendelkezésre állású és az Azure-erőforrások biztonsági megoldások javasolja.

Az Advisor szolgáltatásban a következőket teheti:
* Szerezze be a proaktív, hajtható végre, és személyre szabott ajánlott eljárások a. 
* A teljes Azure csökkentése érdekében lehetőségek meghatározása teljesítmény, biztonsági és az erőforrások magas rendelkezésre állás javítása televíziózással töltenek.
* A javasolt műveletek beágyazott javaslatok beszerzése.

Az Advisor keresztül érheti el a [Azure-portálon](https://aka.ms/azureadvisordashboard). Jelentkezzen be a [portal](https://portal.azure.com), jelölje be **Tallózás**, majd görgessen a **Azure Advisor**. Az Advisor irányítópulton megjelenített személyre szabott javaslatok a kiválasztott előfizetéshez. 

A javaslatok négy kategóriába oszthatók: 

* **Magas rendelkezésre állású**: Győződjön meg arról, az üzleti szempontból kritikus fontosságú alkalmazások folytonosságának javításához. További információkért lásd: [Advisor magas rendelkezésre állású javaslatok](advisor-high-availability-recommendations.md).

* **Biztonsági**: veszélyek és biztonsági problémák vezethet biztonsági rések észlelése. További információkért lásd: [Advisor biztonsági javaslatok](advisor-security-recommendations.md).

* **Teljesítmény**: az alkalmazások a sebesség növelése érdekében. További információkért lásd: [Advisor teljesítmény javaslatok](advisor-performance-recommendations.md).

* **Költség**: optimalizálása és a teljes Azure televíziózással töltenek. További információkért lásd: [Advisor költség javaslatok](advisor-cost-recommendations.md).

  ![Az Advisor javaslat típusok](./media/advisor-overview/advisor-all-tab-examples.png)

> [!NOTE]
> Advisor-javaslatokra szeretne használni, először *az előfizetés regisztrálása* az Advisor szolgáltatásban. Egy előfizetés regisztrálva amikor egy *előfizetés tulajdonosának* elindítja az Advisor irányítópulton, és rákattint a **javaslatok beszerzése** gombra. Ez egy *egyszeri művelet*. Az előfizetés regisztrálása után érheti el, az Advisor-javaslatokra *tulajdonos*, *közreműködő*, vagy *olvasó* előfizetés, egy erőforráscsoport vagy egy adott erőforrás.

Kattintson a javaslatra kattintva olvashat azokról bővebben. Lehetőség előnyeit, vagy hárítsa el a problémát a végrehajtható műveletek is olvashat. 

Az Advisor ezekre vonatkozó javaslatokat beágyazott műveletek vagy dokumentáció hivatkozásokat. Egy beágyazott művelet végigvezeti Önt egy "az interaktív felhasználói út" végrehajtásához. Dokumentáció hivatkozásra kattintva megtudhatja a dokumentáció, és ismerteti, hogyan lehet manuálisan valósítja meg a műveletet. 

Az Advisor óránként frissíti a javaslatokat. Ha nem szeretné azonnal intézkedhet ajánlása, emlékeztet, hogy egy adott időszakra vonatkozóan, vagy zárja be azt. 

## <a name="frequently-asked-questions"></a>Gyakori kérdések

### <a name="how-do-i-access-advisor"></a>Hogyan érhetem el az Advisor?
Az Advisor keresztül érheti el a [Azure-portálon](https://aka.ms/azureadvisordashboard). Jelentkezzen be a [portal](https://portal.azure.com), jelölje be **Tallózás**, majd görgessen a **Azure Advisor**. Az Advisor irányítópulton megjelenített személyre szabott javaslatok a kiválasztott előfizetéshez. 

Javaslatokat biztosít a virtuális gép erőforráspaneljének keresztül is megtekintheti. Válassza ki a virtuális gépet, és görgessen a menü javaslatokat biztosít. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Milyen engedélyekkel Advisor elérésére van?

Advisor-javaslatokra szeretne használni, először *az előfizetés regisztrálása* az Advisor szolgáltatásban. Egy előfizetés regisztrálva amikor egy *előfizetés tulajdonosának* elindítja az Advisor irányítópulton, és rákattint a **javaslatok beszerzése** gombra. Ez egy *egyszeri művelet*. Az előfizetés regisztrálása után érheti el, az Advisor-javaslatokra *tulajdonos*, *közreműködő*, vagy *olvasó* előfizetés, egy erőforráscsoport vagy egy adott erőforrás.

### <a name="how-often-are-advisor-recommendations-updated"></a>Milyen gyakran történik az Advisor-javaslatokra frissíteni?

Advisor-javaslatokra óránként frissíti.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Milyen erőforrásokat Advisor nyújt javaslatokat?

Az Advisor virtuális gépek rendelkezésre állási készletek, alkalmazásátjárót, alkalmazásszolgáltatások, SQL Server-kiszolgálók, SQL-adatbázisok és Redis Cache vonatkozó javaslatokkal szolgál.

### <a name="can-i-snooze-or-dismiss-a-recommendation"></a>Emlékeztet vagy hagyja figyelmen kívül az ajánlás?

Emlékeztet, vagy hagyja figyelmen kívül az ajánlás olyan környezetekben, kattintson a **emlékeztet** gombra vagy hivatkozásra. Megadhat egy időtartamot időszak vagy select **soha** elvetni a javaslat.

## <a name="next-steps"></a>Következő lépések

Az Advisor-javaslatokra kapcsolatos további információkért lásd:

* [Bevezetés az Advisor használatába](advisor-get-started.md)
* [Magas rendelkezésre állású javaslatokat biztosít](advisor-high-availability-recommendations.md)
* [Biztonsági javaslatokat biztosít](advisor-security-recommendations.md)
* [Teljesítmény javaslatokat biztosít](advisor-performance-recommendations.md)
* [Költség javaslatokat biztosít](advisor-cost-recommendations.md)
