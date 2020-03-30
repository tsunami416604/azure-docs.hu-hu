---
title: Sablon konfigurálása felügyelt identitások használatára a virtuálisgép-méretezési csoportokon – Azure AD
description: Részletes útmutató az Azure-erőforrások felügyelt identitások konfigurálásához egy virtuálisgép-méretezési csoporton egy Azure Resource Manager-sablon használatával.
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
ms.date: 02/20/2018
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2d5e324ea20b2ea82fac5b5132893d3558bd3b41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77425561"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Felügyelt identitások konfigurálása Azure-erőforrásokhoz egy Azure virtuálisgép-méretezésen egy sablon használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Az Azure-erőforrások felügyelt identitásai automatikusan felügyelt identitást biztosítaz Azure-szolgáltatásokszámára az Azure Active Directoryban. Ezzel az identitással hitelesítheti magát minden olyan szolgáltatás, amely támogatja az Azure AD-hitelesítést, anélkül, hogy hitelesítő adatokat a kódot.

Ebben a cikkben megtudhatja, hogyan hajthatja végre a következő felügyelt identitások az Azure-erőforrások műveletek egy Azure virtuálisgép-méretezési csoport segítségével az Azure Resource Manager telepítési sablon:
- A rendszeráltal hozzárendelt felügyelt identitás engedélyezése és letiltása egy Azure virtuálisgép-méretezési csoportban
- Felhasználó által hozzárendelt felügyelt identitás hozzáadása és eltávolítása Azure virtuálisgép-méretezési csoportban

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri az Azure-erőforrások felügyelt identitásait, tekintse meg az [áttekintő szakaszt.](overview.md) **Mindenképpen tekintse át a [rendszerhez rendelt és a felhasználó által hozzárendelt felügyelt identitás közötti különbséget.](overview.md#how-does-the-managed-identities-for-azure-resources-work)**
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- A jelen cikkben szereplő felügyeleti műveletek végrehajtásához a fióknak a következő Azure-szerepköralapú hozzáférés-vezérlési hozzárendelésekre van szüksége:

    > [!NOTE]
    > Nincs szükség további Azure AD-címtárszerepkör-hozzárendelésre.

    - [Virtuálisgép-közreműködő](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) egy virtuálisgép-méretezési készlet létrehozásához, valamint a rendszer és/vagy a felhasználó által hozzárendelt felügyelt identitás virtuálisgép-méretezési készletből való engedélyezéséhez és eltávolításához.
    - [Felügyelt identitásközreműködő](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkör a felhasználó által hozzárendelt felügyelt identitás létrehozásához.
    - [Felügyelt identitáskezelő](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör a felhasználó által hozzárendelt felügyelt identitás hozzárendeléséhez és eltávolításához egy virtuálisgép-méretezési csoporthoz.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Az Azure [Portalhoz](../../azure-resource-manager/management/overview.md) és a parancsfájlok futtatásához is az Azure Resource Manager-sablonok lehetővé teszik az Azure-erőforráscsoport által meghatározott új vagy módosított erőforrások üzembe helyezését. Számos lehetőség áll rendelkezésre a sablon szerkesztéséhez és telepítéséhez, helyi és portálalapú, többek között:

   - Az [Azure Marketplace-ről származó egyéni sablon](../../azure-resource-manager/templates/deploy-portal.md#deploy-resources-from-custom-template)használatával, amely lehetővé teszi, hogy teljesen új sablont hozzon létre, vagy egy meglévő közös vagy [rövid útmutató sablonra alapozza.](https://azure.microsoft.com/documentation/templates/)
   - Egy meglévő erőforráscsoportból származó, sablon exportálása [az eredeti központi telepítésből](../../azure-resource-manager/templates/export-template-portal.md)vagy a telepítés aktuális [állapotából.](../../azure-resource-manager/templates/export-template-portal.md)
   - Egy helyi [JSON-szerkesztő (például a VS-kód)](../../azure-resource-manager/resource-manager-create-first-template.md)használatával, majd feltöltése és üzembe helyezése a PowerShell vagy a CLI használatával.
   - A Visual Studio [Azure Resource Group projekt](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md) használatával hozzon létre és telepítsen egy sablont.  

A választott beállítástól függetlenül a sablon szintaxisa megegyezik a kezdeti üzembe helyezés és újratelepítés során. Felügyelt identitások engedélyezése az Azure-erőforrások egy új vagy meglévő virtuális gép ugyanúgy történik. Alapértelmezés szerint az Azure Resource Manager [növekményes frissítést](../../azure-resource-manager/templates/deployment-modes.md) végez a központi telepítésekhez.

## <a name="system-assigned-managed-identity"></a>Rendszerhez rendelt felügyelt identitás

Ebben a szakaszban egy Azure Resource Manager-sablon használatával engedélyezi és letiltja a rendszer által hozzárendelt felügyelt identitást.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>A rendszer által hozzárendelt felügyelt identitás engedélyezése virtuálisgép-méretezési csoport vagy meglévő virtuálisgép-méretezési csoport létrehozása során

1. Akár helyileg, akár az Azure portálon keresztül jelentkezik be az Azure-ba, használjon olyan fiókot, amely a virtuális gép méretezési készletét tartalmazó Azure-előfizetéshez van társítva.
2. A rendszer által hozzárendelt felügyelt identitás engedélyezéséhez töltse be `Microsoft.Compute/virtualMachinesScaleSets` a sablont egy szerkesztőbe, `identity` keresse meg az `"type": "Microsoft.Compute/virtualMachinesScaleSets"` erőforrást az erőforrásszakaszon belül, és adja hozzá a tulajdonságot a tulajdonsággal azonos szinten. Használja a következő szintaxist:

   ```JSON
   "identity": {
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> Az Azure-erőforrások virtuálisgép-méretezési készletbővítményének felügyelt identitásait szükség `extensionProfile` esetén üzembe helyezheti a sablon elemében megadott módon. Ez a lépés nem kötelező, mivel az Azure Instance metaadat-szolgáltatás (IMDS) identitásvégpont használatával is lekérheti a jogkivonatokat.  További információ: [Áttelepítés virtuálisgép-bővítményről Azure IMDS hitelesítésre.](howto-migrate-vm-extension.md)


4. Ha elkészült, a következő szakaszokat kell hozzáadni a sablon erőforrásszakaszához, és a következőkhöz kell hasonlítania:

   ```json
    "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "SystemAssigned",
            },
           "properties": {
                //other resource provider properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Rendszeráltal hozzárendelt felügyelt identitás letiltása azure-beli virtuálisgép-méretezési csoportból

Ha olyan virtuálisgép-méretezési készletet rendelkezik, amelynek már nincs szüksége rendszeráltal hozzárendelt felügyelt identitásra:

1. Akár helyileg, akár az Azure portálon keresztül jelentkezik be az Azure-ba, használjon olyan fiókot, amely a virtuális gép méretezési készletét tartalmazó Azure-előfizetéshez van társítva.

2. Töltse be a sablont `Microsoft.Compute/virtualMachineScaleSets` egy [szerkesztőbe,](#azure-resource-manager-templates) és keresse meg az érdeklődésre számot tartó erőforrást a `resources` szakaszon belül. Ha olyan virtuális gépe van, amely csak a rendszer hez rendelt felügyelt identitással `None`rendelkezik, letilthatja azt az identitástípus módosításával.

   **Microsoft.Compute/virtualMachineScaleSets API 2018-06-01 verzió**

   Ha az apiVersion, `2018-06-01` és a virtuális gép rendszer- és felhasználó `SystemAssigned` által hozzárendelt felügyelt `UserAssigned` identitások, távolítsa el az identitás típusát, és tartsa együtt a userAssignedIdentities szótár értékeket.

   **Microsoft.Compute/virtualMachineScaleSets API 2018-06-01 verzió**

   `2017-12-01` Ha az apiVersion, és a virtuálisgép-méretezési készlet rendszer- és `SystemAssigned` felhasználó által hozzárendelt `UserAssigned` felügyelt `identityIds` identitások, távolítsa el az identitás típusát, és tartsa együtt a felhasználó által hozzárendelt felügyelt identitások tömbjét.



   A következő példa bemutatja, hogyan távolítson el egy rendszer által hozzárendelt felügyelt identitást egy felhasználó által hozzárendelt felügyelt identitások nélkül lévő virtuálisgép-méretezési csoportból:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }

   }
   ```

## <a name="user-assigned-managed-identity"></a>Felhasználó által hozzárendelt felügyelt identitás

Ebben a szakaszban egy felhasználó által hozzárendelt felügyelt identitást rendel egy virtuálisgép-méretezési csoporthoz az Azure Resource Manager sablon használatával.

> [!Note]
> Ha egy Azure Resource Manager-sablon használatával szeretne felhasználó által hozzárendelt felügyelt identitást létrehozni, olvassa el a Felhasználó által hozzárendelt felügyelt identitás létrehozása című [témakört.](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity)

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás hozzárendelése virtuálisgép-méretezési csoporthoz

1. Az `resources` elem alatt adja hozzá a következő bejegyzést a felhasználó által hozzárendelt felügyelt identitás hozzárendeléséhez a virtuálisgép méretezési készletéhez.  Ügyeljen arra, `<USERASSIGNEDIDENTITY>` hogy cserélje le a felhasználó által kijelölt felügyelt identitás nevét.

   **Microsoft.Compute/virtualMachineScaleSets API 2018-06-01 verzió**

   Ha `2018-06-01`az apiVersion a rendszer, a felhasználó által hozzárendelt felügyelt identitások a `userAssignedIdentities` szótárformátumban tárolódnak, és az `<USERASSIGNEDIDENTITYNAME>` értéket a `variables` sablon szakaszában meghatározott változóban kell tárolni.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "userAssignedIdentities": {
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
           }
       }

   }
   ```   

   **Microsoft.Compute/virtualMachineScaleSets API 2017-12-01 verzió**

   Ha `apiVersion` az `2017-12-01` Ön vagy korábban, a felhasználó által hozzárendelt `identityIds` felügyelt `<USERASSIGNEDIDENTITYNAME>` identitások a tömbben tárolódnak, és az értéket a sablon változói szakaszában meghatározott változóban kell tárolni.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ```
> [!NOTE]
> Az Azure-erőforrások virtuálisgép-méretezési készletbővítményének felügyelt identitásait szükség `extensionProfile` esetén üzembe helyezheti a sablon elemében megadott módon. Ez a lépés nem kötelező, mivel az Azure Instance metaadat-szolgáltatás (IMDS) identitásvégpont használatával is lekérheti a jogkivonatokat.  További információ: [Áttelepítés virtuálisgép-bővítményről Azure IMDS hitelesítésre.](howto-migrate-vm-extension.md)

3. Ha elkészült, a sablonnak az alábbiakhoz hasonlóan kell kinéznie:

   **Microsoft.Compute/virtualMachineScaleSets API 2018-06-01 verzió**   

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2018-06-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "userAssignedIdentities": {
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]": {}
                }
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```

   **Microsoft.Compute/virtualMachines API 2017-12-01 verzió**

   ```json
   "resources": [
        {
            //other resource provider properties...
            "apiVersion": "2017-12-01",
            "type": "Microsoft.Compute/virtualMachineScaleSets",
            "name": "[variables('vmssName')]",
            "location": "[resourceGroup().location]",
            "identity": {
                "type": "UserAssigned",
                "identityIds": [
                    "[resourceID('Microsoft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITYNAME>'))]"
                ]
            },
           "properties": {
                //other virtual machine properties...
                "virtualMachineProfile": {
                    //other virtual machine profile properties...
                    //The following appears only if you provisioned the optional virtual machine scale set extension (to be deprecated)    
                    "extensionProfile": {
                        "extensions": [
                            {
                                "name": "ManagedIdentityWindowsExtension",
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
                    }
                }
            }
        }
    ]
   ```
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása egy Azure virtuálisgép-méretezési csoportból

Ha olyan virtuálisgép-méretezési készletet rendelkezik, amelyhez már nincs szükség a felhasználó által hozzárendelt felügyelt identitásra:

1. Akár helyileg, akár az Azure portálon keresztül jelentkezik be az Azure-ba, használjon olyan fiókot, amely a virtuális gép méretezési készletét tartalmazó Azure-előfizetéshez van társítva.

2. Töltse be a sablont `Microsoft.Compute/virtualMachineScaleSets` egy [szerkesztőbe,](#azure-resource-manager-templates) és keresse meg az érdeklődésre számot tartó erőforrást a `resources` szakaszon belül. Ha olyan virtuálisgép-méretezési készletet használ, amely csak a felhasználó által hozzárendelt felügyelt `None`identitással rendelkezik, letilthatja azt az identitástípus módosításával.

   A következő példa bemutatja, hogyan távolítja el az összes felhasználó által hozzárendelt felügyelt identitásokat egy rendszer által hozzárendelt felügyelt identitások nélkül:

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2018-06-01",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "None"
        }
   }
   ```

   **Microsoft.Compute/virtualMachineScaleSets API 2018-06-01 verzió**

   Ha egyetlen felhasználó által hozzárendelt felügyelt identitást szeretne eltávolítani egy `userAssignedIdentities` virtuálisgép-méretezési csoportból, távolítsa el azt a szótárból.

   Ha rendszerhez rendelt identitással rendelkezik, tartsa meg `type` az `identity` érték alatt lévő értékben.

   **Microsoft.Compute/virtualMachineScaleSets API 2017-12-01 verzió**

   Ha egyetlen felhasználó által hozzárendelt felügyelt identitást szeretne eltávolítani egy `identityIds` virtuálisgép-méretezési csoportból, távolítsa el azt a tömbből.

   Ha rendszerhez rendelt felügyelt identitással rendelkezik, tartsa `type` meg az `identity` értékben az érték alatt.

## <a name="next-steps"></a>További lépések

- [Felügyelt identitások az Azure-erőforrások áttekintése.](overview.md)
