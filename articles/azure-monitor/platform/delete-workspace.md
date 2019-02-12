---
title: Az Azure Log Analytics-munkaterület törlése |} A Microsoft Docs
description: Ismerje meg a Log Analytics-munkaterület törlése, ha van ilyen egy személyes előfizetésben, vagy átalakíthatja a munkaterület-modell.
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: magoedte
ms.openlocfilehash: a6542838acba3143123dc90d96746179a2b4469b
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55989134"
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>Az Azure Portalon az Azure Log Analytics-munkaterület törlése
Ez a cikk bemutatja, hogyan használhatja az Azure Portalon, előfordulhat, hogy már nincs szüksége a Log Analytics-munkaterület törlése. 

## <a name="to-delete-a-workspace"></a>Munkaterület törlése 
Ha töröl egy Log Analytics-munkaterületet, a munkaterülethez kapcsolódó összes adat törlődik a szolgáltatás 30 napon belül.  Munkaterület törlése, mivel néhány fontos adatok és a konfigurációt, amely negatív hatással lehet a szolgáltatási műveletek körültekintően szeretné. Vegye figyelembe a többi Azure-szolgáltatások és adatforrások, amely tárolja az adatokat a Log Analyticsben, mint például:

* Application Insights
* Azure Security Center
* Azure Automation
* Windows és Linux rendszerű virtuális gépeken futó ügynökök
* A Windows és Linux rendszerű számítógépek a környezetben futó ügynökök
* System Center Operations Manager
* Felügyeleti megoldások 

Ügynököket és a munkaterület elküldjék a System Center Operations Manager felügyeleti csoportok továbbra is árva állapotban.  A készlet milyen ügynökök, a megoldásokat, és más Azure-szolgáltatásokkal integrálva van a munkaterület folytatás előtt.   
 
Ha Ön rendszergazda, és több felhasználó van társítva a munkaterülethez, a felhasználók és a munkaterület közötti társítás megszűnik. Ha a felhasználók más munkaterületekkel is társítva vannak, továbbra is használhatják a Log Analyticset azokkal a munkaterületekkel. Azonban ha azok nem más munkaterületekhez társítva majd szükségük hozzon létre egy munkaterületet a Log Analytics használatához. 

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com). 
2. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza ki **Log Analytics-munkaterületek**.
3. A Log Analytics-előfizetések ablaktábláján válasszon ki egy munkaterületet, és kattintson a **törlése** a középső ablaktábla tetején.<br><br> ![A munkaterület tulajdonságait tartalmazó ablaktáblán a Törlés elemet](media/delete-workspace/log-analytics-delete-workspace.png)<br>  
4. Rákérdez arra, hogy a munkaterület törlésének megerősítéséhez, majd a megerősítési üzenet ablak megjelenésekor **Igen**.<br><br> ![Munkaterület törlésének megerősítése](media/delete-workspace/log-analytics-delete-workspace-confirm.png)

