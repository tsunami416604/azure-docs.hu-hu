---
title: Azure Service Fabric-fürt konfigurációjának frissítése
description: Megtudhatja, hogyan frissítheti a service fabric-fürtöt az Azure-ban egy Resource Manager-sablon használatával futó konfigurációt.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 476a2d910b916ea29132b108478d06f756454813
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75463289"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Fürt konfigurációjának frissítése az Azure-ban 

Ez a cikk ismerteti, hogyan szabhatja testre a különböző hálóbeállításait a Service Fabric-fürthöz. Az Azure-ban üzemeltetett fürtök esetében testreszabhatja a beállításokat az [Azure Portalon](https://portal.azure.com) keresztül vagy egy Azure Resource Manager-sablon használatával.

> [!NOTE]
> Nem minden beállítás érhető el a portálon, és [ajánlott testre szabni egy Azure Resource Manager-sablon használatával;](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code) A portál csak a Service Fabric dev\Test forgatókönyvhez készült.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Fürtbeállítások testreszabása az Erőforrás-kezelő sablonjaival
Az Azure-fürtök a JSON Resource Manager sablonon keresztül konfigurálhatók. A különböző beállításokról a [Fürtök konfigurációs beállításai](service-fabric-cluster-fabric-settings.md)című témakörben olvashat bővebben. Például az alábbi lépések bemutatják, hogyan adhat hozzá egy új *beállítást MaxDiskQuotaInMB* a *Diagnosztika* szakasz az Azure Resource Explorer használatával.

1. Nyissa meg a következőt: https://resources.azure.com
2. Keresse meg az előfizetést az **előfizetések** -> **\<bővítésével Az előfizetés>**  ->  **a resourceGroups** -> **\<Az erőforráscsoport>**  ->  **a szolgáltatók** -> **Microsoft.ServiceFabric** -> **fürtök** -> **\<A fürt neve>**
3. A jobb felső sarokban válassza az **Olvasás/írás lehetőséget.**
4. Válassza a JSON-elem **szerkesztése** és frissítése lehetőséget, `fabricSettings` és adjon hozzá egy új elemet:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Az Azure Resource Manager az alábbi módokon is testreszabhatja a fürtbeállításokat:

- Az [Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) használatával exportálhatja és frissítheti a Erőforrás-manger sablont.
- A [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) használatával exportálhatja és frissítheti az Erőforrás-kezelő sablont.
- Az [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) használatával exportálhatja és frissítheti az Erőforrás-kezelő sablont.
- Az Azure PowerShell [Set-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) és [az Remove-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) parancsokkal közvetlenül módosíthatja a beállítást.
- Az Azure CLI [az sf fürtbeállítási](https://docs.microsoft.com/cli/azure/sf/cluster/setting) parancsokkal közvetlenül módosíthatja a beállítást.

## <a name="next-steps"></a>További lépések
* További információ a [Service Fabric fürtbeállításairól.](service-fabric-cluster-fabric-settings.md)
* További információ [a fürt be- és kiméretezése.](service-fabric-cluster-scale-up-down.md)
