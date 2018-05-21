---
title: Az App Services - verem Azure feldolgozói szerepkörök kibővítési |} Microsoft Docs
description: Részletes útmutatás méretezését Azure verem App Service szolgáltatások
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: 04a93bc841d553296dca7635151c14892970121c
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
---
# <a name="app-service-on-azure-stack-add-more-infrastructure-or-worker-roles"></a>App Service Azure veremben: további infrastruktúra vagy feldolgozói szerepkörök hozzáadása

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*  

Ez a dokumentum bemutatja, Azure verem infrastruktúra és a feldolgozói szerepkörök az App Service méretezése. Bármilyen méretű alkalmazások támogatásához további feldolgozói szerepkörök létrehozásának lépései tartalmaz.

> [!NOTE]
> Ha az Azure-verem környezethez nem rendelkezik több mint 96 GB RAM, előfordulhat, hogy további kapacitást problémák.

App Service Azure veremben, alapértelmezés szerint a szabad és megosztott worker rétegek támogatja. Más munkavégző rétegek hozzáadásához kell hozzáadnia további feldolgozói szerepköröket.

Ha nem biztos abban, hogy mi telepítették az App Service alapértelmezett Azure verem telepítésen, a további információkat ellenőrizhet a [Azure verem áttekintése az App Service](azure-stack-app-service-overview.md).

Az Azure App Service Azure veremben összes szerepkör virtuálisgép-méretezési csoportok használatával telepíti, és ilyen kihasználja a munkaterhelés méretezési képességeket. Ezért a munkavégző rétegek összes skálázás használatával történik az App Service-rendszergazdához.

> [!IMPORTANT]
> Jelenleg nincs lehetőség a virtuálisgép-méretezési csoportok méretezni a portálon, az az Azure-verem kibocsátási megjegyzések, ezért a PowerShell példát követve kiterjesztése.
>
>

## <a name="add-additional-workers-with-powershell"></a>A PowerShell használatával további dolgozók hozzáadása

1. [A telepítő a PowerShell Azure verem felügyeleti környezet](azure-stack-powershell-configure-admin.md)

2. Ez a példa segítségével a méretezési kiterjesztése:
   ```powershell
   
    ##### Scale out the AppService Role instances ######
   
    # Set context to AzureStack admin.
    Login-AzureRmAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if your VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
   ```    

   > [!NOTE]
   > Ez a lépés attól függően, hogy milyen típusú szerepkör és a példányok száma órát számos is igénybe vehet.
   >
   >

3. Az App Service Adminisztráció új szerepkör-példányok állapotának figyelésével, ellenőrizze, hogy egyes szerepkör-példány állapotának a felhasználóiszerepkör-típus a listában kattintson.

## <a name="add-additional-workers-directly-within-the-app-service-resource-provider-admin"></a>Adja hozzá a további munkavállalók közvetlenül belül az App Service erőforrás-szolgáltató rendszergazdához.

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

## <a name="result"></a>Eredmény

Miután bekerültek a teljes körűen rendszerbe állított és készen áll, a munkavállalók rendelkezésre állásúvá válik a felhasználók számára a munkaterhelés alakzatot őket telepíteni. A következő példáját mutatja be a több elérhető árképzési szinteket alapértelmezés szerint. Ha egy adott munkavégző szinten elérhető dolgozókat, választhatja ki a megfelelő tarifacsomag nem érhető el.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> Felügyeleti kiterjesztése, előtér vagy a közzétevő szerepkört adja hozzá a megfelelő típusú szerepkörként kell horizontális. 
>
>

Horizontális kezelés, az előtér vagy a közzétevő szerepkört, kövesse a lépéseket, jelölje ki a megfelelő szerepkörrel. Tartományvezérlők méretezési készlet nem üzemelnek, és ezért két kell telepíteni az összes üzemi környezetek telepítéskor.

### <a name="next-steps"></a>További lépések

[Központi telepítés forrásának konfigurálása](azure-stack-app-service-configure-deployment-sources.md)
