---
title: Az Azure Monitor kevésébe |} A Microsoft Docs
description: Ismerteti a legutóbbi márkajelzési és az Azure felügyeleti szolgáltatások nemrég végrehajtott változások.
author: bwren
manager: carmonm
editor: tysonn
services: azure-monitor
documentationcenter: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/18/2019
ms.author: bwren
ms.openlocfilehash: 874c0eca97ba7fea7543e41c75e642108141047f
ms.sourcegitcommit: 4bf542eeb2dcdf60dcdccb331e0a336a39ce7ab3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56408493"
---
# <a name="azure-monitor-naming-and-terminology-changes"></a>Az Azure Monitor elnevezési és terminológiát módosítások
Jelentős módosultak az Azure monitornak nemrég, a különböző szolgáltatásokkal, konszolidált annak érdekében, hogy megkönnyítheti a figyelést az Azure-ügyfelek számára. Ez a cikk ismerteti a legutóbbi neve és az Azure Monitor dokumentációja terminológia változásait.

## <a name="february-2019---log-analytics-terminology"></a>Február a 2019 - Log Analytics-terminológia
Után az Azure Monitor különböző szolgáltatások összevonása azt még tart a következő lépés az általunk kiadott dokumentációról jobban írja le az Azure Monitor szolgáltatás és különböző összetevőjét terminológia módosításával. 

### <a name="log-analytics"></a>Log Analytics
Az Azure Monitor log adatok nem a Log Analytics-munkaterületen tárolja, és továbbra is gyűjti, és az ugyanazon a Log Analytics szolgáltatás által elemzett, de az időszak azért módosítottuk _Log Analytics_ több helyen való _Azure Monitor-naplók_ . Ez a kifejezés jobban az Azure monitorban szerepét, és biztosítja a hatékonyabb konzisztencia [metrikák az Azure monitorban](platform/data-collection.md).

Az előfizetési időszak _log analytics_ elsődlegesen vonatkozik az oldal írható, és lekérdezéseket futtathat, és elemezheti a naplófájlok adatait az Azure Portalon. A működési egyenértékű [metrikaböngésző](platform/metrics-charts.md), ez az oldal metrikai adatok elemzéséhez az Azure Portalon.

### <a name="log-analytics-workspaces"></a>Log Analytics-munkaterületek
[Munkaterületek](platform/manage-access.md) , amely a naplóadatokat az Azure monitorban továbbra is nevezik Log Analytics-munkaterületek tartsa. A **Log Analytics** menü az Azure Portalon kapott **Log Analytics-munkaterületek** és itt, [hozzon létre új munkaterületek](learn/quick-create-workspace.md) és adatforrások konfigurálása. A naplók és más monitorozási adatok elemzése **Azure Monitor** és konfigurálja a munkaterületek **Log Analytics-munkaterületek**.

### <a name="management-solutions"></a>Felügyeleti megoldások
[Felügyeleti megoldások](insights/solutions.md) átnevezték _figyelési megoldások_, amely jobban funkcióit ismerteti.


## <a name="august-2018---consolidation-of-monitoring-services-into-azure-monitor"></a>2018 augusztus - szolgáltatások figyelése az Azure Monitor szolgáltatásba összevonása
A log Analytics és az Application Insights az Azure-erőforrások és a hibrid környezetek figyelése egy egyetlen integrált megoldást nyújt az Azure Monitor szolgáltatásba összevontuk. Ezek a szolgáltatások egyik funkció sem el lett távolítva, és a felhasználók el tudják végezni a ugyanezeket a forgatókönyveket, amelyek mindig veszteség és a szolgáltatásokat biztonságának sérüléséhez nélkül már befejeződött.

Ezen szolgáltatások mindegyikéhez dokumentáció az Azure Monitor összevontuk a t egyetlen tartalom. Ez segítséget nyújt az olvasót egy adott figyelési forgatókönyv esetében a tartalom mindegyikét ellentétben nem hivatkozhat a tartalom több példányban kell egyetlen helyen keres. Az összevont szolgáltatás haladásával, mivel válik több integrált a tartalmat.

Azure monitor lehetőségként lettek figyelembe véve a Log Analytics részét, például az ügynökök és a nézetek más szolgáltatások is elrejtését rendelkezik. Azok a funkciók nem változtak, az Azure Portalon szerzett megtalálhatja a javítási lehetőségeket, mint más,


## <a name="april-2018---retirement-of-operations-management-suite-brand"></a>2018 április - használatból való kivonást egyaránt az Operations Management Suite márka
Az Operations Management Suite (OMS) volt a következő Azure felügyeleti szolgáltatások licencelési célokra képezte:

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[Új díjszabás vezettek be ezeket a szolgáltatásokat a](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), és az OMS a kötegelés már nem érhető el új ügyfelek számára. Az OMS részét képező szolgáltatások egyike módosult, kivéve a fent leírt Azure Monitor, az összesítést. 




## <a name="next-steps"></a>További lépések

- Olvassa el az [Azure Monitor szolgáltatás áttekintése](overview.md) , amely ismerteti a különböző összetevők és szolgáltatások.
- További információ a [átvitelét az OMS-portálon](../log-analytics/log-analytics-oms-portal-transition.md).