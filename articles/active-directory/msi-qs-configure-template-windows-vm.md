---
title: "MSI konfigurálása az Azure virtuális gép egy sablon használatával"
description: "Részletes útmutatást ad a kezelt Service Identity (MSI) konfigurálása egy Azure virtuális gépen, Azure Resource Manager-sablonnal."
services: active-directory
documentationcenter: 
author: bryanla
manager: mbaldwin
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 09/14/2017
ms.author: bryanla
ms.openlocfilehash: 3a81ab859bea5bfe53c7f761e0b40ad481c4c41a
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Egy sablon használatával egy virtuális gép felügyelt Szolgáltatásidentitás konfigurálása

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Felügyelt szolgáltatás identitásának (MSI) az Azure Active Directory (Azure AD) automatikusan felügyelt identitással Azure-szolgáltatásokhoz biztosít. Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. 

Ebből a cikkből megismerheti, hogyan engedélyezheti és MSI eltávolítása egy Azure virtuális gép, egy Azure Resource Manager központi telepítési sablon használatával.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../includes/msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Egy Azure virtuális Gépen, vagy egy meglévő virtuális gép létrehozásakor MSI engedélyezése

Az Azure portál és parancsfájl-kezelési, Azure Resource Manager-sablonok telepíthet egy Azure erőforráscsoport által meghatározott új vagy megváltozott erőforrásokat adjon meg. Több lehetőség is elérhető Sablonszerkesztés és a központi telepítés, helyi és portálalapú, beleértve:

   - Használatával egy [egyéni sablont az Azure piactérről](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), amely lehetővé teszi teljesen új sablon létrehozása, vagy létrehozhatja azt egy meglévő közös vagy [gyorsindítási sablonon](https://azure.microsoft.com/documentation/templates/).
   - Egy sablon exportálása vagy egy meglévő erőforráscsoportot, a Származtatás [az eredeti telepítési](../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), vagy a [az üzemelő példány aktuális állapotának](../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Használja a helyi [JSON szerkesztővel (például a Visual STUDIO Code)](../azure-resource-manager/resource-manager-create-first-template.md), majd feltöltése és a PowerShell vagy a parancssori felület segítségével történő telepítéséhez.
   - A Visual Studio használatával [Azure erőforráscsoport-projekt](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) létrehozása és a sablon telepítéséhez.  

Függetlenül a választott lehetőség sablon szintaxisa azonos kezdeti üzembe helyezése és újbóli üzembe helyezése során. Egy új vagy meglévő virtuális gépen az MSI engedélyezése ugyanolyan módon történik. Is, alapértelmezés szerint Azure Resource Manager elvégzi az [növekményes frissítés](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) telepítése esetén:

1. Jelentkezzen be helyileg Azure vagy az Azure-portál, az Azure-előfizetéssel társított fiókot használ, amely a virtuális Gépet tartalmaz. Emellett győződjön meg arról, hogy a fiókja tagja-e egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális gép (például "Virtuális gép közreműködő" szerepkör).

2. A sablon betöltése a szerkesztő, után keresse meg a `Microsoft.Compute/virtualMachines` házirendsablonokkal erőforrás a `resources` szakasz. Saját tűnhet kissé eltér attól függően, hogy a szerkesztő használata a következő képernyőfelvételen látható, és hogy szerkeszti egy sablont egy új központi telepítés vagy a meglévők egyikét.

   >[!NOTE] 
   > Ebben a példában feltételezzük például a változók `vmName`, `storageAccountName`, és `nicName` a sablonban definiált.
   >

   ![Képernyőkép a sablon - keresse meg a virtuális gép](./media/msi-qs-configure-template-windows-vm/template-file-before.png) 

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

   ![Képernyőkép a frissítés után sablon](./media/msi-qs-configure-template-windows-vm/template-file-after.png) 

## <a name="remove-msi-from-an-azure-vm"></a>Az Azure virtuális gép MSI eltávolítása

Ha egy virtuális Gépet, amely már nincs szüksége egy olyan MSI Csomaghoz:

1. Jelentkezzen be helyileg Azure vagy az Azure-portál, az Azure-előfizetéssel társított fiókot használ, amely a virtuális Gépet tartalmaz. Emellett győződjön meg arról, hogy a fiókja tagja-e egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális gép (például "Virtuális gép közreműködő" szerepkör).

2. Távolítsa el a két elem az előző szakaszban hozzáadott: a virtuális gép `"identity"` tulajdonság és a `"Microsoft.Compute/virtualMachines/extensions"` erőforrás.

## <a name="related-content"></a>Kapcsolódó tartalom

- Egy szélesebb körű perspektíva MSI kapcsolatban, olvassa el a [Szolgáltatásidentitás felügyelete – áttekintés](msi-overview.md).

