---
title: Felügyeltszolgáltatás-identitás konfigurálása az Azure virtuálisgép-méretezési csoport, egy sablon használatával
description: Részletes útmutató a Felügyeltszolgáltatás-identitás konfigurálása az Azure VMSS, egy Azure Resource Manager-sablon használatával.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 68304b3e5eea50aba28f46344abcbd7ad060c5c8
ms.sourcegitcommit: d2f2356d8fe7845860b6cf6b6545f2a5036a3dd6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/16/2018
ms.locfileid: "42060161"
---
# <a name="configure-managed-service-identity-on-virtual-machine-scale-using-a-template"></a>Felügyeltszolgáltatás-identitás konfigurálása a virtuálisgép-méretezési csoport sablon használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyeltszolgáltatás-identitás az Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből elsajátíthatja az alábbi műveletek Felügyeltszolgáltatás-identitását egy Azure-beli virtuálisgép-méretezési Azure Resource Manager üzembe helyezési sablon használatával:
- Engedélyezheti és tilthatja le a rendszer által hozzárendelt identitással egy Azure-beli virtuálisgép-méretezési
- Hozzáadhat és eltávolíthat egy Azure-beli virtuálisgép-méretezési egy felhasználóhoz hozzárendelt identitás

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a Felügyeltszolgáltatás-identitást, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy rendszer által hozzárendelt, és a felhasználóhoz hozzárendelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi szerepkör-hozzárendelések van szüksége:
    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) készlet egy virtuálisgép-méretezési csoport létrehozása és engedélyezése és a remove-rendszer és/vagy a felhasználó hozzárendelt felügyelt identitás egy virtuálisgép-méretezési csoportot.
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkört a felhasználóhoz hozzárendelt identitás létrehozása.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör hozzárendelése, és távolítsa el a felhasználóhoz hozzárendelt identitás, a kezdő és a egy virtuálisgép-méretezési csoportot.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Csakúgy, mint az Azure Portalon, és parancsfájl-kezelési, [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) sablonok lehetővé teszi, hogy üzembe helyezése az Azure-erőforráscsoport által meghatározott új vagy megváltozott erőforrásokat. Több lehetőség is elérhető, és a helyi és portálalapú, beleértve a központi telepítési sablon Szerkesztés:

   - Használatával egy [egyéni sablont az Azure Marketplace-ről](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), amely lehetővé teszi, hogy a sablon létrehozása az alapoktól, vagy egy meglévő közös alapul vagy [gyorsindítási sablon](https://azure.microsoft.com/documentation/templates/).
   - Sablon exportálása vagy egy meglévő erőforráscsoportot, a Származtatás [az eredeti üzembe helyezés](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), vagy a [az üzemelő példány aktuális állapotát](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Egy helyi [JSON-szerkesztővel (például a VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), majd feltöltését és üzembe helyezése a PowerShell vagy parancssori felület használatával.
   - A Visual Studio használatával [Azure erőforráscsoport-projekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) hozzon létre és helyezhet üzembe sablont is.  

A lehetőséget választja, függetlenül a sablon szintaxisa megegyezik kezdeti üzembe helyezése és újbóli üzembe helyezés során. Engedélyezi a Felügyeltszolgáltatás-identitását egy új vagy meglévő virtuális gépen azonos módon történik. Emellett, alapértelmezés szerint az Azure Resource Manager elvégzi az [növekményes frissítés](../../azure-resource-manager/deployment-modes.md) üzemelő példányokhoz.

## <a name="system-assigned-identity"></a>Rendszerhez rendelt identitáshoz

Ebben a szakaszban engedélyezze, majd tiltsa le a rendszer hozzárendelt identitás egy Azure Resource Manager-sablon használatával.

### <a name="enable-system-assigned-identity-during-creation-the-creation-of-a-virtual-machines-scale-set-or-a-existing-virtual-machine-scale-set"></a>Rendszer által hozzárendelt identitással engedélyezése egy virtuálisgép-méretezési csoportot vagy egy meglévő virtuálisgép-méretezési csoportot létrehozását a létrehozása során

1. Bejelentkezik az Azure-bA helyileg vagy az Azure Portalon, hogy az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoportba tartozó fiókot kell használnia.
   
2. A rendszer által hozzárendelt identitással engedélyezése, a sablon betöltése a szerkesztő, keresse meg a `Microsoft.Compute/virtualMachinesScaleSets` erőforrás az erőforrások házirendsablonokkal szakaszt, és adja hozzá a `identity` tulajdonság azonos szinten, a `"type": "Microsoft.Compute/virtualMachines"` tulajdonság. Az alábbi szintaxissal:

   ```JSON
   "identity": { 
       "type": "SystemAssigned"
   }
   ```

3. (Nem kötelező) Adja hozzá a virtuális gép méretezési kiterjesztésű fájlt a Felügyeltszolgáltatás-identitást egy `extensionsProfile` elemet. Ez a lépés nem kötelező használni, mivel az Azure példány metaadat szolgáltatás (IMDS) identitás használatával, valamint a jogkivonatok.  Az alábbi szintaxissal:

   >[!NOTE] 
   > Az alábbi példa azt feltételezi, hogy egy Windows virtuális gép méretezési csoport bővítményének (`ManagedIdentityExtensionForWindows`) lesz üzembe helyezve. Beállíthatja a Linux használatával `ManagedIdentityExtensionForLinux` ehelyett a `"name"` és `"type"` elemeket.
   >

   ```json
   "extensionProfile": {
        "extensions": [
            {
                "name": "MSIWindowsExtension",
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

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Tiltsa le az Azure-beli virtuálisgép-méretezési csoportot, a rendszer által hozzárendelt identitással

Ha egy virtuálisgép-méretezési csoportban, már nem kell a felügyeltszolgáltatás-identitás:

1. Bejelentkezik az Azure-bA helyileg vagy az Azure Portalon, hogy az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoportba tartozó fiókot kell használnia.

2. Betölteni a sablont, egy [szerkesztő](#azure-resource-manager-templates) , és keresse meg a `Microsoft.Compute/virtualMachineScaleSets` házirendsablonokkal erőforrás a `resources` szakaszban. Ha egy virtuális Gépet, amely csak a rendszer által hozzárendelt identitással rendelkezik, letilthatja az identitás típusúra `None`.

   **API-verzió a 2018-06-01 Microsoft.Compute/virtualMachineScaleSets**

   Ha az API-verzió `2018-06-01` és a virtuális gép system és a felhasználó által hozzárendelt identitások is van, távolítsa el `SystemAssigned` identitástípus és tarthatja `UserAssigned` és a userAssignedIdentities szótár értékeket.

   **Microsoft.Compute/virtualMachineScaleSets API-verzió a 2018-06-01-es vagy korábbi kiadásai**

   Ha az API-verzió `2017-12-01` és a virtuális gép méretezési system és a felhasználó által hozzárendelt identitások is van, távolítsa el `SystemAssigned` identitástípus és tarthatja `UserAssigned` együtt a `identityIds` a felhasználó által hozzárendelt identitások tömbje. 
   
    

   Az alábbi példa bemutatja, hogyan távolítsa el a rendszer egy virtuálisgép-méretezési csoport nincs a felhasználói identitások hozzárendelt identitás:
   
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

## <a name="user-assigned-identity"></a>A felhasználóhoz hozzárendelt identitás

Ebben a szakaszban rendeljen hozzá egy a felhasználóhoz hozzárendelt identitás az Azure Resource Manager-sablon használatával virtuálisgép-méretezési csoportot.

> [!Note]
> Egy Azure Resource Manager-sablon használatával a felhasználóhoz hozzárendelt identitás létrehozása: [felhasználóhoz hozzárendelt identitás létrehozása](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Rendelje hozzá egy a felhasználóhoz hozzárendelt identitás az Azure vmss-hez

1. Alatt a `resources` elemben adja hozzá a következő bejegyzés hozzárendelni egy felhasználóhoz hozzárendelt identitás a virtuálisgép-méretezési csoporthoz.  Ne felejtse el `<USERASSIGNEDIDENTITY>` létrehozta a felhasználóhoz hozzárendelt identitás nevére.
   
   **API-verzió a 2018-06-01 Microsoft.Compute/virtualMachineScaleSets**

   Az API-verzió esetén `2018-06-01`, a felhasználó által hozzárendelt identitások vannak tárolva a `userAssignedIdentities` szótár formátum és a `<USERASSIGNEDIDENTITYNAME>` egy változóban meghatározott értéket kell tárolni a `variables` szakasz a sablon.

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

   **Microsoft.Compute/virtualMachineScaleSets API 2017-12-01-es verzió**
    
   Ha a `apiVersion` van `2017-12-01` vagy a korábban, a felhasználó által hozzárendelt identitások vannak tárolva a `identityIds` tömböt, és a `<USERASSIGNEDIDENTITYNAME>` értékét a sablon a változók szakaszban definiált egy változót kell tárolni.

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

2. (Nem kötelező) Adja hozzá a következő bejegyzés alatt a `extensionProfile` elem a VMSS felügyelt identitás kiterjesztése hozzárendelése. Ez a lépés nem kötelező használni, mivel az Azure példány metaadat szolgáltatás (IMDS) identitás-végpont használatával, valamint a jogkivonatok. Az alábbi szintaxissal:
   
    ```JSON
       "extensionProfile": {
            "extensions": [
                {
                    "name": "MSIWindowsExtension",
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

3. Amikor elkészült, a sablon az alábbihoz hasonlóan kell kinéznie:
   
   **API-verzió a 2018-06-01 Microsoft.Compute/virtualMachineScaleSets**   

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

   **Korábban Microsoft.Compute/virtualMachines API 2017-12-01-es verzió eand**

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
### <a name="remove-user-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>A felhasználóhoz hozzárendelt identitás eltávolítása egy Azure-beli virtuálisgép-méretezési csoportot

Ha egy virtuálisgép-méretezési csoportban, már nem kell a felügyeltszolgáltatás-identitás:

1. Bejelentkezik az Azure-bA helyileg vagy az Azure Portalon, hogy az Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoportba tartozó fiókot kell használnia.

2. Betölteni a sablont, egy [szerkesztő](#azure-resource-manager-templates) , és keresse meg a `Microsoft.Compute/virtualMachineScaleSets` házirendsablonokkal erőforrás a `resources` szakaszban. Ha egy virtuális gép méretezési csoportot, amely csak a felhasználóhoz hozzárendelt identitás rendelkezik, letilthatja, módosítsa úgy az identitás típus `None`.

   Az alábbi példa bemutatja, hogyan távolítsa el az összes felhasználói identitások hozzárendelt virtuális gép nem hozzárendelt identitások rendszert:

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
   
   **API-verzió a 2018-06-01 Microsoft.Compute/virtualMachineScaleSets**
    
   Eltávolítja a hozzárendelve egyetlen felhasználói identitást a virtuális gép méretezési, távolítsa el a `userAssignedIdentities` szótárban.

   Ha egy rendszer által hozzárendelt identitással, tárolja a a a `type` értékét a `identity` értéket.

   **Microsoft.Compute/virtualMachineScaleSets API 2017-12-01-es verzió**

   Egy virtuálisgép-méretezési csoportot egy egyetlen a felhasználóhoz hozzárendelt identitás eltávolításához távolítsa el a `identityIds` tömb.

   Ha egy rendszer által hozzárendelt identitással, tárolja a a a `type` értékét a `identity` értéket.
   
## <a name="next-steps"></a>További lépések

- Szélesebb perspektíva Felügyeltszolgáltatás-identitás kapcsolatban, olvassa el a [Felügyeltszolgáltatás-identitás – áttekintés](overview.md).

