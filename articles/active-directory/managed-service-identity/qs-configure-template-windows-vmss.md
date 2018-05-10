---
title: Egy Azure virtuális gépek méretezési készletben a sablon használatával MSI konfigurálása
description: Részletes útmutatást ad egy felügyelt szolgáltatás Identity (MSI) konfigurálása az Azure VMSS, Azure Resource Manager-sablonnal.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: f7c5d063bfb287de9afe808395b951ecb161da69
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2018
---
# <a name="configure-a-vmss-managed-service-identity-by-using-a-template"></a>Egy sablon használatával felügyelt VMSS szolgáltatás identitásának beállítása

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt Szolgáltatásidentitást az Azure Active Directoryban automatikusan felügyelt identitással Azure szolgáltatásokat biztosít. Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. 

Ebből a cikkből megtanulhatja a műveleteket a következő felügyelt Szolgáltatásidentitás egy Azure VMSS, Azure Resource Manager központi telepítési sablon használata:
- Engedélyezheti vagy letilthatja a rendszer egy Azure VMSS identitásának hozzárendelve
- Hozzáadhat és eltávolíthat egy felhasználó lehet hozzárendelve egy Azure VMSS identitása

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri a felügyelt Szolgáltatásidentitás, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy hozzárendelt rendszer és a felhasználói identitás](overview.md#how-does-it-work)**.
- Ha még nem telepítette az Azure-fiók [regisztráljon egy ingyenes fiókot](https://azure.microsoft.com/free/) folytatása előtt.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Csakúgy, mint az Azure portál és parancsfájl-kezelési, [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) sablonok lehetővé teszi egy Azure erőforráscsoport által meghatározott új vagy megváltozott erőforrásokat telepítését adja meg. Több lehetőség is elérhető Sablonszerkesztés és a központi telepítés, helyi és portálalapú, beleértve:

   - Használatával egy [egyéni sablont az Azure piactérről](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), amely lehetővé teszi teljesen új sablon létrehozása, vagy létrehozhatja azt egy meglévő közös vagy [gyorsindítási sablonon](https://azure.microsoft.com/documentation/templates/).
   - Egy sablon exportálása vagy egy meglévő erőforráscsoportot, a Származtatás [az eredeti telepítési](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), vagy a [az üzemelő példány aktuális állapotának](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Használja a helyi [JSON szerkesztővel (például a Visual STUDIO Code)](../../azure-resource-manager/resource-manager-create-first-template.md), majd feltöltése és a PowerShell vagy a parancssori felület segítségével történő telepítéséhez.
   - A Visual Studio használatával [Azure erőforráscsoport-projekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) létrehozása és a sablon telepítéséhez.  

Függetlenül a választott lehetőség sablon szintaxisa azonos kezdeti üzembe helyezése és újbóli üzembe helyezése során. Egy új vagy meglévő virtuális gépen az MSI engedélyezése ugyanolyan módon történik. Is, alapértelmezés szerint Azure Resource Manager elvégzi az [növekményes frissítés](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) használatával végzett központi telepítésekhez.

## <a name="system-assigned-identity"></a>A rendszer azonosító hozzárendelve

Ebben a szakaszban engedélyezze, és tiltsa le a rendszer Azure Resource Manager-sablonnal identitás hozzárendelve.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>Hozzárendelt azonosító létrehozása az Azure VMSS, vagy egy meglévő Azure VMSS rendszer engedélyezése

1. A sablon betöltése a szerkesztő, keresse meg a `Microsoft.Compute/virtualMachineScaleSets` házirendsablonokkal erőforrás a `resources` szakasz. Saját tűnhet kissé eltér attól függően, hogy a szerkesztő használata a következő képernyőfelvételen látható, és hogy szerkeszti egy sablont egy új központi telepítés vagy a meglévők egyikét.
   
   ![Képernyőkép a sablon - keresse meg a virtuális gép](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

2. A hozzárendelt rendszeridentitás engedélyezéséhez vegye fel a `"identity"` tulajdonság ugyanazon a szinten az `"type": "Microsoft.Compute/virtualMachineScaleSets"` tulajdonság. A következő szintaxissal:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

3. (Választható) Adja hozzá a virtuális gép méretezési MSI kiterjesztésű fájlt egy `extensionsProfile` elemet. Ez a lépés nem kötelező, hasonlóan az Azure példány metaadatok szolgáltatás (IMDS) identitás, valamint a jogkivonatok beolvasása.  A következő szintaxissal:

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

4. Amikor elkészült, a sablon a következő hasonlóan kell kinéznie:

   ![Képernyőkép a frissítés után sablon](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Tiltsa le az Azure virtuálisgép-méretezési csoport egy hozzárendelt rendszer identitás

> [!NOTE]
> Szolgáltatásidentitás felügyelt virtuális gépről letiltása jelenleg nem támogatott. Időközben válthat rendszer rendelve, és a felhasználó hozzárendelt identitások segítségével.

Ha egy virtuálisgép-méretezési beállítása, amely már nem rendelkezik hozzárendelt identitás rendszer kell, de továbbra is hozzá kell a felhasználói identitások:

- A sablon betöltése a szerkesztő és az identity típus a `'UserAssigned'`

## <a name="user-assigned-identity"></a>A felhasználói identitás

Ebben a szakaszban rendel egy hozzárendelt felhasználói azonosító az Azure VMSS Azure Resource Manager-sablon használatával.

> [!Note]
> Egy Azure Resource Manager-sablonnal a felhasználói identitás létrehozásához lásd: [nem hoz létre a felhasználói](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

### <a name="assign-a-user-assigned-identity-to-an-azure-vmss"></a>Egy a felhasználói identitás egy Azure VMSS hozzárendelése

1. Az a `resources` elemet, adja hozzá a következő bejegyzést a VMSS hozzárendelt felhasználói azonosítót hozzárendelni.  Ügyeljen arra, hogy a csere `<USERASSIGNEDIDENTITY>` nevű, a felhasználó identitásának létrehozott.

    ```json
    {
        "name": "[variables('vmssName')]",
        "apiVersion": "2017-03-30",
        "location": "[parameters(Location')]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/<USERASSIGNEDIDENTITY>)']"
            ]
        }

    }
    ```
2. (Választható) Adja hozzá a következő bejegyzést a `extensionProfile` a felügyelt identity bővítmény hozzárendelése a VMSS elemet. Ez a lépés nem kötelező, hasonlóan a Azure példány metaadatok szolgáltatás (IMDS) identitás végpont, valamint a jogkivonatok beolvasása. A következő szintaxissal:
   
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
3.  Amikor elkészült, a sablon a következő hasonlóan kell kinéznie:
   
      ![A felhasználói identitás képernyőképe](./media/qs-configure-template-windows-vmss/qs-configure-template-windows-final.PNG)

## <a name="next-steps"></a>További lépések

- Egy szélesebb körű perspektíva MSI kapcsolatban, olvassa el a [Szolgáltatásidentitás felügyelete – áttekintés](overview.md).

