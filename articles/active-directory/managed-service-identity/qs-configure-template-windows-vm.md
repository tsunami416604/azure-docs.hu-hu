---
title: MSI konfigurálása az Azure virtuális gép egy sablon használatával
description: Részletes útmutatást ad a kezelt Service Identity (MSI) konfigurálása egy Azure virtuális gépen, Azure Resource Manager-sablonnal.
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
ms.date: 09/14/2017
ms.author: daveba
ms.openlocfilehash: 324a1e08e92a2c7ae76d7a6df56536540dc772a1
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/04/2018
---
# <a name="configure-a-vm-managed-service-identity-by-using-a-template"></a>Egy sablon használatával egy virtuális gép felügyelt Szolgáltatásidentitás konfigurálása

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Felügyelt Szolgáltatásidentitást az Azure Active Directoryban automatikusan felügyelt identitással Azure szolgáltatásokat biztosít. Ez az identitás, amely támogatja az Azure AD-alapú hitelesítés, anélkül, hogy a hitelesítő adatokat a kódban a szolgáltatással való hitelesítésre szolgáló használhatja. 

Ebből a cikkből megtanulhatja a műveleteket a következő felügyelt Szolgáltatásidentitás egy Azure virtuális gépet az Azure Resource Manager központi telepítési sablont:

## <a name="prerequisites"></a>Előfeltételek

