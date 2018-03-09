---
title: "Egy Azure virtuális gépek méretezési készletben a sablon használatával MSI konfigurálása"
description: "Részletes útmutatást ad egy felügyelt szolgáltatás Identity (MSI) konfigurálása az Azure VMSS, Azure Resource Manager-sablonnal."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 669348213e00cb338c69156f37779767c681a227
ms.sourcegitcommit: 0b02e180f02ca3acbfb2f91ca3e36989df0f2d9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2018
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Egy sablon használatával egy virtuális gép felügyelt Szolgáltatásidentitás konfigurálása

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

Felügyelt szolgáltatás identitásának (MSI) az Azure Active Directory (Azure AD) automatikusan felügyelt identitással Azure-szolgáltatásokhoz biztosít. Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. 

Ebből a cikkből megismerheti, hogyan engedélyezése és MSI távolítani egy Azure virtuálisgép-méretezési csoport az Azure Resource Manager-központi telepítés sablonnal.

## <a name="prerequisites"></a>Előfeltételek

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

## <a name="enable-msi-during-creation-of-an-azure-virtual-machine-scale-set-or-an-existing-azure-virtual-machine-scale-set"></a>Egy Azure virtuálisgép-méretezési csoport, vagy egy meglévő Azure virtuálisgép-méretezési csoport létrehozásakor MSI engedélyezése

Az Azure portál és parancsfájl-kezelési, Azure Resource Manager-sablonok telepíthet egy Azure erőforráscsoport által meghatározott új vagy megváltozott erőforrásokat adjon meg. Több lehetőség is elérhető Sablonszerkesztés és a központi telepítés, helyi és portálalapú, beleértve:

   - Használatával egy [egyéni sablont az Azure piactérről](../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), amely lehetővé teszi teljesen új sablon létrehozása, vagy létrehozhatja azt egy meglévő közös vagy [gyorsindítási sablonon](https://azure.microsoft.com/documentation/templates/).
   - Egy sablon exportálása vagy egy meglévő erőforráscsoportot, a Származtatás [az eredeti telepítési](../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), vagy a [az üzemelő példány aktuális állapotának](../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Használja a helyi [JSON szerkesztővel (például a Visual STUDIO Code)](../azure-resource-manager/resource-manager-create-first-template.md), majd feltöltése és a PowerShell vagy a parancssori felület segítségével történő telepítéséhez.
   - A Visual Studio használatával [Azure erőforráscsoport-projekt](../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) létrehozása és a sablon telepítéséhez.  

Függetlenül a választott lehetőség sablon szintaxisa azonos kezdeti üzembe helyezése és újbóli üzembe helyezése során. MSI engedélyezését egy új vagy meglévő Azure virtuálisgép-méretezési csoport azonos módon történik. Is, alapértelmezés szerint Azure Resource Manager elvégzi az [növekményes frissítés](../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) telepítése esetén:

1. Bejelentkezik az Azure-bA helyileg vagy az Azure-portálon, hogy olyan fiókot használjon, amely társított Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoport.

2. A sablon betöltése a szerkesztő, után keresse meg a `Microsoft.Compute/virtualMachineScaleSets` házirendsablonokkal erőforrás a `resources` szakasz. Saját tűnhet kissé eltér attól függően, hogy a szerkesztő használata a következő képernyőfelvételen látható, és hogy szerkeszti egy sablont egy új központi telepítés vagy a meglévők egyikét.
   
   ![Képernyőkép a sablon - keresse meg a virtuális gép](./media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

3. Adja hozzá a `"identity"` tulajdonság ugyanazon a szinten az `"type": "Microsoft.Compute/virtualMachineScaleSets"` tulajdonság. A következő szintaxissal:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. Adja meg a virtuális gép méretezési MSI kiterjesztésű fájlt egy `extensionsProfile` elemet. A következő szintaxissal:

   >[!NOTE] 
   > Az alábbi példa azt feltételezi, hogy a Windows virtuálisgép-méretezési virtuáliskapcsoló-kiterjesztés beállítása (`ManagedIdentityExtensionForWindows`) telepítése történik. Beállíthatja úgy is Linux használatával `ManagedIdentityExtensionForLinux` ehelyett a `"name"` és `"type"` elemeket.
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

5. Amikor elkészült, a sablon a következő hasonlóan kell kinéznie:

   ![Képernyőkép a frissítés után sablon](./media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

## <a name="remove-msi-from-an-azure-virtual-machine-scale-set"></a>Egy Azure virtuálisgép-méretezési csoport MSI eltávolítása

Ha egy virtuálisgép-méretezési csoport, amely már nem kell egy olyan MSI Csomaghoz:

1. Bejelentkezik az Azure-bA helyileg vagy az Azure-portálon, hogy olyan fiókot használjon, amely társított Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoport.

2. Távolítsa el a két elem az előző szakaszban hozzáadott: a virtuális gép méretezési `"identity"` és `"extensionsProfile"` tulajdonságok.

## <a name="next-steps"></a>További lépések

- Egy szélesebb körű perspektíva MSI kapcsolatban, olvassa el a [Szolgáltatásidentitás felügyelete – áttekintés](msi-overview.md).

