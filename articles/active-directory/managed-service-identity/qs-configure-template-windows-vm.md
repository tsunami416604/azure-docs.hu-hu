---
title: Felügyeltszolgáltatás-identitás konfigurálása Azure virtuális gép sablon használatával
description: Részletes útmutató a Felügyeltszolgáltatás-identitás konfigurálása az Azure virtuális gép, egy Azure Resource Manager-sablon használatával.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 4305ad8cfab911baccc4b7c4dc7ee9384dab06fd
ms.sourcegitcommit: 615403e8c5045ff6629c0433ef19e8e127fe58ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2018
ms.locfileid: "39579476"
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>A virtuális gépek Felügyeltszolgáltatás-identitás konfigurálása egy sablon használatával

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyeltszolgáltatás-identitás az Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ebből a cikkből elsajátíthatja az alábbi műveletek Felügyeltszolgáltatás-identitás-beli virtuális gépen, az Azure Resource Manager üzembe helyezési sablon használatával:

## <a name="prerequisites"></a>Előfeltételek

- Ha még nem ismeri a Felügyeltszolgáltatás-identitást, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy rendszer által hozzárendelt, és a felhasználóhoz hozzárendelt identitás](overview.md#how-does-it-work)**.
- Ha még nincs Azure-fiókja, a folytatás előtt [regisztráljon egy ingyenes fiókra](https://azure.microsoft.com/free/).
- Ez a cikk a felügyeleti műveleteket hajt végre, a fiók az alábbi szerepkör-hozzárendelések van szüksége:
    - [Virtuális gépek Közreműködője](/azure/role-based-access-control/built-in-roles#virtual-machine-contributor) virtuális gép létrehozása és engedélyezése, és távolítsa el a rendszer és/vagy a felhasználó hozzárendelt felügyelt identitás Azure virtuális gépből.
    - [Felügyelt identitások Közreműködője](/azure/role-based-access-control/built-in-roles#managed-identity-contributor) szerepkört a felhasználóhoz hozzárendelt identitás létrehozása.
    - [Felügyelt identitások üzemeltetője](/azure/role-based-access-control/built-in-roles#managed-identity-operator) szerepkör hozzárendelése és a egy felhasználóhoz hozzárendelt identitás távolítsa el, és egy virtuális géphez.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Csakúgy, mint az Azure Portalon, és parancsfájl-kezelési, [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) sablonok lehetővé teszi, hogy üzembe helyezése az Azure-erőforráscsoport által meghatározott új vagy megváltozott erőforrásokat. Több lehetőség is elérhető, és a helyi és portálalapú, beleértve a központi telepítési sablon Szerkesztés:

   - Használatával egy [egyéni sablont az Azure Marketplace-ről](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), amely lehetővé teszi, hogy a sablon létrehozása az alapoktól, vagy egy meglévő közös alapul vagy [gyorsindítási sablon](https://azure.microsoft.com/documentation/templates/).
   - Sablon exportálása vagy egy meglévő erőforráscsoportot, a Származtatás [az eredeti üzembe helyezés](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), vagy a [az üzemelő példány aktuális állapotát](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Egy helyi [JSON-szerkesztővel (például a VS Code)](../../azure-resource-manager/resource-manager-create-first-template.md), majd feltöltését és üzembe helyezése a PowerShell vagy parancssori felület használatával.
   - A Visual Studio használatával [Azure erőforráscsoport-projekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) hozzon létre és helyezhet üzembe sablont is.  

A lehetőséget választja, függetlenül a sablon szintaxisa megegyezik kezdeti üzembe helyezése és újbóli üzembe helyezés során. A rendszer vagy az azzal a felhasználóhoz hozzárendelt identitás egy új vagy meglévő virtuális gépen azonos módon történik. Emellett, alapértelmezés szerint az Azure Resource Manager elvégzi az [növekményes frissítés](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) üzemelő példányokhoz.

## <a name="system-assigned-identity"></a>Rendszerhez rendelt identitáshoz

Ebben a szakaszban engedélyezze, majd tiltsa le a rendszer hozzárendelt identitás egy Azure Resource Manager-sablon használatával.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Engedélyezze a rendszerhez rendelt identitáshoz egy Azure virtuális Gépen vagy egy meglévő virtuális Gépet a létrehozása során

1. Jelentkezzen be az Azure-bA helyileg vagy az Azure Portalon az Azure-előfizetéshez társított olyan fiókot használjon, amely tartalmazza a virtuális Gépet.

2. A sablon betöltése be egy szerkesztőt, után keresse meg a `Microsoft.Compute/virtualMachines` házirendsablonokkal erőforrás a `resources` szakaszban. Öné kissé eltérhetnek az alábbi képernyőfelvételen látható, a szerkesztő használata függően előfordulhat, hogy keresse meg, és hogy szerkeszti egy sablont egy új központi telepítést vagy a meglévőt.

   >[!NOTE] 
   > Ez a példa feltételezi, hogy változók például `vmName`, `storageAccountName`, és `nicName` van definiálva a sablonban.
   >

   ![Képernyőkép a sablonhoz: keresse meg a virtuális gép](../managed-service-identity/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Ahhoz, hogy a rendszerhez rendelt identitáshoz, adja hozzá a `"identity"` tulajdonság azonos szinten, a `"type": "Microsoft.Compute/virtualMachines"` tulajdonság. Az alábbi szintaxissal:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. (Nem kötelező) Adja hozzá a virtuális gépek Felügyeltszolgáltatás-identitás bővítményt, mint egy `resources` elemet. Ez a lépés nem kötelező használni, mivel az Azure példány metaadat szolgáltatás (IMDS) identitás-végpont használatával, valamint a jogkivonatok.  Az alábbi szintaxissal:

   >[!NOTE] 
   > Az alábbi példa feltételezi, hogy Windows VM-bővítmény (`ManagedIdentityExtensionForWindows`) lesz üzembe helyezve. Beállíthatja a Linux használatával `ManagedIdentityExtensionForLinux` ehelyett a `"name"` és `"type"` elemeket.
   >

   ```JSON
   { 
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "[concat(variables('vmName'),'/ManagedIdentityExtensionForWindows')]",
       "apiVersion": "2016-03-30",
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

5. Ha elkészült, a sablon az alábbihoz hasonlóan kell kinéznie:

   ![Képernyőkép a frissítés után a sablon](../managed-service-identity/media/msi-qs-configure-template-windows-vm/template-file-after.png)

### <a name="assign-a-role-the-vms-system-assigned-identity"></a>Szerepkör hozzárendelése a virtuális gép rendszer által hozzárendelt identitással

Miután engedélyezte a rendszer által hozzárendelt identitással a virtuális Gépen, érdemes lehet megadni, például egy szerepkör **olvasó** hozzáférés az erőforráscsoporthoz, amelyben létrehozták.

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

### <a name="disable-a-system-assigned-identity-from-an-azure-vm"></a>Tiltsa le a rendszer hozzárendelt identitás Azure virtuális gépből

Ha egy virtuális Gépet, amely már nincs szüksége a felügyeltszolgáltatás-identitás van:

1. Jelentkezzen be az Azure-bA helyileg vagy az Azure Portalon az Azure-előfizetéshez társított olyan fiókot használjon, amely tartalmazza a virtuális Gépet.

2. Betölteni a sablont, egy [szerkesztő](#azure-resource-manager-templates) , és keresse meg a `Microsoft.Compute/virtualMachines` házirendsablonokkal erőforrás a `resources` szakaszban. Ha egy virtuális Gépet, amely csak a rendszer által hozzárendelt identitással rendelkezik, letilthatja az identitás típusúra `None`.  Ha a virtuális gép system és a felhasználó által hozzárendelt identitások is van, távolítsa el `SystemAssigned` identitástípus és tarthatja `UserAssigned` együtt a `identityIds` a felhasználó által hozzárendelt identitások tömbje.  Az alábbi példa bemutatja, hogyan távolítsa el a rendszer hozzárendelt identitás egy virtuális gépről a nem a felhasználói identitások:
   
   ```JSON
    {
      "apiVersion": "2017-12-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```

## <a name="user-assigned-identity"></a>A felhasználóhoz hozzárendelt identitás

Ebben a szakaszban rendel egy felhasználóhoz hozzárendelt identitás egy Azure virtuális Gépen az Azure Resource Manager-sablon használatával.

> [!Note]
> Egy Azure Resource Manager-sablon használatával a felhasználóhoz hozzárendelt identitás létrehozása: [felhasználóhoz hozzárendelt identitás létrehozása](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

 ### <a name="assign-a-user-assigned-identity-to-an-azure-vm"></a>Rendelje hozzá egy a felhasználóhoz hozzárendelt identitás Azure virtuális gépekhez

1. Alatt a `resources` elemben adja hozzá a következő bejegyzés egy felhasználóhoz hozzárendelt identitás hozzárendelése a virtuális gép.  Ne felejtse el `<USERASSIGNEDIDENTITY>` létrehozta a felhasználóhoz hozzárendelt identitás nevére.
   
   > [!Important]
   > A `<USERASSIGNEDIDENTITYNAME>` érték a következő példa egy változóban kell tárolni.  Emellett a jelenleg támogatott megvalósítás, felhasználó által hozzárendelt identitások hozzárendelése egy virtuális géphez a Resource Manager-sablonnal, az api-verziót a verziószámnak egyeznie kell az alábbi példában.
    
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
        },
    ```
    
2. (Nem kötelező) A következő a `resources` elemben adja hozzá a következő bejegyzést a felügyelt identitás bővítmény hozzárendelése a virtuális gép. Ez a lépés nem kötelező használni, mivel az Azure példány metaadat szolgáltatás (IMDS) identitás-végpont használatával, valamint a jogkivonatok. Az alábbi szintaxissal:
    ```json
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
    ```
    
3.  Amikor elkészült, a sablon az alábbihoz hasonlóan kell kinéznie:

      ![Képernyőkép a felhasználóhoz hozzárendelt identitás](./media/qs-configure-template-windows-vm/qs-configure-template-windows-vm-ua-final.PNG)

### <a name="remove-user-assigned-identity-from-an-azure-vm"></a>Távolítsa el a felhasználóhoz hozzárendelt identitás Azure virtuális gépből

Ha egy virtuális Gépet, amely már nincs szüksége a felügyeltszolgáltatás-identitás van:

1. Jelentkezzen be az Azure-bA helyileg vagy az Azure Portalon az Azure-előfizetéshez társított olyan fiókot használjon, amely tartalmazza a virtuális Gépet.

2. Betölteni a sablont, egy [szerkesztő](#azure-resource-manager-templates) , és keresse meg a `Microsoft.Compute/virtualMachines` házirendsablonokkal erőforrás a `resources` szakaszban. Ha egy virtuális Gépet, amelyhez csak a felhasználóhoz hozzárendelt identitás, letilthatja, módosítsa úgy az identitás típus `None`.  Ha a virtuális gép system és a felhasználó által hozzárendelt identitások is van, és szeretné megőrizni a rendszer által hozzárendelt identitással, távolítsa el `UserAssigned` az identitás típusból a `identityIds` a felhasználó által hozzárendelt identitások tömbje.
    
   Eltávolítja a hozzárendelt egyetlen felhasználói identitást egy virtuális gépről, távolítsa el a `identityIds` tömb.
   
   Az alábbi példa bemutatja, hogyan távolítsa el az összes felhasználói identitások hozzárendelt virtuális gép nem hozzárendelt identitások rendszert:
   
   ```JSON
    {
      "apiVersion": "2017-12-01",
      "type": "Microsoft.Compute/virtualMachines",
      "name": "[parameters('vmName')]",
      "location": "[resourceGroup().location]",
      "identity": { 
          "type": "None"
    }
   ```

## <a name="related-content"></a>Kapcsolódó tartalom

- Szélesebb perspektíva Felügyeltszolgáltatás-identitás kapcsolatban, olvassa el a [Felügyeltszolgáltatás-identitás – áttekintés](overview.md).

