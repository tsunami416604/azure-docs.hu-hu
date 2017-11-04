---
title: "Egy Azure Naplóelemzési munkaterület törlése |} Microsoft Docs"
description: "Útmutató: a Naplóelemzési munkaterület törlése, ha van ilyen, a személyes előfizetés, vagy átalakíthatja a munkaterület-modell."
services: log-analytics
documentationcenter: log-analytics
author: mgoedtel
manager: carmonm
editor: 
ms.assetid: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/27/2017
ms.author: magoedte
ms.custom: mvc
ms.openlocfilehash: 3d99f9869dfdfbe18f82e873bd367cc85f9414f1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="delete-an-azure-log-analytics-workspace-with-the-azure-portal"></a>És az Azure portál egy Azure Naplóelemzési munkaterület törlése
Ez a témakör bemutatja, hogyan használható az Azure-portálon törölni a Naplóelemzési workpace, előfordulhat, hogy már nem szükséges. 

## <a name="to-delete-a-workspace"></a>Munkaterület törlése 
A Naplóelemzési munkaterület törlése, ha a munkaterület kötődő összes adat törlődik a szolgáltatás számított 30 napon belül.  Körültekintően a munkaterület törlése, mivel néhány fontos adatok és a konfiguráció negatívan befolyásolhatja a szolgáltatási műveletek szeretné.  
 
Ha Ön rendszergazda, és több felhasználó van társítva a munkaterülethez, a felhasználók és a munkaterület közötti társítás megszűnik. Ha a felhasználók hozzárendelve egyéb munkaterületekkel, majd tovább Naplóelemzési használatával ezek munkaterületeket. Azonban, ha nincs hozzájuk társítva az egyéb munkaterületekkel majd akkor kell használni a Naplóelemzési munkaterület létrehozása. 

1. Jelentkezzen be az [Azure Portalra](http://portal.azure.com). 
2. Az Azure portálon kattintson **további szolgáltatások** bal alsó sarokban található. Az erőforrások listájába írja be a **Log Analytics** kifejezést. Ahogy elkezd gépelni, a lista a beírtak alapján szűri a lehetőségeket. Válassza ki **Analytics jelentkezzen**.
3. A Naplóelemzési előfizetések ablaktáblán jelölje ki a munkaterület, és kattintson **törlése** teteje és a középső ablaktáblán.<br><br> ![A munkaterület tulajdonságai ablak beállítás törlése](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace.png)<br>  
4. Amikor a megerősítő üzenet ablak megjelenik, amely kéri a munkaterület törlés jóváhagyásához kattintson **Igen**.<br><br> ![Munkaterület törlésének megerősítése](media/log-analytics-manage-del-workspace/log-analytics-delete-workspace-confirm.png)

