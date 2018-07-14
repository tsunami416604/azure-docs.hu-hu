---
title: Egy felhasználó által hozzárendelt MSI konfigurálása Azure virtuális gép Azure-sablon használatával
description: Lépés útmutató egy felhasználó által hozzárendelt Felügyeltszolgáltatás-identitás (MSI) konfigurálásához egy Azure virtuális gép, egy Azure Resource Manager-sablon használatával.
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
ms.date: 12/22/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 1af9770fa899a03e6e3514c539c511ba26fdced4
ms.sourcegitcommit: 04fc1781fe897ed1c21765865b73f941287e222f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/13/2018
ms.locfileid: "39036380"
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-template"></a>Egy felhasználó által hozzárendelt Felügyeltszolgáltatás-identitás (MSI) konfigurálja a virtuális gépek, Azure-sablon használatával

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyeltszolgáltatás-identitás egy felügyelt identitás, az Azure Active Directory Azure-szolgáltatásokat biztosít. Ez az identitás használatával, amelyek támogatják az Azure AD-hitelesítés, anélkül, hogy hitelesítő adatok a kód a szolgáltatásokhoz való hitelesítéséhez. 

Ebből a cikkből megismerheti, hogyan engedélyezheti és a egy felhasználó által hozzárendelt MSI eltávolítása egy Azure virtuális gép, egy Azure Resource Manager üzembe helyezési sablon használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Az MSI engedélyezéséhez egy Azure virtuális Gépen, vagy pedig egy meglévő virtuális gép létrehozása során

Ahogy az az Azure portal és a parancsfájlok, az Azure Resource Manager-sablonok lehetővé teszi, hogy üzembe helyezése az Azure-erőforráscsoport által meghatározott új vagy megváltozott erőforrásokat. Több lehetőség is elérhető, és a helyi és portálalapú, beleértve a központi telepítési sablon Szerkesztés:

   - Használatával egy [egyéni sablont az Azure Marketplace-ről](~/articles/azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), amely lehetővé teszi, hogy a sablon létrehozása az alapoktól, vagy egy meglévő közös alapul vagy [gyorsindítási sablon](https://azure.microsoft.com/documentation/templates/).
   - Sablon exportálása vagy egy meglévő erőforráscsoportot, a Származtatás [az eredeti üzembe helyezés](~/articles/azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), vagy a [az üzemelő példány aktuális állapotát](~/articles/azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Egy helyi [JSON-szerkesztővel (például a VS Code)](~/articles/azure-resource-manager/resource-manager-create-first-template.md), majd feltöltését és üzembe helyezése a PowerShell vagy parancssori felület használatával.
   - A Visual Studio használatával [Azure erőforráscsoport-projekt](~/articles/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) hozzon létre és helyezhet üzembe sablont is.  

A lehetőséget választja, függetlenül a sablon szintaxisa megegyezik kezdeti üzembe helyezése és újbóli üzembe helyezés során. Létrehozása és hozzárendelése egy felhasználó által hozzárendelt MSI egy új vagy meglévő virtuális géphez azonos módon történik. Emellett, alapértelmezés szerint az Azure Resource Manager elvégzi az [növekményes frissítés](~/articles/azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) – üzembe helyezéséhez:

1. E jelentkezik be az Azure-bA helyileg vagy az Azure Portalon, az Azure-előfizetést, amely tartalmazza az MSI és a virtuális Géphez tartozó fiókot kell használnia. Emellett győződjön meg arról, hogy a fiók tartozik egy szerepkör, amely lehetővé teszi az előfizetés vagy az erőforrások (például "owner" szerepkör) írási engedéllyel.

2. A sablon betöltése be egy szerkesztőt, után keresse meg a `Microsoft.Compute/virtualMachines` házirendsablonokkal erőforrás a `resources` szakaszban. Öné kissé eltérhetnek az alábbi képernyőfelvételen látható, a szerkesztő használata függően előfordulhat, hogy keresse meg, és hogy szerkeszti egy sablont egy új központi telepítést vagy a meglévőt.

   >[!NOTE] 
   > Ez a példa feltételezi, hogy változók például `vmName`, `storageAccountName`, és `nicName` van definiálva a sablonban.
   >

   ![Képernyőkép a sablonhoz: keresse meg a virtuális gép](../managed-service-identity/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Adja hozzá a `"identity"` tulajdonság azonos szinten, a `"type": "Microsoft.Compute/virtualMachines"` tulajdonság. Az alábbi szintaxissal:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Adja hozzá a virtuális gép MSI-bővítményt, mint egy `resources` elemet. Az alábbi szintaxissal:

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

## <a name="remove-msi-from-an-azure-vm"></a>Egy Azure virtuális gép MSI eltávolítása

Ha egy virtuális Gépet, amely már nincs szüksége egy MSI-csomag:

1. Jelentkezzen be az Azure-bA helyileg vagy az Azure Portalon az Azure-előfizetéshez társított olyan fiókot használjon, amely tartalmazza a virtuális Gépet. Emellett győződjön meg arról, hogy a fiók tartozik egy szerepkör, amely lehetővé teszi a virtuális gép (például "Virtuális gép közreműködő" szerepkör) írási engedéllyel.

2. Távolítsa el a két elemet az előző szakaszban hozzáadott: a virtuális gép `"identity"` tulajdonság és a `"Microsoft.Compute/virtualMachines/extensions"` erőforrás.

## <a name="related-content"></a>Kapcsolódó tartalom

- Szélesebb perspektíva MSI kapcsolatban, olvassa el a [Felügyeltszolgáltatás-identitás – áttekintés](msi-overview.md).

