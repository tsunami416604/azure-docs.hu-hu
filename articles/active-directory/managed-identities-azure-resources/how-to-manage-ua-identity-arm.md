---
title: Hogyan hozhat létre, és a egy Azure Resource Manager használatával felügyelt felhasználó által hozzárendelt Identitások törlése
description: Lépésről lépésre való létrehozása és törlése a felhasználó által hozzárendelt identitások az Azure Resource Manager által felügyelt.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: priyamo
ms.openlocfilehash: 198bf1e971ee7594668bbd79a1f6fc92acaa6a03
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54885251"
---
# <a name="create-list-and-delete-a-user-assigned-managed-identity-using-azure-resource-manager"></a>Lista létrehozása, és a egy Azure Resource Manager használatával felügyelt felhasználó által hozzárendelt Identitások törlése

[!INCLUDE [preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyelt identitások az Azure-erőforrások egy felügyelt identitás, az Azure Active Directory Azure-szolgáltatásokat biztosít. Ez az identitás használatával, amelyek támogatják az Azure AD-hitelesítés, anélkül, hogy hitelesítő adatok a kód a szolgáltatásokhoz való hitelesítéséhez. 

Ez a cikk egy Azure Resource Manager használatával felügyelt felhasználó által hozzárendelt identitás létrehozása.

Nem alkalmas listázása és a egy Azure Resource Manager-sablon használatával felügyelt felhasználó által hozzárendelt Identitások törlése.  Tekintse meg a következő cikkeket hozhat létre, és egy felhasználó által hozzárendelt lista felügyelt identitás:

- [Felügyelt identitás felhasználó által hozzárendelt listából](how-to-manage-ua-identity-cli.md#list-user-assigned-managed-identities)
- [Felhasználó által hozzárendelt felügyelt identitás törlése](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-managed-identity)
## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség a rendszer által hozzárendelt, és a felhasználó által hozzárendelt felügyelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="template-creation-and-editing"></a>Sablon létrehozása és szerkesztése

Ahogy az az Azure portal és a parancsfájlok, az Azure Resource Manager-sablonok lehetővé teszi, hogy üzembe helyezése az Azure-erőforráscsoport által meghatározott új vagy megváltozott erőforrásokat. Több lehetőség is elérhető, és a helyi és portálalapú, beleértve a központi telepítési sablon Szerkesztés:

- Használatával egy [egyéni sablont az Azure Marketplace-ről](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), amely lehetővé teszi, hogy a sablon létrehozása az alapoktól, vagy egy meglévő közös alapul vagy [gyorsindítási sablon](https://azure.microsoft.com/documentation/templates/).
- Sablon exportálása vagy egy meglévő erőforráscsoportot, a Származtatás [az eredeti üzembe helyezés](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), vagy a [az üzemelő példány aktuális állapotát](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
- Egy helyi [JSON-szerkesztővel (például a VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), majd feltöltését és üzembe helyezése a PowerShell vagy parancssori felület használatával.
- A Visual Studio használatával [Azure erőforráscsoport-projekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) hozzon létre és helyezhet üzembe sablont is. 

## <a name="create-a-user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás létrehozása 

A felügyelt felhasználó által hozzárendelt identitás létrehozása, a fióknak rendelkeznie kell a [felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör-hozzárendelés.

Egy felhasználó által hozzárendelt felügyelt identitás létrehozásához használja az alábbi sablont. Cserélje le a `<USER ASSIGNED IDENTITY NAME>` értéket a saját értékeire:

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

Információk egy felhasználó által hozzárendelt hozzárendelése felügyelt identitás használata egy Azure Resource Manager sablon lásd: Azure virtuális gépekhez [konfigurálása felügyelt Azure-erőforrások egy Azure-beli Virtuálisgép-sablonok használata az identitások](qs-configure-template-windows-vm.md).


 
