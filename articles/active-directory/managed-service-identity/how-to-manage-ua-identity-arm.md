---
title: Hogyan hozhat létre, és a felhasználó törlése hozzárendelt felügyelt identitás Azure Resource Manager használatával
description: Lépésről lépésre történő létrehozását és törlését a felhasználó hozzárendelt felügyelt szolgáltatás identitás használatával az Azure erőforrás.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/16/2018
ms.author: daveba
ms.openlocfilehash: de768bdfa75e80adbfde8a677ffe1d5f24d56472
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="create-list-and-delete-a-user-assigned-identity-using-azure-resource-manager"></a>Lista létrehozása, és egy Azure Resource Manager használatával a felhasználói identitás törlése

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyelt Szolgáltatásidentitás az Azure Active Directory-identitással felügyelt Azure-szolgáltatásokhoz biztosít. Ezzel az identitással használhatja az Azure AD-alapú hitelesítés, anélkül, hogy hitelesítő adatok a kódban támogató szolgáltatások felé történő hitelesítésre. 

Ebben a cikkben az Azure Resource Manager használatával felügyelt identitás hozzárendelt felhasználó hoz létre.

Nincs lehetőség a listában, és az Azure Resource Manager-sablonnal identitás hozzárendelt felhasználó törlése.  Tekintse meg a következő cikkek létrehozásához, és egy felhasználó lehet hozzárendelve identitás listában:

- [A felhasználói listában identitás](how-to-manage-ua-identity-cli.md#list-user-assigned-identities)
- [A felhasználói identitás törlése](how-to-manage-ua-identity-cli.md#delete-a-user-assigned-identity)
## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri a felügyelt Szolgáltatásidentitás, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy hozzárendelt rendszer és a felhasználói identitás](overview.md#how-does-it-work)**.
- Ha még nem telepítette az Azure-fiók [regisztráljon egy ingyenes fiókot](https://azure.microsoft.com/free/) folytatása előtt.

Jelentkezzen be helyileg Azure vagy az Azure-portál, az Azure-előfizetéssel társított fiókot használ, amely a virtuális Gépet tartalmaz. Emellett győződjön meg arról, hogy a fiókja tagja-e egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális gép (például "Virtuális gép közreműködő" szerepkör).

## <a name="template-creation-and-editing"></a>Sablon létrehozása és szerkesztése

Az Azure portál és parancsfájl-kezelési, Azure Resource Manager-sablonok telepíthet egy Azure erőforráscsoport által meghatározott új vagy megváltozott erőforrásokat adjon meg. Több lehetőség is elérhető Sablonszerkesztés és a központi telepítés, helyi és portálalapú, beleértve:

- Használatával egy [egyéni sablont az Azure piactérről](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), amely lehetővé teszi teljesen új sablon létrehozása, vagy létrehozhatja azt egy meglévő közös vagy [gyorsindítási sablonon](https://azure.microsoft.com/documentation/templates/).
- Egy sablon exportálása vagy egy meglévő erőforráscsoportot, a Származtatás [az eredeti telepítési](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), vagy a [az üzemelő példány aktuális állapotának](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
- Használja a helyi [JSON szerkesztővel (például a Visual STUDIO Code)](../../azure-resource-manager/resource-manager-create-first-template.md), majd feltöltése és a PowerShell vagy a parancssori felület segítségével történő telepítéséhez.
- A Visual Studio használatával [Azure erőforráscsoport-projekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) létrehozása és a sablon telepítéséhez. 

## <a name="create-a-user-assigned-identity"></a>Hozzon létre egy felhasználó lehet hozzárendelve identitás 

Egy felhasználó lehet hozzárendelve identitás létrehozásához használja az alábbi sablont. Cserélje le a `<USER ASSIGNED IDENTITY NAME>` érték saját értékekkel:

> [!IMPORTANT]
> A felhasználói identitások létrehozása csak alfanumerikus és kötőjel (0 – 9 vagy a-z vagy A-Z vagy -) karaktereket. Emellett nevét kell korlátozni a virtuális gép/VMSS helyes működéséhez hozzárendelés 24 karakter hosszúságot. Biztonsági frissítések ellenőrzése. További információkért lásd: [– gyakori kérdések és ismert problémák](known-issues.md)

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
      "name": "[parameters('<USER ASSIGNED IDENTITY NAME>')]",
      "apiVersion": "2015-08-31-PREVIEW",
      "location": "[resourceGroup().location]"
    }
  ],
  "outputs": {
      "identityName": {
          "type": "string",
          "value": "[parameters('<USER ASSIGNED IDENTITY NAME>')]"
      }
  }
}
```
## <a name="related-content"></a>Kapcsolódó tartalom

Információk egy felhasználó lehet hozzárendelve identitás hozzárendelése egy Azure virtuális gép az Azure Resource Manager sablon lásd [egy sablon használatával konfigurálja a VM felügyelt Szolgáltatásidentitás](qs-configure-template-windows-vm.md).


 
