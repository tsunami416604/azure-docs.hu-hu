---
title: "Az App Services - verem Azure feldolgozói szerepkörök kibővítési |} Microsoft Docs"
description: "Részletes útmutatás méretezését Azure verem App Service szolgáltatások"
services: azure-stack
documentationcenter: 
author: brenduns
manager: femila
editor: 
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/29/2018
ms.author: brenduns
ms.reviewer: anwestg
ms.openlocfilehash: ddd9820715e964218db8b88fb5211b3725c808b9
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>App Service Azure veremben: további infrastruktúra vagy feldolgozói szerepkörök hozzáadása
*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*  

Ez a dokumentum bemutatja, Azure verem infrastruktúra és a feldolgozói szerepkörök az App Service méretezése. Bármilyen méretű alkalmazások támogatásához további feldolgozói szerepkörök létrehozásának lépései tartalmaz.

> [!NOTE]
> Ha több mint 96 GB RAM nem rendelkezik az Azure-verem környezethez elő további kapacitást.

App Service Azure veremben, alapértelmezés szerint a szabad és megosztott worker rétegek támogatja. Más munkavégző rétegek hozzáadásához kell hozzáadnia további feldolgozói szerepköröket.

Ha nem biztos abban, hogy mi telepítették az App Service alapértelmezett Azure verem telepítésen, a további információkat ellenőrizhet a [Azure verem áttekintése az App Service](azure-stack-app-service-overview.md).

Az Azure App Service Azure veremben összes szerepkör virtuálisgép-méretezési csoportok használatával telepíti, és ilyen kihasználja a munkaterhelés méretezési képességeket. Ezért a munkavégző rétegek összes skálázás használatával történik az App Service-rendszergazdához.

Adja hozzá a további munkavállalók közvetlenül belül az App Service erőforrás-szolgáltató rendszergazdához.

1. Jelentkezzen be a szolgáltatás-rendszergazdaként a verem Azure felügyeleti portálján.

2. Keresse meg a **alkalmazásszolgáltatások**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Kattintson a **szerepkörök**. Itt láthatja a telepített szerepkörök az App Service bontásban tartalmazza.

4. Kattintson jobb gombbal a méretezhető, és kattintson a kívánt típusú sor **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Kattintson a **méretezés**, válassza ki a kívánt méretezhető, és kattintson a példányok száma **mentése**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. Veremben Azure App Service fog most adja hozzá a további virtuális gépeket, konfigurálja őket, a szükséges szoftver telepítéséhez és megjelölhetők üzemkész, ha a folyamat befejeződik. Ez a folyamat a körülbelül 80 percet is igénybe vehet.

7. Az új szerepkörök vizsgálata előrehaladását a dolgozók megtekintésével figyelheti a **szerepkörök** panelen.

Miután bekerültek a teljes körűen rendszerbe állított és készen áll, a munkavállalók rendelkezésre állásúvá válik a felhasználók számára a munkaterhelés alakzatot őket telepíteni. A következő példáját mutatja be a több elérhető árképzési szinteket alapértelmezés szerint. Ha egy adott munkavégző szinten elérhető dolgozókat, választhatja ki a megfelelő tarifacsomag nem érhető el.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Kívánt felügyeleti réteget, előtér- vagy közzétevő szerepkörök hozzáadása horizontális kell a megfelelő Virtuálisgép-méretezési készlet. Ezeket a szerepköröket egy későbbi kiadásban az alkalmazásszolgáltatási management szolgáltatáson keresztül, a szolgáltatás adjuk hozzá.

Horizontális kezelés, az előtér vagy a közzétevő szerepkört, kövesse a lépéseket, jelölje ki a megfelelő szerepkörrel. Tartományvezérlők méretezési készlet nem üzemelnek, és ezért két kell telepíteni az összes üzemi környezetek telepítéskor.

### <a name="next-steps"></a>További lépések

[Központi telepítés forrásának konfigurálása](azure-stack-app-service-configure-deployment-sources.md)
