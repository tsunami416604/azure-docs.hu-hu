---
title: Egy Azure virtuális gépek méretezési készletben a sablon használatával MSI konfigurálása
description: Részletes útmutatást ad egy felügyelt szolgáltatás Identity (MSI) konfigurálása az Azure VMSS, Azure Resource Manager-sablonnal.
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
ms.date: 02/20/2018
ms.author: daveba
ms.openlocfilehash: 64fe217cf3d845e6a09fe67d03648e79e8a4cadd
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
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

Az Azure portál és parancsfájl-kezelési, Azure Resource Manager-sablonok telepíthet egy Azure erőforráscsoport által meghatározott új vagy megváltozott erőforrásokat adjon meg. Több lehetőség is elérhető Sablonszerkesztés és a központi telepítés, helyi és portálalapú, beleértve:

   - Használatával egy [egyéni sablont az Azure piactérről](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), amely lehetővé teszi teljesen új sablon létrehozása, vagy létrehozhatja azt egy meglévő közös vagy [gyorsindítási sablonon](https://azure.microsoft.com/documentation/templates/).
   - Egy sablon exportálása vagy egy meglévő erőforráscsoportot, a Származtatás [az eredeti telepítési](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), vagy a [az üzemelő példány aktuális állapotának](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Használja a helyi [JSON szerkesztővel (például a Visual STUDIO Code)](../../azure-resource-manager/resource-manager-create-first-template.md), majd feltöltése és a PowerShell vagy a parancssori felület segítségével történő telepítéséhez.
   - A Visual Studio használatával [Azure erőforráscsoport-projekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) létrehozása és a sablon telepítéséhez.  

Függetlenül a választott lehetőség sablon szintaxisa azonos kezdeti üzembe helyezése és újbóli üzembe helyezése során. Egy új vagy meglévő virtuális gépen az MSI engedélyezése ugyanolyan módon történik. Is, alapértelmezés szerint Azure Resource Manager elvégzi az [növekményes frissítés](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) használatával végzett központi telepítésekhez.

## <a name="system-assigned-identity"></a>A rendszer azonosító hozzárendelve

Ebben a szakaszban engedélyezze, és tiltsa le a rendszer Azure Resource Manager-sablonnal identitás hozzárendelve.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss"></a>Hozzárendelt azonosító létrehozása az Azure VMSS, vagy egy meglévő Azure VMSS rendszer engedélyezése

1. Bejelentkezik az Azure-bA helyileg vagy az Azure-portálon, hogy olyan fiókot használjon, amely társított Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoport.

2. A sablon betöltése a szerkesztő, után keresse meg a `Microsoft.Compute/virtualMachineScaleSets` házirendsablonokkal erőforrás a `resources` szakasz. Saját tűnhet kissé eltér attól függően, hogy a szerkesztő használata a következő képernyőfelvételen látható, és hogy szerkeszti egy sablont egy új központi telepítés vagy a meglévők egyikét.
   
   ![Képernyőkép a sablon - keresse meg a virtuális gép](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-before-vmss.png) 

3. A hozzárendelt rendszeridentitás engedélyezéséhez vegye fel a `"identity"` tulajdonság ugyanazon a szinten az `"type": "Microsoft.Compute/virtualMachineScaleSets"` tulajdonság. A következő szintaxissal:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. (Választható) Adja hozzá a virtuális gép méretezési MSI kiterjesztésű fájlt egy `extensionsProfile` elemet. Ez a lépés nem kötelező, hasonlóan az Azure példány metaadatok szolgáltatás (IMDS) identitás, valamint a jogkivonatok beolvasása.  A következő szintaxissal:

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

   ![Képernyőkép a frissítés után sablon](../media/msi-qs-configure-template-windows-vmss/msi-arm-template-file-after-vmss.png) 

### <a name="disable-a-system-assigned-identity-from-an-azure-virtual-machine-scale-set"></a>Tiltsa le az Azure virtuálisgép-méretezési csoport egy hozzárendelt rendszer identitás

> [!NOTE]
> Szolgáltatásidentitás felügyelt virtuális gépről letiltása jelenleg nem támogatott. Időközben válthat rendszer rendelve, és a felhasználó hozzárendelt identitások segítségével.

Ha egy virtuálisgép-méretezési beállítása, amely már nem rendelkezik hozzárendelt identitás rendszer kell, de továbbra is hozzá kell a felhasználói identitások:

1. Bejelentkezik az Azure-bA helyileg vagy az Azure-portálon, hogy olyan fiókot használjon, amely társított Azure-előfizetést, amely tartalmazza a virtuálisgép-méretezési csoport.

2. Az identity típus a `'UserAssigned'`

## <a name="user-assigned-identity"></a>A felhasználói identitás

Ebben a szakaszban egy felhasználó lehet hozzárendelve az identitás- és egy Azure VMSS Azure Resource Manager-sablonnal hoz létre.

### <a name="create-and-assign-a-user-assigned-identity-to-an-azure-vmss"></a>Létrehozni és hozzárendelni egy a felhasználói identitás egy Azure VMSS

1. A szakaszban a lépés végrehajtása az első [engedélyezése a hozzárendelt rendszeridentitás létrehozása az Azure VMSS, egy meglévő VMSS a](qs-configure-template-windows-vmss.md#enable-system-assigned-identity-during-creation-of-an-azure-vmss-or-an-existing-azure-vmss).

2. A változók szakaszban, amely tartalmazza a konfigurációs változók a Azure VMSS adjon hozzá egy bejegyzést, az alábbihoz hasonló felhasználói hozzárendelt azonosító nevet.  Ez a felhasználó identitását az Azure VMSS létrehozási folyamata során értéke:
    
    > [!IMPORTANT]
    > A felhasználói identitások a különleges karakterek (pl. aláhúzásjel) nevében létrehozása jelenleg nem támogatott. Alfanumerikus karaktereket használja. Biztonsági frissítések ellenőrzése.  További információ: [– gyakori kérdések és ismert problémák](known-issues.md)

    ```json
    "variables": {
        "vmssPrefix": "vmss",
        "vmssName": "[concat(variables('vmssPrefix'), uniquestring(resourceGroup().id,deployment().name))]",
        //other vm configuration variables...
        "identityName": "[concat(variables('vmssName'), 'id')]"
    ```
3. Az a `resources` elem hozzáadása a következő bejegyzést hoz létre egy felhasználó lehet hozzárendelve:

    ```json
    {
        "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
        "name": "[variables('identityName')]",
        "apiVersion": "2015-08-31-PREVIEW",
        "location": "[resourceGroup().location]"
    },
    ```
4. Ezután bontsa a `resources` elem hozzáadása a felhasználói identitás hozzárendelése a VMSS a következő bejegyzést:

    ```json
    {
        "name": "[variables('vmssName')]",
        "apiVersion": "2017-03-30",
        "location": "[parameters(Location')]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/, variables('identityName'))]"
            ]
        }

    }
    ```
5. (Választható) Adja hozzá a következő bejegyzést a `extensionProfile` a felügyelt identity bővítmény hozzárendelése a VMSS elemet. Ez a lépés nem kötelező, hasonlóan a Azure példány metaadatok szolgáltatás (IMDS) identitás végpont, valamint a jogkivonatok beolvasása. A következő szintaxissal:
   
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
6.  Amikor elkészült, a sablon a következő hasonlóan kell kinéznie:
    > [!NOTE]
    > A sablon nem listázza az összes szükséges változó a VMSS létrehozásához.  `//other configuration variables...` helyett kivonatosan mutatja az összes szükséges konfigurációs változót használja.

      ![A felhasználói identitás képernyőképe](../media/msi-qs-configure-template-windows-vmss/template-vmss-user-assigned-identity.png)

## <a name="next-steps"></a>További lépések

- Egy szélesebb körű perspektíva MSI kapcsolatban, olvassa el a [Szolgáltatásidentitás felügyelete – áttekintés](overview.md).

