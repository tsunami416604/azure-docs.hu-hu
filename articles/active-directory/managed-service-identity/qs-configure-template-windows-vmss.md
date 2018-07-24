---
title: Az MSI konfigurálása az Azure virtuálisgép-méretezési csoport, egy sablon használatával
description: Részletes útmutató a Felügyeltszolgáltatás-identitás (MSI) konfigurálása az Azure VMSS, egy Azure Resource Manager-sablon használatával.
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
ms.openlocfilehash: babeb0eb930d865cf519ddb45c651a3d77265665
ms.sourcegitcommit: 248c2a76b0ab8c3b883326422e33c61bd2735c6c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2018
ms.locfileid: "39215793"
---
# <a name="configure-a-vmss-managed-service-identity-by-using-a-template"></a>A sablonok segítségével a VMSS Felügyeltszolgáltatás-identitás konfigurálása

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyeltszolgáltatás-identitás az Azure-szolgáltatásokat az Azure Active Directoryban automatikusan felügyelt identitást biztosít. Használhatja ezt az identitást, amely támogatja az Azure AD-hitelesítés, a kód a hitelesítő adatok nélkül bármely szolgáltatással való hitelesítésre. 

Ez a cikk ismerteti az Azure VMSS, Azure Resource Manager üzembe helyezési sablon használatával Felügyeltszolgáltatás-identitást a következő műveletek végrehajtásához:
- Engedélyezheti és tilthatja le a rendszer az Azure VMSS identitásának hozzárendelve
- Hozzáadhat és eltávolíthat az Azure VMSS identitásának hozzárendelt felhasználó

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

A lehetőséget választja, függetlenül a sablon szintaxisa megegyezik kezdeti üzembe helyezése és újbóli üzembe helyezés során. Egy új vagy meglévő virtuális gépen az MSI engedélyezéséhez azonos módon történik. Emellett, alapértelmezés szerint az Azure Resource Manager elvégzi az [növekményes frissítés](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) üzemelő példányokhoz.

## <a name="system-assigned-identity"></a>Rendszerhez rendelt identitáshoz

Ebben a szakaszban engedélyezze, majd tiltsa le a rendszer hozzárendelt identitás egy Azure Resource Manager-sablon használatával.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>Hozzárendelt identitás létrehozása az Azure VMSS, vagy egy meglévő Azure VMSS rendszer engedélyezése

1. A sablon betöltése a szerkesztő, keresse meg a `Microsoft.Compute/virtualMachineScaleSets` házirendsablonokkal erőforrás a `resources` szakaszban. Öné kissé eltérhetnek az alábbi képernyőfelvételen látható, a szerkesztő használata függően előfordulhat, hogy keresse meg, és hogy szerkeszti egy sablont egy új központi telepítést vagy a meglévőt.
   
   ![Képernyőkép a sablonhoz: keresse meg a virtuális gép](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. A rendszer által hozzárendelt identitással engedélyezéséhez vegye fel a `"identity"` tulajdonság azonos szinten, a `"type": "Microsoft.Compute/virtualMachineScaleSets"` tulajdonság. Az alábbi szintaxissal:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. (Nem kötelező) Adja hozzá a virtuális gép méretezési csoport MSI kiterjesztésű fájlt egy `extensionsProfile` elemet. Ez a lépés nem kötelező használni, mivel az Azure példány metaadat szolgáltatás (IMDS) identitás használatával, valamint a jogkivonatok.  Az alábbi szintaxissal:

   >[!NOTE] 
   > Az alábbi példa azt feltételezi, hogy egy Windows virtuális gép méretezési csoport bővítményének (`ManagedIdentityExtensionForWindows`) lesz üzembe helyezve. Beállíthatja a Linux használatával `ManagedIdentityExtensionForLinux` ehelyett a `"name"` és `"type"` elemeket.
   >

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

4. Ha elkészült, a sablon az alábbihoz hasonlóan kell kinéznie:

   ![Képernyőkép a frissítés után a sablon](../managed-service-identity/media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Tiltsa le az Azure-beli virtuálisgép-méretezési csoportot, a rendszer által hozzárendelt identitással

> [!NOTE]
> A Felügyeltszolgáltatás-identitást a virtuális gépről letiltása jelenleg nem támogatott. Addig is válthat a rendszer által hozzárendelt, és a felhasználó hozzárendelt identitások között.

Ha egy virtuálisgép-méretezési csoportban, már nem kell a rendszerhez rendelt identitáshoz, de továbbra is szüksége van a felhasználó által hozzárendelt identitások:

- A sablon betöltése az egy szövegszerkesztőben, és módosítsa az azonosító típusát `'UserAssigned'`

## <a name="user-assigned-identity"></a>A felhasználóhoz hozzárendelt identitás

Ebben a szakaszban rendel egy felhasználóhoz hozzárendelt identitás az Azure VMSS Azure Resource Manager-sablon használatával.

> [!Note]
> Egy Azure Resource Manager-sablon használatával a felhasználóhoz hozzárendelt identitás létrehozása: [felhasználóhoz hozzárendelt identitás létrehozása](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Rendelje hozzá egy a felhasználóhoz hozzárendelt identitás az Azure vmss-hez

1. Alatt a `resources` elemben adja hozzá a következő bejegyzés hozzárendelni egy felhasználóhoz hozzárendelt identitás a vmss-hez.  Ne felejtse el `<USERASSIGNEDIDENTITY>` létrehozta a felhasználóhoz hozzárendelt identitás nevére.

   > [!Important]
   > A `<USERASSIGNEDIDENTITYNAME>` érték a következő példa egy változóban kell tárolni.  Emellett a jelenleg támogatott megvalósítás, felhasználó által hozzárendelt identitások hozzárendelése egy virtuális géphez a Resource Manager-sablonnal, az api-verziót a verziószámnak egyeznie kell az alábbi példában. 

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
3.  Amikor elkészült, a sablon az alábbihoz hasonlóan kell kinéznie:
   
      ![Képernyőkép a felhasználóhoz hozzárendelt identitás](./media/qs-configure-template-windows-vmss/qs-configure-template-windows-final.PNG)

## <a name="next-steps"></a>További lépések

- Szélesebb perspektíva MSI kapcsolatban, olvassa el a [Felügyeltszolgáltatás-identitás – áttekintés](overview.md).

