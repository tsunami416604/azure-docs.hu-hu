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
ms.devlang: na
ms.topic: conceptual
ms.date: 05/07/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: c04cc601152001dabc0cf152918c8d0ec884c7ed
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2018
ms.locfileid: "48041136"
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

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com). 
2. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
3. A Log Analytics-előfizetések ablaktábláján válasszon ki egy munkaterületet, és kattintson a **törlése** a középső ablaktábla tetején.<br><br> ![A munkaterület tulajdonságait tartalmazó ablaktáblán a Törlés elemet](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. Rákérdez arra, hogy a munkaterület törlésének megerősítéséhez, majd a megerősítési üzenet ablak megjelenésekor **Igen**.<br><br> ![Munkaterület törlésének megerősítése](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

