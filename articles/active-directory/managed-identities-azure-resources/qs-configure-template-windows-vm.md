---
title: Felügyelt identitások konfigurálása az Azure Virtuális gépen sablon használatával – Azure AD
description: Részletes útmutató az Azure-erőforrások felügyelt identitások azure-erőforrásokhoz való konfigurálásához egy Azure-beli virtuális gépen egy Azure Resource Manager-sablon használatával.
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
ms.date: 09/26/2019
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: e5540697e8e64586d73e34d253fb95e549fc0301
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75972152"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-vm-using-a-templates"></a>Felügyelt identitások konfigurálása Azure-erőforrásokhoz egy Azure-beli virtuális gépen sablonok használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai automatikusan felügyelt identitást biztosítaz Azure-szolgáltatásokszámára az Azure Active Directoryban. Ezzel az identitással hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést, anélkül, hogy hitelesítő adatokat a kódot.

Ebben a cikkben az Azure Resource Manager központi telepítési sablon használatával megtudhatja, hogyan hajthatja végre a következő felügyelt identitásokat az Azure-erőforrások műveleteihez egy Azure virtuális gépen:

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure Resource Manager telepítési sablonhasználatát, tekintse meg az [áttekintő szakaszt.](overview.md) **Mindenképpen tekintse át a [rendszerhez rendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget.](overview.md#how-does-the-managed-identities-for-azure-resources-work)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Az Azure [Portalhoz](../../azure-resource-manager/management/overview.md) és a parancsfájlok futtatásához is az Azure Resource Manager-sablonok lehetővé teszik az Azure-erőforráscsoport által meghatározott új vagy módosított erőforrások üzembe helyezését. Számos lehetőség áll rendelkezésre a sablon szerkesztéséhez és telepítéséhez, helyi és portálalapú, többek között:

   - Az [Azure Marketplace-ről származó egyéni sablon](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)használatával, amely lehetővé teszi, hogy teljesen új sablont hozzon létre, vagy egy meglévő közös vagy [rövid útmutató sablonra alapozza.](https://azure.microsoft.com/documentation/templates/)
   - Egy meglévő erőforráscsoportból származó, sablon exportálása [az eredeti központi telepítésből](../../azure-resource-manager/templates/export-template-portal.md)vagy a telepítés aktuális [állapotából.](../../azure-resource-manager/templates/export-template-portal.md)
   - Egy helyi [JSON-szerkesztő (például a VS-kód)](../../azure-resource-manager/resource-manager-create-first-template.md)használatával, majd feltöltése és üzembe helyezése a PowerShell vagy a CLI használatával.
   - A Visual Studio [Azure Resource Group projekt](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) használatával hozzon létre és telepítsen egy sablont.  

A választott beállítástól függetlenül a sablon szintaxisa megegyezik a kezdeti üzembe helyezés és újratelepítés során. A rendszer vagy a felhasználó által kijelölt felügyelt identitás engedélyezése egy új vagy meglévő virtuális gépen ugyanúgy történik. Alapértelmezés szerint az Azure Resource Manager [növekményes frissítést](../../azure-resource-manager/templates/deployment-modes.md) végez a központi telepítésekhez.

## <a name="system-assigned-managed-identity"></a>Rendszerhez rendelt felügyelt identitás

Ebben a szakaszban egy Azure Resource Manager-sablon használatával engedélyezi és letiltja a rendszer által kijelölt felügyelt identitást.

### <a name="enable-system-assigned-managed-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Rendszeráltal hozzárendelt felügyelt identitás engedélyezése Azure-beli virtuális gép létrehozása kor vagy meglévő virtuális gépen

A virtuális gép rendszer által hozzárendelt felügyelt identitásának engedélyezéséhez a fióknak szüksége van a [Virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelésre.  Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Akár helyileg, akár az Azure portálon keresztül jelentkezik be az Azure-ba, használjon olyan fiókot, amely a virtuális gép-alapú Azure-előfizetéshez van társítva.

2. A rendszer által hozzárendelt felügyelt identitás engedélyezéséhez töltse be `Microsoft.Compute/virtualMachines` a sablont `resources` egy szerkesztőbe, keresse meg az érdeklődésre számot tartó erőforrást a szakaszon belül, és adja hozzá a `"identity"` tulajdonságot a `"type": "Microsoft.Compute/virtualMachines"` tulajdonsággal azonos szinten. Használja a következő szintaxist:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   },
   ```



3. Ha elkészült, a következő szakaszokat kell `resource` hozzáadni a sablon szakaszához, és a következőhöz kell hasonlítania:

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

            //The following appears only if you provisioned the optional VM extension (to be deprecated)
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

### <a name="assign-a-role-the-vms-system-assigned-managed-identity"></a>Szerepkör hozzárendelése a virtuális gép rendszeráltal hozzárendelt felügyelt identitásához

Miután engedélyezte a rendszer által hozzárendelt felügyelt identitása a virtuális gép, érdemes lehet, hogy adjon neki egy szerepkört, például **olvasó** hozzáférést az erőforráscsoporthoz, amelyben létrehozták.

Ahhoz, hogy szerepkört rendeljen a virtuális gép rendszeráltal hozzárendelt identitásához, a fióknak szüksége van a [felhasználói hozzáférés rendszergazdája](/azure/role-based-access-control/built-in-roles#user-access-administrator) szerepkör-hozzárendelésre.

1. Akár helyileg, akár az Azure portálon keresztül jelentkezik be az Azure-ba, használjon olyan fiókot, amely a virtuális gép-alapú Azure-előfizetéshez van társítva.

2. Töltse be a sablont egy [szerkesztőbe,](#azure-resource-manager-templates) és adja hozzá a következő információkat, hogy a virtuális gép **olvasó** hozzáférést az erőforráscsoporthoz, amelyben létrehozták.  A sablon szerkezete a szerkesztőtől és a választott telepítési modelltől függően változhat.

   A `parameters` szakasz alatt adja hozzá a következő:

    ```JSON
    "builtInRoleType": {
        "type": "string",
        "defaultValue": "Reader"
    },
    "rbacGuid": {
        "type": "string"
    }
    ```

    A `variables` szakasz alatt adja hozzá a következő:

    ```JSON
    "Reader": "[concat('/subscriptions/', subscription().subscriptionId, '/providers/Microsoft.Authorization/roleDefinitions/', 'acdd72a7-3385-48ef-bd42-f606fba81ae7')]"
    ```

    A `resources` szakasz alatt adja hozzá a következő:

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

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-vm"></a>Rendszeráltal hozzárendelt felügyelt identitás letiltása Azure-beli virtuális gépről

A rendszer által hozzárendelt felügyelt identitás eltávolítása a virtuális gépről, a fióknak szüksége van a [Virtuálisgép közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés.  Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Akár helyileg, akár az Azure portálon keresztül jelentkezik be az Azure-ba, használjon olyan fiókot, amely a virtuális gép-alapú Azure-előfizetéshez van társítva.

2. Töltse be a sablont `Microsoft.Compute/virtualMachines` egy [szerkesztőbe,](#azure-resource-manager-templates) és keresse meg az érdeklődésre számot tartó erőforrást a `resources` szakaszon belül. Ha olyan virtuális gépe van, amely csak a rendszer hez rendelt felügyelt identitással `None`rendelkezik, letilthatja azt az identitástípus módosításával.  

   **Microsoft.Compute/virtualMachines API 2018-06-01 verzió**

   Ha a virtuális gép rendszer- és felhasználó által `SystemAssigned` hozzárendelt felügyelt identitásokkal `UserAssigned` is `userAssignedIdentities` rendelkezik, távolítsa el az identitástípusból, és tartsa meg a szótárértékeket.

   **Microsoft.Compute/virtualMachines API 2018-06-01 verzió**

   Ha `apiVersion` az `2017-12-01` ön és a virtuális gép rendszer- és felhasználó `SystemAssigned` által hozzárendelt felügyelt `UserAssigned` identitásokkal `identityIds` is rendelkezik, távolítsa el az identitástípusból, és tartsa meg a felhasználó által hozzárendelt felügyelt identitások tömbjével együtt.  

A következő példa bemutatja, hogyan távolítson el egy rendszer által hozzárendelt felügyelt identitást egy felhasználó által hozzárendelt felügyelt identitások nélküli virtuális gépről:

 ```JSON
 {
     "apiVersion": "2018-06-01",
     "type": "Microsoft.Compute/virtualMachines",
     "name": "[parameters('vmName')]",
     "location": "[resourceGroup().location]",
     "identity": {
         "type": "None"
     }
 }
 ```

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban egy felhasználó által hozzárendelt felügyelt identitást rendel egy Azure-beli virtuális géphez az Azure Resource Manager-sablon használatával.

> [!Note]
> Ha egy Azure Resource Manager-sablon használatával szeretne felhasználó által hozzárendelt felügyelt identitást létrehozni, olvassa el a Felhasználó által hozzárendelt felügyelt identitás létrehozása című [témakört.](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)

### <a name="assign-a-user-assigned-managed-identity-to-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése Azure-beli virtuális géphez

Ha egy felhasználó által hozzárendelt identitást rendel egy virtuális géphez, a fióknak szüksége van a [virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) és a [felügyelt identitáskezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör-hozzárendelések. Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Az `resources` elem alatt adja hozzá a következő bejegyzést egy felhasználó által hozzárendelt felügyelt identitás hozzárendeléséhez a virtuális géphez.  Ügyeljen arra, `<USERASSIGNEDIDENTITY>` hogy cserélje le a felhasználó által kijelölt felügyelt identitás nevét.

   **Microsoft.Compute/virtualMachines API 2018-06-01 verzió**

   Ha `apiVersion` a, `2018-06-01`a felhasználó által hozzárendelt felügyelt identitások a `userAssignedIdentities` szótár `<USERASSIGNEDIDENTITYNAME>` formátumban tárolódnak, és az `variables` értéket a sablon szakaszában meghatározott változóban kell tárolni.

   ```JSON
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

   **Microsoft.Compute/virtualMachines API 2017-12-01 verzió**

   Ha `apiVersion` a, `2017-12-01`a felhasználó által hozzárendelt felügyelt identitások a `identityIds` tömbben tárolódnak, és az `<USERASSIGNEDIDENTITYNAME>` értéket a `variables` sablon szakaszában meghatározott változóban kell tárolni.

   ```JSON
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

3. Ha elkészült, a következő szakaszokat kell `resource` hozzáadni a sablon szakaszához, és a következőhöz kell hasonlítania:

   **Microsoft.Compute/virtualMachines API 2018-06-01 verzió**    

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
        //The following appears only if you provisioned the optional VM extension (to be deprecated)                  
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
   **Microsoft.Compute/virtualMachines API 2017-12-01 verzió**

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

        //The following appears only if you provisioned the optional VM extension (to be deprecated)                   
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

### <a name="remove-a-user-assigned-managed-identity-from-an-azure-vm"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása azure-beli virtuális gépből

A felhasználó által hozzárendelt identitás eltávolítása a virtuális gép, a fióknak szüksége van a [Virtuálisgép közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) szerepkör-hozzárendelés. Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

1. Akár helyileg, akár az Azure portálon keresztül jelentkezik be az Azure-ba, használjon olyan fiókot, amely a virtuális gép-alapú Azure-előfizetéshez van társítva.

2. Töltse be a sablont `Microsoft.Compute/virtualMachines` egy [szerkesztőbe,](#azure-resource-manager-templates) és keresse meg az érdeklődésre számot tartó erőforrást a `resources` szakaszon belül. Ha olyan virtuális gépe van, amely csak a felhasználó által hozzárendelt felügyelt identitással rendelkezik, letilthatja azt az identitástípus módosításával. `None`

   A következő példa bemutatja, hogyan távolítja el az összes felhasználó által hozzárendelt felügyelt identitásokat egy rendszer által hozzárendelt felügyelt identitások nélkül:

   ```json
    {
      "apiVersion": "2018-06-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": {
          "type": "None"
          },
    }
   ```

   **Microsoft.Compute/virtualMachines API 2018-06-01 verzió**

   Egyetlen felhasználó által hozzárendelt felügyelt identitás eltávolítása a virtuális gépről, távolítsa el azt a `useraAssignedIdentities` szótárból.

   Ha rendszerhez rendelt felügyelt identitással rendelkezik, tartsa `type` meg az `identity` értékben az érték alatt.

   **Microsoft.Compute/virtualMachines API 2017-12-01 verzió**

   Egyetlen felhasználó által hozzárendelt felügyelt identitás eltávolítása a virtuális gépről, távolítsa el azt a `identityIds` tömbből.

   Ha rendszerhez rendelt felügyelt identitással rendelkezik, tartsa `type` meg az `identity` értékben az érték alatt.

## <a name="next-steps"></a>További lépések

- [Felügyelt identitások az Azure-erőforrások áttekintése.](overview.md)
