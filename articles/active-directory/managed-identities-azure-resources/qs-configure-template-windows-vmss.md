---
title: Felügyelt identitások az Azure-erőforrások konfigurálása egy virtuálisgép-méretezési csoport, egy sablon használatával
description: Részletes útmutató az Azure-erőforrások felügyelt identitások konfigurálása a virtuálisgép-méretezési csoportot beállítani, egy Azure Resource Manager-sablon használatával.
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
ms.openlocfilehash: 6ecbac8af86c3c2c76b7710eb61f71481b86291b
ms.sourcegitcommit: e43ea344c52b3a99235660960c1e747b9d6c990e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "59009869"
---
# <a name="configure-managed-identities-for-azure-resources-on-an-azure-virtual-machine-scale-using-a-template"></a>Felügyelt identitások az Azure-erőforrások konfigurálása az Azure virtuálisgép-méretezési csoport, egy sablon használatával

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt identitások az Azure-erőforrások Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből megismerheti, hogyan hajthat végre a következő felügyelt identitások Azure-erőforrások műveletek egy Azure-beli virtuálisgép-méretezési Azure Resource Manager üzembe helyezési sablon használatával:
- Engedélyezheti és tilthatja le a rendszer által hozzárendelt felügyelt identitás egy Azure-beli virtuálisgép-méretezési csoportot
- Hozzáadhat és eltávolíthat egy felhasználó által hozzárendelt felügyelt identitás egy Azure-beli virtuálisgép-méretezési csoportot

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a felügyelt identitások Azure-erőforrások számára, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség a rendszer által hozzárendelt, és a felhasználó által hozzárendelt felügyelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi Azure szerepkör-alapú access control-hozzárendelések van szüksége:

    > [!NOTE]
    > Nincsenek további Azure AD directory szerepkör-hozzárendelések megadása kötelező.

    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) egy virtuálisgép-méretezési csoport létrehozása és engedélyezése és rendszer-és/vagy felhasználó által hozzárendelt felügyelt identitás eltávolítása egy virtuálisgép-méretezési csoportot.
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) hozhat létre felhasználó által hozzárendelt szerepkör felügyelt identitás.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör hozzárendelése és eltávolítása, felhasználó által hozzárendelt felügyelt identitás, a kezdő és a egy virtuálisgép-méretezési csoportot.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Csakúgy, mint az Azure Portalon, és parancsfájl-kezelési, [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) sablonok lehetővé teszi, hogy üzembe helyezése az Azure-erőforráscsoport által meghatározott új vagy megváltozott erőforrásokat. Több lehetőség is elérhető, és a helyi és portálalapú, beleértve a központi telepítési sablon Szerkesztés:

   - Használatával egy [egyéni sablont az Azure Marketplace-ről](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), amely lehetővé teszi, hogy a sablon létrehozása az alapoktól, vagy egy meglévő közös alapul vagy [gyorsindítási sablon](https://azure.microsoft.com/documentation/templates/).
   - Sablon exportálása vagy egy meglévő erőforráscsoportot, a Származtatás [az eredeti üzembe helyezés](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates), vagy a [az üzemelő példány aktuális állapotát](../../azure-resource-manager/manage-resource-groups-portal.md#export-resource-groups-to-templates).
   - Egy helyi [JSON-szerkesztővel (például a VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), majd feltöltését és üzembe helyezése a PowerShell vagy parancssori felület használatával.
   - A Visual Studio használatával [Azure erőforráscsoport-projekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) hozzon létre és helyezhet üzembe sablont is.  

A lehetőséget választja, függetlenül a sablon szintaxisa megegyezik kezdeti üzembe helyezése és újbóli üzembe helyezés során. Az Azure-erőforrások egy új vagy meglévő virtuális Gépet a felügyelt identitásokból engedélyezése ugyanolyan módon történik. Emellett, alapértelmezés szerint az Azure Resource Manager elvégzi az [növekményes frissítés](../../azure-resource-manager/deployment-modes.md) üzemelő példányokhoz.

## <a name="system-assigned-managed-identity"></a>Rendszer által hozzárendelt felügyelt identitás

Ebben a szakaszban engedélyezze, majd tiltsa le a felügyelt rendszer által hozzárendelt identitások egy Azure Resource Manager-sablon használatával.

### <a name="enable-system-assigned-managed-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-an-existing-virtual-machine-scale-set"></a>Rendszer által hozzárendelt engedélyezése felügyelt identitás létrehozása során egy virtuálisgép-méretezési csoportot vagy egy meglévő virtuális gép méretezési csoportjának létrehozása

1. Bejelentkezik az Azure-bA helyileg vagy az Azure Portalon, hogy az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoportba tartozó fiókot kell használnia.
2. Ahhoz, hogy a rendszer által hozzárendelt felügyelt identitás, a sablon betöltése a szerkesztő, keresse meg a `Microsoft.Compute/virtualMachinesScaleSets` erőforrás az erőforrások házirendsablonokkal szakaszt, és adja hozzá a `identity` tulajdonság azonos szinten, a `"type": "Microsoft.Compute/virtualMachinesScaleSets"` tulajdonság. Az alábbi szintaxissal:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   }
   ```

> [!NOTE]
> Előfordulhat, hogy igény szerint üzembe helyezi a felügyelt identitások az Azure-erőforrások virtuális gép méretezési csoport bővítményének megadásával, a a `extensionProfile` elem a sablon. Ez a lépés nem kötelező használni, mivel az Azure példány metaadat szolgáltatás (IMDS) identitás-végpont használatával, valamint a jogkivonatok.  További információkért lásd: [áttelepítése a Virtuálisgép-bővítmény az Azure IMDS hitelesítéshez](howto-migrate-vm-extension.md).


4. Ha elkészült, a következő szakaszok kell hozzáadni a sablon erőforrás szakaszába, és a következőket kell hasonlítania:

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

### <a name="disable-a-system-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Tiltsa le a rendszer által hozzárendelt felügyelt identitás, az Azure-beli virtuálisgép-méretezési csoportot

Ha egy virtuális gép méretezési csoportot, amely egy rendszer által hozzárendelt felügyelt identitás már nincs szüksége van:

1. Bejelentkezik az Azure-bA helyileg vagy az Azure Portalon, hogy az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoportba tartozó fiókot kell használnia.

2. Betölteni a sablont, egy [szerkesztő](#azure-resource-manager-templates) , és keresse meg a `Microsoft.Compute/virtualMachineScaleSets` házirendsablonokkal erőforrás a `resources` szakaszban. Ha egy virtuális Gépet, amely csak a felügyelt identitás alapértelmezett rendelkezik, letilthatja az identitás típusúra `None`.

   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**

   Ha az API-verzió `2018-06-01` és a virtuális gép rendelkezik a rendszer mind a felügyelt identitásokból felhasználó által hozzárendelt, távolítsa el `SystemAssigned` identitástípus és tarthatja `UserAssigned` és a userAssignedIdentities szótár értékeket.

   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**

   Ha az API-verzió `2017-12-01` és rendszer mind a felügyelt identitásokból felhasználó által hozzárendelt, távolítsa el a virtuálisgép-méretezési `SystemAssigned` identitástípus és tarthatja `UserAssigned` az a `identityIds` a felhasználó által hozzárendelt felügyelt tömbje identitások. 
   
    

   Az alábbi példa bemutatja, hogyan távolít el egy rendszer által hozzárendelt felügyelt identitás nem felügyelt felhasználó által hozzárendelt identitások rendelkező virtuálisgép-méretezési csoportot:
   
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

Ebben a szakaszban egy virtuálisgép-méretezési csoport Azure Resource Manager-sablon használatával rendelheti hozzá a felhasználó által hozzárendelt felügyelt identitás.

> [!Note]
> Egy Azure Resource Manager-sablon használatával felügyelt felhasználó által hozzárendelt identitások létrehozásával kapcsolatban lásd: [felügyelt felhasználó által hozzárendelt identitás létrehozása](how-to-manage-ua-identity-arm.md#create-a-user-assigned-managed-identity).

### <a name="assign-a-user-assigned-managed-identity-to-a-virtual-machine-scale-set"></a>Egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése egy virtuálisgép-méretezési csoportot

1. Alatt a `resources` elemben adja hozzá a következő bejegyzés egy felhasználó által hozzárendelt felügyelt identitás hozzárendelése a virtuális gép méretezési csoportját.  Ne felejtse el `<USERASSIGNEDIDENTITY>` a felhasználó által hozzárendelt nevű felügyelt identitás hozott létre.
   
   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**

   Az API-verzió esetén `2018-06-01`, a felügyelt felhasználó által hozzárendelt identitások vannak tárolva a `userAssignedIdentities` szótár formátum és a `<USERASSIGNEDIDENTITYNAME>` egy változóban meghatározott értéket kell tárolni a `variables` szakasz a sablon.

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

   **Microsoft.Compute/virtualMachineScaleSets API version 2017-12-01**
    
   Ha a `apiVersion` van `2017-12-01` vagy a korábban, a felügyelt felhasználó által hozzárendelt identitások vannak tárolva a `identityIds` tömböt, és a `<USERASSIGNEDIDENTITYNAME>` értékét a sablon a változók szakaszban definiált egy változót kell tárolni.

   ```json
   {
       "name": "[variables('vmssName')]",
       "apiVersion": "2017-03-30",
       "location": "[parameters(Location')]",
       "identity": {
           "type": "userAssigned",
           "identityIds": [
               "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/',variables('<USERASSIGNEDIDENTITY>'))]"
           ]
       }

   }
   ``` 
> [!NOTE]
> Előfordulhat, hogy igény szerint üzembe helyezi a felügyelt identitások az Azure-erőforrások virtuális gép méretezési csoport bővítményének megadásával, a a `extensionProfile` elem a sablon. Ez a lépés nem kötelező használni, mivel az Azure példány metaadat szolgáltatás (IMDS) identitás-végpont használatával, valamint a jogkivonatok.  További információkért lásd: [áttelepítése a Virtuálisgép-bővítmény az Azure IMDS hitelesítéshez](howto-migrate-vm-extension.md).

3. Amikor elkészült, a sablon az alábbihoz hasonlóan kell kinéznie:
   
   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**   

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

   **Microsoft.Compute/virtualMachines API 2017-12-01-es verzió**

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
   ### <a name="remove-user-assigned-managed-identity-from-an-azure-virtual-machine-scale-set"></a>Felhasználó által hozzárendelt felügyelt identitás eltávolítása egy Azure-beli virtuálisgép-méretezési csoportot

Ha egy virtuális gép méretezési csoportot, amely a felhasználó által hozzárendelt felügyelt identitás már nincs szüksége van:

1. Bejelentkezik az Azure-bA helyileg vagy az Azure Portalon, hogy az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoportba tartozó fiókot kell használnia.

2. Betölteni a sablont, egy [szerkesztő](#azure-resource-manager-templates) , és keresse meg a `Microsoft.Compute/virtualMachineScaleSets` házirendsablonokkal erőforrás a `resources` szakaszban. Ha egy virtuális gép méretezési csoportot, amely csak a felhasználó által hozzárendelt felügyelt identitás rendelkezik, letilthatja az identitás típusúra `None`.

   Az alábbi példa bemutatja, hogyan eltávolítja az összes felhasználó által hozzárendelt felügyelt identitások egy virtuális Gépet a nem felügyelt rendszer által hozzárendelt identitások:

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
   
   **Microsoft.Compute/virtualMachineScaleSets API version 2018-06-01**
    
   Egy virtuálisgép-méretezési csoportot egy egyetlen felhasználó által hozzárendelt felügyelt identitás eltávolításához távolítsa el a `userAssignedIdentities` szótárban.

   Ha egy rendszer által hozzárendelt identitással, tárolja a a a `type` értékét a `identity` értéket.

   **Microsoft.Compute/virtualMachineScaleSets API version 2017-12-01**

   Egy virtuálisgép-méretezési csoportot egy egyetlen felhasználó által hozzárendelt felügyelt identitás eltávolításához távolítsa el a `identityIds` tömb.

   Ha egy rendszer által hozzárendelt felügyelt identitás, tárolja a a a `type` értékét a `identity` értéket.
   
## <a name="next-steps"></a>További lépések

- [Felügyelt identitások Azure-erőforrások áttekintő](overview.md).

