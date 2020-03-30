---
title: Az Azure Monitor terminológiai frissítései | Microsoft dokumentumok
description: Az Azure figyelési szolgáltatásaiban végrehajtott legutóbbi terminológiai módosítások ismertetése.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/08/2019
ms.openlocfilehash: 0c5e1096b0780d2f9e50389f772e63344935012b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79274137"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Az Azure Monitor elnevezése és terminológiai változásai
A közelmúltban jelentős módosítások történtek az Azure Monitoron, a különböző szolgáltatások konszolidálása az Azure-ügyfelek figyelésének egyszerűsítése érdekében. Ez a cikk az Azure Monitor dokumentációjában a legutóbbi név- és terminológiai változásokat ismerteti.

## <a name="october-2019---diagnostic-log-to-resource-log"></a>2019. október – Diagnosztikai napló az erőforrásnaplóba
A "diagnosztikai naplók" "erőforrásnaplókra" módosultak, hogy jobban megfeleljenek a ténylegesen gyűjtött adatoknak. A "diagnosztikai beállítások" kifejezés ugyanaz marad.  

## <a name="february-2019---log-analytics-terminology"></a>2019. február - Log Analytics terminológia
A különböző szolgáltatások Azure Monitor alatti összevonása után a következő lépést azzal tesszük meg, hogy a dokumentációban módosítjuk a terminológiát, hogy jobban leírhassuk az Azure Monitor szolgáltatást és annak különböző összetevőit. 

### <a name="log-analytics"></a>Log Analytics
Az Azure Monitor naplóadatai továbbra is a Log Analytics-munkaterületen tárolódnak, és ugyanaz a Log Analytics-szolgáltatás továbbra is gyűjti és elemzi, de a _Log Analytics_ kifejezést sok helyen módosítjuk az Azure _Monitor naplóira._ Ez a kifejezés jobban tükrözi az Azure Monitorban betöltött szerepét, és jobb konzisztenciát biztosít [az Azure Monitor metrikáival.](platform/data-platform-metrics.md)

A _kifejezés log analytics_ most elsősorban az Azure Portalon a lekérdezések írása és futtatása, valamint a naplóadatok elemzése kifejezés. Ez a [metrikák explorer](platform/metrics-charts.md)funkcionális megfelelője, amely az Azure Portalon a metrikaadatok elemzésére használt lap.

### <a name="log-analytics-workspaces"></a>Log Analytics-munkaterületek
Az Azure Monitorban naplóadatokat tároló [munkaterületeket](platform/manage-access.md) továbbra is Log Analytics-munkaterületnek nevezzük. Az Azure Portal **On-di** analytics menüje átlett nevezve **Log Analytics-munkaterületekre,** és itt [hozhat létre új munkaterületeket](learn/quick-create-workspace.md) és konfigurálhat adatforrásokat. Elemezze a naplókat és más figyelési adatokat az **Azure Monitorban,** és konfigurálja a munkaterületet a **Log Analytics-munkaterületeken.**

### <a name="management-solutions"></a>Felügyeleti megoldások
[A felügyeleti megoldásokat](insights/solutions.md) átnevezték _figyelési megoldásokra,_ amelyek jobban leírják azok funkcióit.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>2018. augusztus – A figyelési szolgáltatások összevonása az Azure Monitorba
A Log Analytics és az Application Insights az Azure Monitorba lett összesítve, így egyetlen integrált felhasználói élményt nyújt az Azure-erőforrások és hibrid környezetek figyeléséhez. Egyetlen funkció sem lett eltávolítva ezekből a szolgáltatásokból, és a felhasználók ugyanazokat a forgatókönyveket hajthatják végre, amelyeket mindig is végrehajtottak, a funkciók elvesztése vagy veszélyeztetése nélkül.

Az egyes szolgáltatások dokumentációja egyetlen tartalomkészletbe lett összesítve az Azure Monitorhoz. Ez segít az olvasónak abban, hogy egy adott figyelési forgatókönyv összes tartalmát egyetlen helyen találja meg, ahelyett, hogy több tartalomhalmazra kellene hivatkoznia. Az összevont szolgáltatás fejlődésével a tartalom egyre integráltabbá válik.

A Log Analytics részét használó egyéb funkciókat, például az ügynököket és a nézeteket is áthelyezték az Azure Monitor funkcióiként. A funkciójuk nem változott, kivéve az Azure Portalon szerzett felhasználói élményük lehetséges fejlesztéseit.


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>2018. április – Az Operations Management Suite márkakivonás
Az Operations Management Suite (OMS) a következő Azure-kezelési szolgáltatások licencelési célú összekapcsolása volt:

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[Új árképzést vezettek be ezekre a szolgáltatásokra](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), és az OMS csomagban értékesítés már nem érhető el az új ügyfelek számára. Az OMS részét használó szolgáltatások egyike sem változott, kivéve az Azure Monitorba történő, fent leírt összesítést. 




## <a name="next-steps"></a>További lépések

- Olvassa el [az Azure Monitor áttekintését,](overview.md) amely leírja a különböző összetevőket és funkciókat.
- További információ az [OMS portál áttűnéséről.](../log-analytics/log-analytics-oms-portal-transition.md)
