---
title: 'App Service Azure veremben: Fault tartomány frissítés |} Microsoft Docs'
description: How Azure App Service Azure veremben elvégzi a tartalék tartományok között
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
ms.date: 03/09/2018
ms.author: anwestg
ms.openlocfilehash: 42adef66fb1b1141ab44aab3a1ccdaae022202b5
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>How Azure App Service Azure veremben elvégzi a tartalék tartományok között

*A következőkre vonatkozik: Azure verem integrált rendszerek*

A 1802 frissítéstől kezdve Azure verem most támogatja az munkaterhelések tartalék tartományokban egy szolgáltatásnál, ami létfontosságú a magas rendelkezésre állás érdekében.

> [!IMPORTANT]
> Még kell frissített az Azure-verem integrált rendszert 1802 kell kihasználni a tartalék tartomány támogatása.  Ez a dokumentum csak az App Service erőforrás szolgáltató központi telepítések a 1802 frissítés előtt elvégzett vonatkozik.  Ha telepítette a veremben Azure App Service, Azure verem alkalmazott 1802 frissítés után, az erőforrás-szolgáltató már terjesztve tartalék tartományok között.
>
>

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Az App Service erőforrás-szolgáltató egyensúlyba tartalék tartományok között

Ahhoz, hogy elvégzi a méretezési készlet telepítve az App Service erőforrás-szolgáltató, akkor kell a következő lépésekkel minden méretezési készlet.  Alapértelmezés szerint a scaleset nevek a következők:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

> [!NOTE]
> Ha még nem-példányt a munkavégző réteg méretezési készlet része, nem kell egyensúlyba e méretezési készlet.  A méretezési készlet kiegyensúlyozott megfelelően kell akkor ki azokat a jövőben.
>
>

1. Tallózással keresse meg a virtuálisgép-méretezési csoportok a verem rendszergazda Azure portálon.  Az App Service központi telepítésének részeként telepített meglévő méretezési csoportok példányok száma információkkal jelennek meg.

    ![Az Azure App Service méretezési készlet szerepel a virtuális gép méretezési készletek UX][1]

2. Minden kibővítési tovább.  Például ha három meglévő a méretezési csoportban lévő kell a horizontális 6, hogy a három új példányok kiépítendő tartalék tartományok között.
    a. [A PowerShell Azure verem felügyeleti környezet beállítása](azure-stack-powershell-configure-admin.md) b. Ez a példa segítségével a méretezési kiterjesztése:
        ```powershell
                Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

                # Get current scale set
                $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

                # Set and update the capacity of your scale set
                $vmss.sku.capacity = 6
                Update-AzureRmVmss -ResourceGroupName "AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
        '''
> [!NOTE]
> Ez a lépés attól függően, hogy milyen típusú szerepkör és a példányok száma órát számos is igénybe vehet.
>
>

3. Az App Service felügyeleti szerepkörök panelen új szerepkör-példányok állapotának figyelése.  A felhasználóiszerepkör-típus a listában kattintson egy egyedi szerepkörpéldányt állapotának ellenőrzése

    ![Azure verem szerepkörök az Azure App Service][2]

4. Egy új példányt a rendszer egy **készen** állapot, lépjen vissza a virtuálisgép-méretezési csoport panel és **törlése** a régi példányok.

5. Ismételje meg ezeket a lépéseket **minden** virtuálisgép-méretezési készlet.

## <a name="next-steps"></a>További lépések

Is kipróbálhatja más [platformok (PaaS) szolgáltatás](azure-stack-tools-paas-services.md).

* [SQL Server erőforrás-szolgáltató](azure-stack-sql-resource-provider-deploy.md)
* [MySQL-erőforrás-szolgáltató](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