- Ha nem ismeri a felügyelt Szolgáltatásidentitás, tekintse meg a [áttekintés szakaszban](overview.md). **Ne feledje el áttekinteni a [különbség egy hozzárendelt rendszer és a felhasználói identitás](overview.md#how-does-it-work)**.
- Ha még nem telepítette az Azure-fiók [regisztráljon egy ingyenes fiókot](https://azure.microsoft.com/free/) folytatása előtt.

## <a name="azure-resource-manager-templates"></a>Azure Resource Manager-sablonok

Csakúgy, mint az Azure portál és parancsfájl-kezelési, [Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md) sablonok lehetővé teszi egy Azure erőforráscsoport által meghatározott új vagy megváltozott erőforrásokat telepítését adja meg. Több lehetőség is elérhető Sablonszerkesztés és a központi telepítés, helyi és portálalapú, beleértve:

   - Használatával egy [egyéni sablont az Azure piactérről](../../azure-resource-manager/resource-group-template-deploy-portal.md#deploy-resources-from-custom-template), amely lehetővé teszi teljesen új sablon létrehozása, vagy létrehozhatja azt egy meglévő közös vagy [gyorsindítási sablonon](https://azure.microsoft.com/documentation/templates/).
   - Egy sablon exportálása vagy egy meglévő erőforráscsoportot, a Származtatás [az eredeti telepítési](../../azure-resource-manager/resource-manager-export-template.md#view-template-from-deployment-history), vagy a [az üzemelő példány aktuális állapotának](../../azure-resource-manager/resource-manager-export-template.md#export-the-template-from-resource-group).
   - Használja a helyi [JSON szerkesztővel (például a Visual STUDIO Code)](../../azure-resource-manager/resource-manager-create-first-template.md), majd feltöltése és a PowerShell vagy a parancssori felület segítségével történő telepítéséhez.
   - A Visual Studio használatával [Azure erőforráscsoport-projekt](../../azure-resource-manager/vs-azure-tools-resource-groups-deployment-projects-create-deploy.md) létrehozása és a sablon telepítéséhez.  

Függetlenül a választott lehetőség sablon szintaxisa azonos kezdeti üzembe helyezése és újbóli üzembe helyezése során. Engedélyezi a rendszer vagy a felhasználó identitását egy új vagy meglévő virtuális gépen azonos módon történik. Is, alapértelmezés szerint Azure Resource Manager elvégzi az [növekményes frissítés](../../azure-resource-manager/resource-group-template-deploy.md#incremental-and-complete-deployments) használatával végzett központi telepítésekhez.

## <a name="system-assigned-identity"></a>A rendszer azonosító hozzárendelve

Ebben a szakaszban engedélyezze, és tiltsa le a rendszer Azure Resource Manager-sablonnal identitás rendelt.

### <a name="enable-system-assigned-identity-during-creation-of-an-azure-vm-or-on-an-existing-vm"></a>Identitás hozzárendelve egy Azure virtuális Gépen, vagy egy meglévő virtuális gép létrehozásakor rendszer engedélyezése

1. Jelentkezzen be helyileg Azure vagy az Azure-portál, az Azure-előfizetéssel társított fiókot használ, amely a virtuális Gépet tartalmaz. Emellett győződjön meg arról, hogy a fiókja tagja-e egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális gép (például "Virtuális gép közreműködő" szerepkör).

2. A sablon betöltése a szerkesztő, után keresse meg a `Microsoft.Compute/virtualMachines` házirendsablonokkal erőforrás a `resources` szakasz. Saját tűnhet kissé eltér attól függően, hogy a szerkesztő használata a következő képernyőfelvételen látható, és hogy szerkeszti egy sablont egy új központi telepítés vagy a meglévők egyikét.

   >[!NOTE] 
   > Ebben a példában feltételezzük például a változók `vmName`, `storageAccountName`, és `nicName` a sablonban definiált.
   >

   ![Képernyőkép a sablon - keresse meg a virtuális gép](../media/msi-qs-configure-template-windows-vm/template-file-before.png) 

3. Ahhoz, hogy a rendszer identitás hozzárendelve, adja hozzá a `"identity"` tulajdonság ugyanazon a szinten az `"type": "Microsoft.Compute/virtualMachines"` tulajdonság. A következő szintaxissal:

   ```JSON
   "identity": { 
       "type": "systemAssigned"
   },
   ```

4. (Választható) Adja hozzá a virtuális gép MSI kiterjesztést, mint egy `resources` elemet. Ez a lépés nem kötelező, hasonlóan a Azure példány metaadatok szolgáltatás (IMDS) identitás végpont, valamint a jogkivonatok beolvasása.  A következő szintaxissal:

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

   ![Képernyőkép a frissítés után sablon](../media/msi-qs-configure-template-windows-vm/template-file-after.png)

### <a name="disable-a-system-assigned-identity-from-an-azure-vm"></a>A rendszer az Azure virtuális gép identitásának hozzárendelt letiltása

> [!NOTE]
> Szolgáltatásidentitás felügyelt virtuális gépről letiltása jelenleg nem támogatott. Időközben válthat rendszer rendelve, és a felhasználó hozzárendelt identitások segítségével.

Ha egy virtuális Gépet, amely már nincs szüksége a felügyelt szolgáltatásidentitás:

1. Jelentkezzen be helyileg Azure vagy az Azure-portál, az Azure-előfizetéssel társított fiókot használ, amely a virtuális Gépet tartalmaz. Emellett győződjön meg arról, hogy a fiókja tagja-e egy szerepkör, amely lehetővé teszi az írási engedéllyel a virtuális gép (például "Virtuális gép közreműködő" szerepkör).

2. Az identity típus a `UserAssigned`.

## <a name="user-assigned-identity"></a>A felhasználói identitás

Ebben a szakaszban rendel egy hozzárendelt felhasználói azonosító az Azure virtuális gép Azure Resource Manager-sablon használatával.

> [!Note]
> Egy Azure Resource Manager-sablonnal a felhasználói identitás létrehozásához lásd: [nem hoz létre a felhasználói](how-to-manage-ua-identity-arm.md#create-a-user-assigned-identity).

 ### <a name="assign-a-user-assigned-identity-to-an-azure-vm"></a>Rendelje hozzá egy a felhasználói identitás egy Azure virtuális Gépen

1. Az a `resources` elemet, adja hozzá a következő bejegyzést hozzárendelt felhasználói azonosítót rendel a virtuális Gépet.  Ügyeljen arra, hogy a csere `<USERASSIGNEDIDENTITY>` nevű, a felhasználó identitásának létrehozott.
    ```json
    {
        "apiVersion": "2017-12-01",
        "type": "Microsoft.Compute/virtualMachines",
        "name": "[variables('vmName')]",
        "location": "[resourceGroup().location]",
        "identity": {
            "type": "userAssigned",
            "identityIds": [
                "[resourceID('Micrososft.ManagedIdentity/userAssignedIdentities/<USERASSIGNEDIDENTITYNAME>)']"
            ]
        },
    ```
    
2. (Választható) Ezután bontsa a `resources` elemet, adja hozzá a következő bejegyzést a felügyelt identity bővítmény hozzárendelése a virtuális Gépet. Ez a lépés nem kötelező, hasonlóan a Azure példány metaadatok szolgáltatás (IMDS) identitás végpont, valamint a jogkivonatok beolvasása. A következő szintaxissal:
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
    
3.  Amikor elkészült, a sablon a következő hasonlóan kell kinéznie:

      ![A felhasználói identitás képernyőképe](./media/qs-configure-template-windows-vm/qs-configure-template-windows-vm-ua-final.PNG)


## <a name="related-content"></a>Kapcsolódó tartalom

- Egy szélesebb körű perspektíva MSI kapcsolatban, olvassa el a [Szolgáltatásidentitás felügyelete – áttekintés](overview.md).

