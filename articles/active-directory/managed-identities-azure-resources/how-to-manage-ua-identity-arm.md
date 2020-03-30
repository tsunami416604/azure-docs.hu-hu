---
title: Felhasználó által kijelölt felügyelt identitás létrehozása & törlése az Azure Resource Manager használatával
description: Lépésenkénti útmutató a felhasználó által hozzárendelt felügyelt identitások létrehozásához és törléséhez az Azure Resource Manager használatával.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/10/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 244965da4e22c0808fd1ea9088aa182b27eaf484
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79253376"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása, listázása és törlése az Azure Resource Manager használatával


Az Azure-erőforrások felügyelt identitásai felügyelt identitást biztosítaz Azure-szolgáltatások felügyelt identitással az Azure Active Directoryban. Ezzel az identitással hitelesítheti az Azure AD-hitelesítést támogató szolgáltatások, anélkül, hogy hitelesítő adatokat a kódban. 

Ebben a cikkben egy felhasználó által hozzárendelt felügyelt identitást hoz létre egy Azure Resource Manager használatával.

Nem lehet felsorolni és törölni egy felhasználó által hozzárendelt felügyelt identitást egy Azure Resource Manager-sablon használatával.  A felhasználó által hozzárendelt felügyelt identitás létrehozásáról és listázásáról az alábbi cikkekből választhat:

- [Felhasználó által hozzárendelt felügyelt identitás listázása](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Felhasználó által hozzárendelt felügyelt identitás törlése](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
  ## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md) **Mindenképpen tekintse át a [rendszerhez rendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget.](overview.md#how-does-the-managed-identities-for-azure-resources-work)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="template-creation-and-editing"></a>Sablon létrehozása és szerkesztése

Az Azure Portalhoz és a parancsfájlok futtatásához is az Azure Resource Manager-sablonok lehetővé teszik az Azure-erőforráscsoport által meghatározott új vagy módosított erőforrások üzembe helyezését. Számos lehetőség áll rendelkezésre a sablon szerkesztéséhez és telepítéséhez, helyi és portálalapú, többek között:

- Az [Azure Marketplace-ről származó egyéni sablon](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)használatával, amely lehetővé teszi, hogy teljesen új sablont hozzon létre, vagy egy meglévő közös vagy [QuickStart sablonra](https://azure.microsoft.com/documentation/templates/)alapozza.
- Egy meglévő erőforráscsoportból származó, sablon exportálása [az eredeti központi telepítésből](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)vagy a telepítés aktuális [állapotából.](../../azure-resource-manager/management/manage-resource-groups-portal.md#export-resource-groups-to-templates)
- Egy helyi [JSON-szerkesztő (például a VS-kód)](../../azure-resource-manager/resource-manager-create-first-template.md)használatával, majd feltöltése és üzembe helyezése a PowerShell vagy a CLI használatával.
- A Visual Studio [Azure Resource Group projekt](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) használatával hozzon létre és telepítsen egy sablont. 

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása 

Felhasználó által hozzárendelt felügyelt identitás létrehozásához a fióknak szüksége van a [felügyelt identitás közreműködőszerepkör-hozzárendelésre.](/azure/role-based-access-control/built-in-roles#managed-identity-contributor)

Felhasználó által hozzárendelt felügyelt identitás létrehozásához használja a következő sablont. Cserélje `<USER ASSIGNED IDENTITY NAME>` le az értéket a saját értékeire:

[!INCLUDE [ua-character-limit](~/includes/managed-identity-ua-character-limits.md)]

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "resourceName": {
          "type": "string",
          "metadata": {
            "description": "<USER ASSIGNED IDENTITY NAME>"
          }
        }
  },
  "resources": [
    {
      "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
      "name": "[parameters('resourceName')]",
      "apiVersion": "2018-11-30",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('resourceName')]"
      }
  }
}
```
## <a name="next-steps"></a>További lépések

Ha további információban része, hogyan rendelhet hozzá egy felhasználó által hozzárendelt felügyelt identitást egy Azure-beli virtuális géphez egy Azure Resource Manager-sablon használatával, olvassa [el az Azure-erőforrások felügyelt identitásának konfigurálása egy Azure-beli virtuális gépen sablonok használatával című témakört.](qs-configure-template-windows-vm.md)


 
