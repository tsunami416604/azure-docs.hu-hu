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
ms.topic: article
ms.date: 05/07/2018
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 084b8fa0addbe2ddeee699b58c9b0de1a6f27d99
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
ms.locfileid: "33938692"
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
 
Ha Ön rendszergazda, és több felhasználó van társítva a munkaterülethez, a felhasználók és a munkaterület közötti társítás megszűnik. Ha a felhasználók hozzárendelve egyéb munkaterületekkel, majd tovább Naplóelemzési használatával ezek munkaterületeket. Azonban, ha nincs hozzájuk társítva az egyéb munkaterületekkel majd akkor kell használni a Naplóelemzési munkaterület létrehozása. 

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com). 
2. Az Azure Portalon kattintson a bal alsó sarokban található **További szolgáltatások** elemre. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza a **Log Analytics** elemet.
3. A Naplóelemzési előfizetések ablaktáblán jelölje ki a munkaterület, és kattintson **törlése** teteje és a középső ablaktáblán.<br><br> ![A munkaterület tulajdonságai ablak beállítás törlése](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. Amikor a megerősítő üzenet ablak megjelenik, amely kéri a munkaterület törlés jóváhagyásához kattintson **Igen**.<br><br> ![Munkaterület törlésének megerősítése](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

