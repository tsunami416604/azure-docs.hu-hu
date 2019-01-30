---
title: 'App Service-ben az Azure Stack: Tartalék tartomány frissítése |} A Microsoft Docs'
description: Hogyan lehet Azure App Service az Azure Stacken újraterjeszteni a tartalék tartományok között
services: azure-stack
documentationcenter: ''
author: apwestgarth
manager: stefsch
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/05/2018
ms.author: anwestg
ms.lastreviewed: 09/05/2018
ms.openlocfilehash: 249e4b466e9be567111aaa22b40ca3e5dadb6cac
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55246381"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>Hogyan lehet Azure App Service az Azure Stacken újraterjeszteni a tartalék tartományok között

*Vonatkozik: Az Azure Stack integrált rendszerek*

Az 1802-es frissítés az Azure Stack mostantól támogatja a számítási feladatok eloszlása hibatartományokban, egy szolgáltatás, amelyet a kritikus fontosságú a magas rendelkezésre állás érdekében.

>[!IMPORTANT]
>Tartalék tartomány támogatása kihasználásához frissítenie kell az Azure Stackkel integrált rendszereknél 1802. Ez a dokumentum csak érvényes App Service erőforrás szolgáltató üzemelő példánya, amely az 1802-es frissítés előtt voltak befejeződött. Ha telepítette az Azure Stack App Service-ben a után az 1802-es frissítés került alkalmazásra, az Azure Stackhez, az erőforrás-szolgáltató már terjesztve van tartalék tartomány között.

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Az App Service erőforrás-szolgáltató újraegyensúlyozására tartalék tartomány között

A méretezési csoportok üzembe helyezve, az App Service erőforrás-szolgáltató újraterjesztése, a lépéseket kell elvégeznie ebben a cikkben minden egyes méretezési csoport esetében. Alapértelmezés szerint a méretezési csoport nevek a következők:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> Ha nem rendelkezik a feldolgozói réteg méretezési csoportok egyes üzembe helyezett, nem kell ezeket a méretezési csoportok újraegyensúlyozására. A méretezési csoportok kiegyensúlyozott megfelelően kell horizontális ki azokat a jövőben.

A méretezési csoportok horizontális, kövesse az alábbi lépéseket:

1. Jelentkezzen be az Azure Stack rendszergazdai portál.
1. Válassza az **Összes szolgáltatás** elemet.
2. Az a **számítási** kategória, jelölje be **a Virtual machine scale sets**. Meglévő méretezési csoportok az alkalmazásszolgáltatás üzemelő példányának részeként üzembe helyezett példányok száma adatokkal jelennek meg. Az alábbi képernyőfelvétel-készítés méretezési csoportok egy példát mutat be.

      ![Az Azure App Service a méretezési csoportok szerepel a Virtual Machine Scale Sets UX][1]

1. Minden készlet méretezése. Például ha rendelkezik meglévő három példányban a méretezési csoportban lévő ki kell terjeszteni a 6, a három új példányok üzembe helyezése tartalék tartomány között. A következő PowerShell-példa a méretezési horizontális jeleníti meg.

   ```powershell
   Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

   # Get current scale set
   $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

   # Set and update the capacity of your scale set
   $vmss.sku.capacity = 6
   Update-AzureRmVmss -ResourceGroupName AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
   ```

   >[!NOTE]
   >Ez a lépés több órát, attól függően, a szerepkör típusát és a példányok számát is eltarthat.

1. A **App Service felügyeleti szerepkörök**, figyelheti az új szerepkör-példányok állapotát. Egy szerepkörpéldány állapotának ellenőrzéséhez válassza ki a szerepkör típusa a listából

    ![Az Azure App Service-ben az Azure Stack-szerepkörökről][2]

1. Ha az új szerepkör példányai állapota **készen**, lépjen vissza **virtuálisgép-méretezési** és **törlése** a régi szerepkörpéldányokat.

1. Ismételje meg ezeket a lépéseket **egyes** virtuálisgép-méretezési csoportot.

## <a name="next-steps"></a>További lépések

Is kipróbálhatja más [platform platformszolgáltatási (PaaS) szolgáltatásokra](azure-stack-tools-paas-services.md).

* [Az SQL Server erőforrás-szolgáltató](azure-stack-sql-resource-provider-deploy.md)
* [MySQL típusú erőforrás-szolgáltató](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
