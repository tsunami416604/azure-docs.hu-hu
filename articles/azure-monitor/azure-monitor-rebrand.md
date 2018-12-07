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
ms.date: 09/17/2018
ms.author: bwren
ms.openlocfilehash: 68986b3d60566ee190a92c6ccf8cda767824350f
ms.sourcegitcommit: 2469b30e00cbb25efd98e696b7dbf51253767a05
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "53000155"
---
# <a name="azure-monitor-branding-changes"></a>Az Azure Monitor márkajelzési módosítások
Ez a cikk ismerteti a legutóbbi márkajelzési és Azure felügyeleti szolgáltatások nemrég végrehajtott változások. 

## <a name="consolidation-of-monitoring-services-into-azure-monitor"></a>A szolgáltatások figyelése az Azure Monitor szolgáltatásba összevonása
A log Analytics és az Application Insights az Azure-erőforrások és a hibrid környezetek figyelése egy egyetlen integrált megoldást nyújt az Azure Monitor szolgáltatásba összevontuk. Ezek a szolgáltatások egyik funkció sem el lett távolítva, és a felhasználók el tudják végezni a ugyanezeket a forgatókönyveket, amelyek mindig veszteség és a szolgáltatásokat biztonságának sérüléséhez nélkül már befejeződött.

Ezen szolgáltatások mindegyikéhez dokumentáció az Azure Monitor összevontuk a t egyetlen tartalom. Ez segítséget nyújt az olvasót egy adott figyelési forgatókönyv esetében a tartalom mindegyikét ellentétben nem hivatkozhat a tartalom több példányban kell egyetlen helyen keres. Az összevont szolgáltatás haladásával, mivel válik több integrált a tartalmat.

## <a name="log-analytics-redefinition"></a>Log Analytics újradefiniálása
A log Analytics rendelkezik központi szerepet játszott az Azure felügyeleti telemetriai és egyéb adatokat gyűjt különböző forrásokból, és a egy lekérdezési nyelvet és elemzési motor, amely betekintést nyerhet az alkalmazások és erőforrások működését, megadásával. Töltse ki az Azure Monitor szolgáltatásban a kritikus szerepkör továbbra is. Fog funkciók az Azure monitor, helyezze át más olyan funkció, például az ügynökök és a megoldások a Log Analytics részének tekintendők. Azok a funkciók nem változtak, az Azure Portalon szerzett megtalálhatja a javítási lehetőségeket, mint más,

## <a name="retirement-of-operations-management-suite-brand"></a>Az Operations Management Suite márka kivonása
Az Operations Management Suite (OMS) volt a következő Azure felügyeleti szolgáltatások licencelési célokra képezte:

- Application Insights
- Azure Automation
- Azure Backup
- Log Analytics
- Site Recovery

[Új díjszabás vezettek be ezeket a szolgáltatásokat a](https://azure.microsoft.com/blog/introducing-a-new-way-to-purchase-azure-monitoring-services/), és az OMS a kötegelés már nem érhető el új ügyfelek számára. Az OMS részét képező szolgáltatások egyike módosult, kivéve a fent leírt Azure Monitor, az összesítést. 



## <a name="next-steps"></a>További lépések

- Olvassa el az [Azure Monitor szolgáltatás áttekintése](overview.md) , amely ismerteti a különböző összetevők és szolgáltatások.
- További információ a [átvitelét az OMS-portálon](../azure-monitor/platform/oms-portal-transition.md).