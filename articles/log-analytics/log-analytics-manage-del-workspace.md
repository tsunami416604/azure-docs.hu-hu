---
title: Egy Azure Naplóelemzési munkaterület törlése |} Microsoft Docs
description: 'Útmutató: a Naplóelemzési munkaterület törlése, ha van ilyen, a személyes előfizetés, vagy átalakíthatja a munkaterület-modell.'
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
ms.component: na
ms.openlocfilehash: 54f2af60751ed0d9c64e71efad6fa9aa3ef06589
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37129115"
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>És az Azure portál egy Azure Naplóelemzési munkaterület törlése
Ez a cikk bemutatja, hogyan használható az Azure-portálon lehetséges, hogy már nem szükséges a Naplóelemzési munkaterület törlése. 

## <a name="to-delete-a-workspace"></a>Munkaterület törlése 
A Naplóelemzési munkaterület törlése, ha a munkaterület kötődő összes adat törlődik a szolgáltatás számított 30 napon belül.  Körültekintően a munkaterület törlése, mivel néhány fontos adatok és a konfiguráció negatívan befolyásolhatja a szolgáltatási műveletek szeretné. Vegye figyelembe a más Azure-szolgáltatások és adatforrások, amely tárolja az adatokat a Naplóelemzési, például:

* Application Insights
* Azure Security Center
* Azure Automation
* A Windows és Linux rendszerű virtuális gépeken futó ügynökök
* Operációs rendszert futtató ügynökökön Windows és Linux rendszerű számítógépek a környezetben
* System Center Operations Manager
* Felügyeleti megoldások 

Bármely ügynökök és a System Center Operations Manager felügyeleti csoportok úgy konfigurálva, hogy a munkaterületet jelentés továbbra is árva állapotban.  A készlet milyen ügynökök, megoldások, és más Azure-szolgáltatások integrálva van a munkaterület, mielőtt továbblép.   
 
Ha Ön rendszergazda, és több felhasználó van társítva a munkaterülethez, a felhasználók és a munkaterület közötti társítás megszűnik. Ha a felhasználók más munkaterületekkel is társítva vannak, továbbra is használhatják a Log Analyticset azokkal a munkaterületekkel. Azonban, ha nincs hozzájuk társítva az egyéb munkaterületekkel majd akkor kell használni a Naplóelemzési munkaterület létrehozása. 

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com). 
2. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
3. A Naplóelemzési előfizetések ablaktáblán jelölje ki a munkaterület, és kattintson **törlése** teteje és a középső ablaktáblán.<br><br> ![A munkaterület tulajdonságai ablak beállítás törlése](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. Amikor a megerősítő üzenet ablak megjelenik, amely kéri a munkaterület törlés jóváhagyásához kattintson **Igen**.<br><br> ![Munkaterület törlésének megerősítése](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

