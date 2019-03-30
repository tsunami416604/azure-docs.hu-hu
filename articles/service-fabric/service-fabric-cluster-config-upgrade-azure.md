---
title: Azure Service Fabric-fürt konfigurációjának frissítése |} A Microsoft Docs
description: Ismerje meg, hogyan frissítse a konfigurációt, amely a Service Fabric-fürtön fut az Azure-ban, Resource Manager-sablon használatával.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: chackdan
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 818136f24eb063e2bd7217d5441bda19bf141317
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666596"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Az Azure-ban a fürt konfigurációjának frissítése 

Ez a cikk ismerteti, hogyan szabhatja testre a különböző hálóbeállítások a Service Fabric-fürt számára. A fürtök az Azure-ban üzemeltetett, testre szabhatja a beállításokat a [az Azure portal](https://portal.azure.com) vagy Azure Resource Manager-sablon használatával.

> [!NOTE]
> Nem minden beállítás érhető el a portálon, és van egy [ajánlott eljárás az, hogy egy Azure Resource Manager-sablon használatával testre szabható](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code); Service Fabric Dev\Test forgatókönyvei csak olyan portálon.
> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Testre szabhatja a Resource Manager-sablonok használatával fürtbeállítások
Az Azure-fürtök a JSON-Resource Manager-sablon konfigurálható. A különböző beállításokkal kapcsolatos további információkért lásd: [fürtök konfigurációs beállításai](service-fabric-cluster-fabric-settings.md). Tegyük fel, az alábbi lépéseket egy új beállítás hozzáadása megjelenítése *MaxDiskQuotaInMB* , a *diagnosztikai* szakaszban az Azure Resource Explorer használatával.

1. Nyissa meg a következőt: https://resources.azure.com
2. Keresse meg az előfizetés által bővítése **előfizetések** -> **\<előfizetését >** -> **resourceGroups**  ->   **\<Az erőforráscsoport >** -> **szolgáltatók** -> **Microsoft.ServiceFabric**  ->  **fürtök** -> **\<saját fürt neve >**
3. A jobb felső sarokban, válassza ki **olvasási/írási.**
4. Válassza ki **szerkesztése** és frissítheti a `fabricSettings` JSON-elem és a egy új elem hozzáadása:

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

A következő módokon az Azure Resource Manager fürt beállítások is testre:

- Használja a [az Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) exportálhatja, és a Resource Manager-sablon frissítéséhez.
- Használat [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) exportálhatja, és a Resource Manager-sablon frissítéséhez.
- Használja a [Azure CLI-vel](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) exportálhatja, és a Resource Manager-sablon frissítéséhez.
- Az Azure RM PowerShell [Set-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Set-AzureRmServiceFabricSetting) és [Remove-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Remove-AzureRmServiceFabricSetting) parancsok a beállítás módosításához közvetlenül.
- Az Azure CLI-vel [az sf cluster beállítás](https://docs.microsoft.com/cli/azure/sf/cluster/setting) parancsok a beállítás módosításához közvetlenül.

## <a name="next-steps"></a>További lépések
* További információ a [Service Fabric-fürt beállítások](service-fabric-cluster-fabric-settings.md).
* Ismerje meg, hogyan [fürt kétirányú méretezése](service-fabric-cluster-scale-up-down.md).
