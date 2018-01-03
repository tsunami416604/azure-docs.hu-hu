---
title: "A felhasználó által hozzárendelt MSI konfigurálása az Azure virtuális gép Azure-sablon alapján"
description: "Lépés lépés útmutatást, a felhasználó által hozzárendelt felügyelt szolgáltatás Identity (MSI) konfigurálása az Azure virtuális gép, egy Azure Resource Manager-sablon használatával."
services: active-directory
documentationcenter: 
author: bryanla
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/22/2017
ms.author: bryanla
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: d97f0fa2d6c1c92aaa3d5c74dd6715de00d32438
ms.sourcegitcommit: a648f9d7a502bfbab4cd89c9e25aa03d1a0c412b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/22/2017
---
# <a name="configure-a-user-assigned-managed-service-identity-msi-for-a-vm-using-an-azure-template"></a>A felhasználó által hozzárendelt felügyelt szolgáltatás Identity (MSI) konfigurálja a virtuális gépek Azure-sablon alapján

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

Felügyelt Szolgáltatásidentitás az Azure Active Directory-identitással felügyelt Azure-szolgáltatásokhoz biztosít. Ezzel az identitással használhatja az Azure AD-alapú hitelesítés, anélkül, hogy hitelesítő adatok a kódban támogató szolgáltatások felé történő hitelesítésre. 

Ebből a cikkből megismerheti, hogyan engedélyezheti, és távolítsa el a felhasználó által hozzárendelt MSI egy Azure virtuális gép, egy Azure Resource Manager központi telepítési sablon használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Egy Azure virtuális Gépen, vagy egy meglévő virtuális gép létrehozásakor MSI engedélyezése

Az Azure portál és parancsfájl-kezelési, Azure Resource Manager-sablonok telepíthet egy Azure erőforráscsoport által meghatározott új vagy megváltozott erőforrásokat adjon meg. Több lehetőség is elérhető Sablonszerkesztés és a központi telepítés, helyi és portálalapú, beleértve:

   - Használatával egy [egyéni sablont az Azure piactérről](~/articles/azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), amely lehetővé teszi teljesen új sablon létrehozása, vagy létrehozhatja azt egy meglévő közös vagy [gyorsindítási sablonon](https://azure.microsoft.com/documentation/templates/).
   - Egy sablon exportálása vagy egy meglévő erőforráscsoportot, a Származtatás [az eredeti telepítési](~/articles/azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), vagy a [az üzemelő példány aktuális állapotának](~/articles/azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Használja a helyi [JSON szerkesztővel (például a Visual STUDIO Code)](~/articles/azure-resource-manager/resource-manager-create-first-template.md), majd feltöltése és a PowerShell vagy a parancssori felület segítségével történő telepítéséhez.
   - A Visual Studio használatával [Azure erőforráscsoport-projekt](~/articles/azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) létrehozása és a sablon telepítéséhez.  

Függetlenül a választott lehetőség sablon szintaxisa azonos kezdeti üzembe helyezése és újbóli üzembe helyezése során. Hoz létre, és egy felhasználó által hozzárendelt MSI egy új vagy meglévő virtuális géppel megegyező módon történik. Is, alapértelmezés szerint Azure Resource Manager elvégzi az [növekményes frissítés](~/articles/azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) telepítése esetén:

1. Jelentkezzen be helyileg Azure vagy az Azure-portál, az Azure-előfizetéssel társított fiókot használ, amely tartalmazza az MSI-fájl és a virtuális gép. Emellett győződjön meg arról, hogy a fiókja tagja-e egy szerepkör, amely lehetővé teszi az írási engedéllyel az előfizetés vagy az erőforrások (például a "tulajdonos" szerepe).

2. A sablon betöltése a szerkesztő, után keresse meg a `Microsoft.Compute/virtualMachines` házirendsablonokkal erőforrás a `resources` szakasz. Saját tűnhet kissé eltér attól függően, hogy a szerkesztő használata a következő képernyőfelvételen látható, és hogy szerkeszti egy sablont egy új központi telepítés vagy a meglévők egyikét.

   >[!NOTE] 
   > Ebben a példában feltételezzük például a változók `vmName`, `storageAccountName`, és `nicName` a sablonban definiált.
   >

   ![Képernyőkép a sablon - keresse meg a virtuális gép](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Adja hozzá a `"identity"` tulajdonság ugyanazon a szinten az `"type": "Microsoft.Compute/virtualMachines"` tulajdonság. A következő szintaxissal:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Majd adja hozzá a virtuális gép MSI kiterjesztést, mint egy `resources` elemet. A következő szintaxissal:

   >[!NOTE] 
   > Az alábbi példa azt feltételezi, hogy a Windows Virtuálisgép-bővítmény (`ManagedIdentityExtensionForWindows`) telepítése történik. Beállíthatja úgy is Linux használatával `ManagedIdentityExtensionForLinux` ehelyett a `"name"` és `"type"` elemeket.
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

5. Amikor elkészült, a sablon a következő hasonlóan kell kinéznie:

   ![Képernyőkép a frissítés után sablon](~/articles/active-directory/media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Az Azure virtuális gép MSI eltávolítása

Ha egy virtuális Gépet, amely már nincs szüksége egy olyan MSI Csomaghoz:

1. Jelentkezzen be helyileg Azure vagy az Azure-portál, az Azure-előfizetéssel társított fiókot használ, amely a virtuális Gépet tartalmaz. Emellett győződjön meg arról, hogy a fiókja tagja-e egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális gép (például "Virtuális gép közreműködő" szerepkör).

2. Távolítsa el a két elem az előző szakaszban hozzáadott: a virtuális gép `"identity"` tulajdonság és a `"Microsoft.Compute/virtualMachines/extensions"` erőforrás.

## <a name="related-content"></a>Kapcsolódó tartalom

- Egy szélesebb körű perspektíva MSI kapcsolatban, olvassa el a [Szolgáltatásidentitás felügyelete – áttekintés](msi-overview.md).

