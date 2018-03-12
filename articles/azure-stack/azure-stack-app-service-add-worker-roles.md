---
title: "Az App Services - verem Azure feldolgozói szerepkörök kibővítési |} Microsoft Docs"
description: "Részletes útmutatás méretezését Azure verem App Service szolgáltatások"
services: azure-stack
documentationcenter: 
author: apwestgarth
manager: stefsch
editor: 
ms.assetid: 3cbe87bd-8ae2-47dc-a367-51e67ed4b3c0
ms.service: azure-stack
ms.workload: app-service
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/08/2018
ms.author: anwestg
ms.reviewer: brenduns
ms.openlocfilehash: d6471796863a80e69fdaf740b68fb27d59503453
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
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
    Login-AzureRMAccount -EnvironmentName AzureStackAdmin
                                                 
    ## Name of the Resource group where AppService is deployed.
    $AppServiceResourceGroupName = "AppService.local"

    ## Name of the ScaleSet : e.g. FrontEndsScaleSet, ManagementServersScaleSet, PublishersScaleSet , LargeWorkerTierScaleSet,      MediumWorkerTierScaleSet, SmallWorkerTierScaleSet, SharedWorkerTierScaleSet
    $ScaleSetName = "SharedWorkerTierScaleSet"

    ## TotalCapacity is sum of the instances needed at the end of operation. 
    ## e.g. if you VMSS has 1 instance(s) currently and you need 1 more the TotalCapacity should be set to 2
    $TotalCapacity = 2  

    # Get current scale set
    $vmss = Get-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -VMScaleSetName $ScaleSetName

    # Set and update the capacity
    $vmss.sku.capacity = $TotalCapacity
    Update-AzureRmVmss -ResourceGroupName $AppServiceResourceGroupName -Name $ScaleSetName -VirtualMachineScaleSet $vmss 
  
    '''

> [!NOTE]
> This step can take a number of hours to complete depending on the type of role and the number of instances.
>
>

3. Monitor the status of the new role instances in the App Service Administration, to check the status of an individual role instance click the role type in the list.

## Add additional workers directly within the App Service Resource Provider Admin.

1. Log in to the Azure Stack administration portal as the service administrator.

2. Browse to **App Services**.

    ![](media/azure-stack-app-service-add-worker-roles/image01.png)

3. Click **Roles**. Here you see the breakdown of all App Service roles deployed.

4. Right click on the row of the type you want to scale and then click **ScaleSet**.

    ![](media/azure-stack-app-service-add-worker-roles/image02.png)

5. Click **Scaling**, select the number of instances you want to scale to, and then click **Save**.

    ![](media/azure-stack-app-service-add-worker-roles/image03.png)

6. App Service on Azure Stack will now add the additional VMs, configure them, install all the required software, and mark them as ready when this process is complete. This process can take approximately 80 minutes.

7. You can monitor the progress of the readiness of the new roles by viewing the workers in the **Roles** blade.

## Result

After they are fully deployed and ready, the workers become available for users to deploy their workload onto them. The following shows an example of the multiple pricing tiers available by default. If there are no available workers for a particular worker tier, the option to choose the corresponding pricing tier is unavailable.

![](media/azure-stack-app-service-add-worker-roles/image04.png)

>[!NOTE]
> To scale out Management, Front End or Publisher roles add you must scale out the corresponding role type. 
>
>

To scale out Management, Front End, or Publisher roles, follow the same steps selecting the appropriate role type. Controllers are not deployed as Scale Sets and therefore two should be deployed at Installation time for all production deployments.

### Next steps

[Configure deployment sources](azure-stack-app-service-configure-deployment-sources.md)
