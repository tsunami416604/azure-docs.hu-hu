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
ms.date: 06/29/2018
ms.author: anwestg
ms.openlocfilehash: ce57e153dcab6a386150ebefe1ecb4a018514247
ms.sourcegitcommit: 5892c4e1fe65282929230abadf617c0be8953fd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37130370"
---
# <a name="how-to-redistribute-azure-app-service-on-azure-stack-across-fault-domains"></a>How Azure App Service Azure veremben elvégzi a tartalék tartományok között

*A következőkre vonatkozik: Azure verem integrált rendszerek*

A 1802 frissítéstől kezdve Azure verem mostantól támogatja az munkaterhelések tartalék tartományokban egy szolgáltatás, amely a magas rendelkezésre állás fontos.

>[!IMPORTANT]
>Tartalék tartomány támogatása előnyeit, frissítenie kell az integrált Azure verem rendszer 1802. Ez a dokumentum csak érvényes App Service erőforrás szolgáltató központi telepítések a 1802 frissítése előtt volt befejeződött. Ha telepítette az Azure veremben App Service 1802 rendszer frissítés Azure verem után, az erőforrás-szolgáltató már terjesztve tartalék tartományok között.

## <a name="rebalance-an-app-service-resource-provider-across-fault-domains"></a>Az App Service erőforrás-szolgáltató egyensúlyba tartalék tartományok között

A méretezési készlet telepítve az App Service erőforrás-szolgáltató újraterjesztése, lépésekkel az ebben a cikkben minden méretezési készlet. Alapértelmezés szerint a scaleset nevek a következők:

* ManagementServersScaleSet
* FrontEndsScaleSet
* PublishersScaleSet
* SharedWorkerTierScaleSet
* SmallWorkerTierScaleSet
* MediumWorkerTierScaleSet
* LargeWorkerTierScaleSet

>[!NOTE]
> Ha példányt az egyes a munkavégző réteg méretezési készlet nem rendelkezik, nem kell azokat méretezési csoportok egyensúlyba. A méretezési készlet kiegyensúlyozott megfelelően kell akkor ki azokat a jövőben.

A méretezési készlet horizontális, kövesse az alábbi lépéseket:

1. Jelentkezzen be a Azure verem felügyeleti portálra.
2. Válassza ki **további szolgáltatások**.
3. A SZÁMÍTÁST, és válassza a **virtuálisgép-méretezési csoportok**. Az App Service központi telepítésének részeként telepített meglévő méretezési csoportok példányok száma információkkal jelennek meg. Az alábbi képernyőfelvételen látható méretezési készlet.

      ![Az Azure App Service méretezési készlet szerepel a virtuális gép méretezési készletek UX][1]

4. Minden kiterjesztése. Például ha három meglévő a méretezési csoportban lévő kell a horizontális 6, a három új példányok tartalék tartományokban vannak telepítve. A következő PowerShell-példa a méretezési horizontális jeleníti meg.

   ```powershell
   Add-AzureRmAccount -EnvironmentName AzureStackAdmin 

   # Get current scale set
   $vmss = Get-AzureRmVmss -ResourceGroupName "AppService.local" -VMScaleSetName "SmallWorkerTierScaleSet"

   # Set and update the capacity of your scale set
   $vmss.sku.capacity = 6
   Update-AzureRmVmss -ResourceGroupName AppService.local" -Name "SmallWorkerTierScaleSet" -VirtualMachineScaleSet $vmss
   ```

   >[!NOTE]
   >Ez a lépés néhány órát, attól függően, hogy milyen típusú szerepkör és a példányok száma is tarthat.

5. A **App Service felügyeleti szerepkörök**, új szerepkör-példányok állapotának figyelése. A szerepkör példánya állapotának ellenőrzéséhez listáján válassza ki a felhasználóiszerepkör-típus

    ![Azure verem szerepkörök az Azure App Service][2]

6. Amikor új szerepkör-példányok állapota **készen**, lépjen vissza a **virtuálisgép-méretezési csoport** és **törlése** a régi szerepkörpéldányokat.

7. Ismételje meg ezeket a lépéseket **minden** virtuálisgép-méretezési készlet.

## <a name="next-steps"></a>További lépések

Is kipróbálhatja más [platformok (PaaS) szolgáltatás](azure-stack-tools-paas-services.md).

* [SQL Server erőforrás-szolgáltató](azure-stack-sql-resource-provider-deploy.md)
* [MySQL-erőforrás-szolgáltató](azure-stack-mysql-resource-provider-deploy.md)

<!--Image references-->
[1]: ./media/azure-stack-app-service-fault-domain-update/app-service-scale-sets.png
[2]: ./media/azure-stack-app-service-fault-domain-update/app-service-roles.png
