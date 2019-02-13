---
title: Felügyelt identitások az Azure-erőforrások konfigurálása az Azure virtuális gépen egy sablon használatával
description: Részletes útmutató az Azure-erőforrások felügyelt identitások konfigurálása-beli virtuális gépen, egy Azure Resource Manager-sablon használatával.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6fdabc16e191fb265b15af4a6d4a57cb749adc9f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56202553"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-a-templates"></a>Felügyelt identitások az Azure-erőforrások konfigurálása egy Azure-beli Virtuálisgép-sablonok használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ez a cikk az Azure Resource Manager üzembe helyezési sablon használatával megismerheti, hogyan végrehajtani a következő felügyelt identitások az Azure-erőforrások operations-beli virtuális gépen:

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri az Azure Resource Manager üzembe helyezési sablon használatával, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség a rendszer által hozzárendelt, és a felhasználó által hozzárendelt felügyelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Csakúgy, mint az Azure Portalon, és parancsfájl-kezelési, [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) sablonok lehetővé teszi, hogy üzembe helyezése az Azure-erőforráscsoport által meghatározott új vagy megváltozott erőforrásokat. Több lehetőség is elérhető, és a helyi és portálalapú, beleértve a központi telepítési sablon Szerkesztés:

   - Használatával egy [egyéni sablont az Azure Marketplace-ről](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), amely lehetővé teszi, hogy a sablon létrehozása az alapoktól, vagy egy meglévő közös alapul vagy [gyorsindítási sablon](https://azure.microsoft.com/documentation/templates/).
   - Sablon exportálása vagy egy meglévő erőforráscsoportot, a Származtatás [az eredeti üzembe helyezés](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), vagy a [az üzemelő példány aktuális állapotát](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Egy helyi [JSON-szerkesztővel (például a VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), majd feltöltését és üzembe helyezése a PowerShell vagy parancssori felület használatával.
   - A Visual Studio használatával [Azure erőforráscsoport-projekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) hozzon létre és helyezhet üzembe sablont is.  

A lehetőséget választja, függetlenül a sablon szintaxisa megegyezik kezdeti üzembe helyezése és újbóli üzembe helyezés során. A rendszer vagy az új vagy meglévő virtuális gép felügyelt identitás felhasználó által hozzárendelt azonos módon történik. Emellett, alapértelmezés szerint az Azure Resource Manager elvégzi az [növekményes frissítés](../../azure-resource-manager/deployment-modes.md) üzemelő példányokhoz.

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban engedélyezze, és egy Azure Resource Manager-sablon használatával felügyelt rendszer által hozzárendelt identitások letiltása.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Rendszer által hozzárendelt felügyelt identitás engedélyezése egy Azure virtuális Gépen vagy egy meglévő virtuális Gépet a létrehozása során

Ahhoz, hogy a rendszer által hozzárendelt felügyelt identitás, a virtuális gép, a fióknak rendelkeznie kell a [virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés.  Nincsenek további Azure AD-címtár szerepkör-hozzárendelések szükségesek.

1. Jelentkezzen be az Azure-bA helyileg vagy az Azure Portalon az Azure-előfizetéshez társított olyan fiókot használjon, amely tartalmazza a virtuális Gépet.

2. Ahhoz, hogy a rendszer által hozzárendelt felügyelt identitás, a sablon betöltése a szerkesztő, keresse meg a `Microsoft.Compute/virtualMachines` házirendsablonokkal erőforrás a `resources` szakaszt, és adja hozzá a `"identity"` tulajdonság azonos szinten, a `"type": "Microsoft.Compute/virtualMachines"` tulajdonság. Az alábbi szintaxissal:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   },
   ```

3. (Nem kötelező) A virtuális gép felügyelt identitások, Azure-erőforrás-bővítmény hozzáadása egy `resources` elemet. Ez a lépés nem kötelező használni, mivel az Azure példány metaadat szolgáltatás (IMDS) identitás-végpont használatával, valamint a jogkivonatok.  Az alábbi szintaxissal:

   >[!NOTE] 
   > Az alábbi példa feltételezi, hogy Windows VM-bővítmény (`ManagedIdentityExtensionForWindows`) lesz üzembe helyezve. Beállíthatja a Linux használatával `ManagedIdentityExtensionForLinux` ehelyett a `"name"` és `"type"` elemeket. A Virtuálisgép-bővítmény elavult a január 2019 tervezünk.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2018-06-01",
       "location": "[resourceGroup().location]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.ManagedIdentity",
           "type": "ManagedIdentityExtensionForWindows",
           "typeHandlerVersion": "1.0",
           "autoUpgradeMinorVersion": true,
           "settings": {
               "port": 50342
           },
           "protectedSettings": {}
       }
   }
   ```

4. Ha elkészült, a következő szakaszok kell hozzáadni a `resource` a sablont, és azt a következőképpen kell kinéznie:

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
                },
            },
            {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
                }
            }
        }
    ]
   ```

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>A virtuális gép felügyelt identitás alapértelmezett szerepkör hozzárendelése

Miután engedélyezte a rendszer által hozzárendelt felügyelt identitás a virtuális Gépen, érdemes lehet megadni, például egy szerepkör **olvasó** hozzáférés az erőforráscsoporthoz, amelyben létrehozták.

Szerepkör hozzárendelése a virtuális gép alapértelmezett identitásra, a fióknak rendelkeznie kell a [felhasználói hozzáférés rendszergazdája](/azure/role-based-access-control/built-in-roles#user-access-administrator) szerepkör-hozzárendelés.

1. Jelentkezzen be az Azure-bA helyileg vagy az Azure Portalon az Azure-előfizetéshez társított olyan fiókot használjon, amely tartalmazza a virtuális Gépet.
 
2. Betölteni a sablont, egy [szerkesztő](#azure-resource-manager-templates) , és adja hozzá a következő információkat biztosíthat a virtuális gép **olvasó** hozzáférés az erőforráscsoporthoz, amelyben létrehozták.  A sablon struktúra a szerkesztő és a választott telepítési modell függvénye.
   
   Alatt a `parameters` szakaszban adja hozzá a következő:

    ```JSON
    "builtInRoleType": {
          "type": "string",
          "defaultValue": "Reader"
        },
        "rbacGuid": {
          "type": "string"
        }
    ```

    Alatt a `variables` szakaszban adja hozzá a következő:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    Alatt a `resources` szakaszban adja hozzá a következő:

    ```JSON
    {
        "apiVersion": "2017-09-01",
         "type": "Microsoft.Authorization/roleAssignments",
         "name": "[parameters('rbacGuid')]",
         "properties": {
                "roleDefinitionId": "[variables(parameters('builtInRoleType'))]",
                "principalId": "[reference(variables('vmResourceId'), '2017-12-01', 'Full').identity.principalId]",
                "scope": "[resourceGroup().id]"
          },
          "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', parameters('vmName'))]"
            ]
    }
    ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Tiltsa le a rendszer által hozzárendelt felügyelt identitás Azure virtuális gépből

Felügyelt identitás alapértelmezett eltávolítása egy virtuális Gépet, a fióknak rendelkeznie kell a [virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés.  Nincsenek további Azure AD-címtár szerepkör-hozzárendelések szükségesek.

1. Jelentkezzen be az Azure-bA helyileg vagy az Azure Portalon az Azure-előfizetéshez társított olyan fiókot használjon, amely tartalmazza a virtuális Gépet.

2. Betölteni a sablont, egy [szerkesztő](#azure-resource-manager-templates) , és keresse meg a `Microsoft.Compute/virtualMachines` házirendsablonokkal erőforrás a `resources` szakaszban. Ha egy virtuális Gépet, amely csak a felügyelt identitás alapértelmezett rendelkezik, letilthatja az identitás típusúra `None`.  
   
   **API-verzió a 2018-06-01 Microsoft.Compute/virtualMachines**

   Ha a virtuális gépen, rendszer és a felügyelt identitásokból felhasználó által hozzárendelt, távolítsa el a `SystemAssigned` identitástípus és tarthatja `UserAssigned` együtt a `userAssignedIdentities` szótár értékeket.

   **API-verzió a 2018-06-01 Microsoft.Compute/virtualMachines**
   
   Ha a `apiVersion` van `2017-12-01` és a virtuális gép rendelkezik a rendszer mind a felügyelt identitásokból felhasználó által hozzárendelt, távolítsa el `SystemAssigned` identitástípus és tarthatja `UserAssigned` együtt a `identityIds` tömbje, a felhasználó által hozzárendelt identitások felügyelt.  
   
Az alábbi példa bemutatja, hogyan távolít el egy rendszer által hozzárendelt felügyelt identitás egy virtuális Gépet a nem felügyelt felhasználó által hozzárendelt identitások:

```JSON
{
    "apiVersion": "2018-06-01",
    "type": "Microsoft.Compute/virtualMachines",
    "name": "[parameters('vmName')]",
    "location": "[resourceGroup().location]",
    "identity": { 
        "type": "None"
}
```

## <a name="user-assigned-managed-identity"></a>felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban egy felhasználó által hozzárendelt felügyelt identitás lehet hozzárendelni egy Azure virtuális gép Azure Resource Manager-sablon használatával.

> [!Note]
> Egy Azure Resource Manager-sablon használatával felügyelt felhasználó által hozzárendelt identitások létrehozásával kapcsolatban lásd: [felügyelt felhasználó által hozzárendelt identitás létrehozása](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

 ### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy Azure virtuális Gépen

A felhasználó által hozzárendelt identitás hozzárendelése egy virtuális Gépet, a fióknak rendelkeznie kell a [virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és [felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendeléseket. Nincsenek további Azure AD-címtár szerepkör-hozzárendelések szükségesek.

1. Alatt a `resources` elemben adja hozzá a következő bejegyzést a felhasználó által hozzárendelt felügyelt identitás hozzárendelése a virtuális gép.  Ne felejtse el `<USERASSIGNEDIDENTITY>` a felhasználó által hozzárendelt nevű felügyelt identitás hozott létre.

   **API-verzió a 2018-06-01 Microsoft.Compute/virtualMachines**

   Ha a `apiVersion` van `2018-06-01`, a felügyelt felhasználó által hozzárendelt identitások vannak tárolva a `userAssignedIdentities` szótár formátum és a `<USERASSIGNEDIDENTITYNAME>` egy változóban meghatározott értéket kell tárolni a `variables` szakasz a sablon.

   ```json
   {
       "apiVersion": "2018-06-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
        }
   }
   ```
   
   **Microsoft.Compute/virtualMachines API 2017-12-01-es verzió**
    
   Ha a `apiVersion` van `2017-12-01`, a felügyelt felhasználó által hozzárendelt identitások vannak tárolva a `identityIds` tömb és a `<USERASSIGNEDIDENTITYNAME>` egy változóban meghatározott értéket kell tárolni a `variables` a sablon szakaszában.
    
   ```json
   {
       "apiVersion": "2017-12-01",
       "type": "Microsoft.Compute/virtualMachines",
       "name": "[variables('vmName')]",
       "location": "[resourceGroup().location]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
           ]
       }
   }
   ```
       

2. (Nem kötelező) A következő a `resources` elemben adja hozzá a következő bejegyzést a felügyelt identitás bővítmény hozzárendelése a virtuális gép (tervezett elavult a január 2019 esetében). Ez a lépés nem kötelező használni, mivel az Azure példány metaadat szolgáltatás (IMDS) identitás-végpont használatával, valamint a jogkivonatok. Az alábbi szintaxissal:
    ```json
    {
        "type": "Microsoft.Compute/virtualMachines/extensions",
        "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
        "apiVersion": "2018-06-01",
        "location": "[resourceGroup().location]",
        "dependsOn": [
            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
        ],
        "properties": {
            "publisher": "Microsoft.ManagedIdentity",
            "type": "ManagedIdentityExtensionForWindows",
            "typeHandlerVersion": "1.0",
            "autoUpgradeMinorVersion": true,
            "settings": {
                "port": 50342
            }
        }
    }
    ```
    
3. Ha elkészült, a következő szakaszok kell hozzáadni a `resource` a sablont, és azt a következőképpen kell kinéznie:
   
   **API-verzió a 2018-06-01 Microsoft.Compute/virtualMachines**    

   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "userAssignedIdentities": {
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2018-06-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
            }
        }
       }
    ]
   ```
   **Microsoft.Compute/virtualMachines API 2017-12-01-es verzió**
   
   ```JSON
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "userAssigned",
                "identityIds": [
                   "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            }
        },
        {
            "type": "Microsoft.Compute/virtualMachines/extensions",
            "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[resourceGroup().location]",
            "dependsOn": [
                "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
            ],
            "properties": {
                "publisher": "Microsoft.ManagedIdentity",
                "type": "ManagedIdentityExtensionForWindows",
                "typeHandlerVersion": "1.0",
                "autoUpgradeMinorVersion": true,
                "settings": {
                    "port": 50342
            }
        }
       }
    ]
   ```

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Távolítsa el a felhasználó által hozzárendelt felügyelt identitás Azure virtuális gépből

Egy felhasználó által hozzárendelt identitással eltávolítása egy virtuális Gépet, a fióknak rendelkeznie kell a [virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés. Nincsenek további Azure AD-címtár szerepkör-hozzárendelések szükségesek.

1. Jelentkezzen be az Azure-bA helyileg vagy az Azure Portalon az Azure-előfizetéshez társított olyan fiókot használjon, amely tartalmazza a virtuális Gépet.

2. Betölteni a sablont, egy [szerkesztő](#azure-resource-manager-templates) , és keresse meg a `Microsoft.Compute/virtualMachines` házirendsablonokkal erőforrás a `resources` szakaszban. Ha egy virtuális Gépet, amely csak a felhasználó által hozzárendelt felügyelt identitás rendelkezik, letilthatja az identitás típusúra `None`.
 
   Az alábbi példa bemutatja, hogyan eltávolítja az összes felhasználó által hozzárendelt felügyelt identitások egy virtuális Gépet a nem felügyelt rendszer által hozzárendelt identitások:
   
   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```
   
   **API-verzió a 2018-06-01 Microsoft.Compute/virtualMachines**
    
   Egy virtuális Gépet egy egyetlen felhasználó által hozzárendelt felügyelt identitás eltávolításához távolítsa el a `useraAssignedIdentities` szótárban.

   Ha egy rendszer által hozzárendelt felügyelt identitás, tárolja a a a `type` értékét a `identity` értéket.
 
   **Microsoft.Compute/virtualMachines API 2017-12-01-es verzió**

   Egy virtuális Gépet egy egyetlen felhasználó által hozzárendelt felügyelt identitás eltávolításához távolítsa el a `identityIds` tömb.

   Ha egy rendszer által hozzárendelt felügyelt identitás, tárolja a a a `type` értékét a `identity` értéket.
   
## <a name="next-steps"></a>További lépések

- [Felügyelt identitások Azure-erőforrások áttekintő](overview.md).

