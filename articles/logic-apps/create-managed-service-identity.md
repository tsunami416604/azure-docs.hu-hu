---
title: Hitelesítés felügyelt identitásokkal
description: Erőforrások elérése más Azure Active Directory-bérlőkben anélkül, hogy hitelesítő adatokkal vagy titkos fájlokkal jelentkezne be egy felügyelt identitás használatával
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 82710a66cdf7874c745070e49b2c7aff7bc8816d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77117328"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Az Azure-erőforrásokhoz való hozzáférés hitelesítése felügyelt identitások használatával az Azure Logic Apps-alkalmazásokban

Más Azure Active Directory (Azure AD) bérlők erőforrásainak eléréséhez és az identitás bejelentkezés nélküli hitelesítéséhez a logikai alkalmazás hitelesítő adatok vagy titkos kulcsok helyett [felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md) (korábban felügyelt szolgáltatásidentitást vagy MSI-t) használhat. Az Azure kezeli ezt az identitást az Ön számára, és segít a hitelesítő adatok biztonságossá tétele, mert nem kell megadnia vagy elforgatnia a titkos kulcsokat.

Az Azure Logic Apps támogatja a [*rendszer által hozzárendelt*](../active-directory/managed-identities-azure-resources/overview.md) és [*a felhasználó által hozzárendelt*](../active-directory/managed-identities-azure-resources/overview.md) felügyelt identitások. A logikai alkalmazás használhatja a rendszer által hozzárendelt identitást vagy *egyetlen* felhasználó által hozzárendelt identitást, amelyet a logikai alkalmazások egy csoportjában oszthat meg, de mindkettőt nem. Jelenleg csak [bizonyos beépített eseményindítók és műveletek támogatják](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) a felügyelt identitásokat, nem a felügyelt összekötők vagy kapcsolatok, például:

* HTTP
* Azure Functions
* Azure API Management
* Azure App Services

Ez a cikk bemutatja, hogyan állíthatja be mindkét típusú felügyelt identitások a logikai alkalmazáshoz. További információt az alábbi témakörökben talál:

* [A felügyelt identitásokat támogató eseményindítók és műveletek](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Támogatott hitelesítési típusok kimenő hívásokesetén](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Felügyelt identitáskorlátok a logikai alkalmazásokhoz](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Azure-szolgáltatások, amelyek támogatják az Azure AD-hitelesítést felügyelt identitásokkal](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). A felügyelt identitás és a cél Azure-erőforrás, ahol hozzáférésre van szüksége, ugyanazt az Azure-előfizetést kell használnia.

* Felügyelt identitás-hozzáférés egy Azure-erőforráshoz való hozzáférést, hozzá kell adnia egy szerepkört a célerőforráshoz az adott identitáshoz. Szerepkörök hozzáadásához szüksége van [az Azure AD rendszergazdai engedélyeket,](../active-directory/users-groups-roles/directory-assign-admin-roles.md) amelyek szerepkörökhozzárendelése identitások a megfelelő Azure AD-bérlő.

* A cél Azure-erőforrás, amely et el szeretne érni. Ezen az erőforráson hozzá kell adnia egy szerepkört a felügyelt identitáshoz, amely segít a logikai alkalmazásnak a célerőforráshoz való hozzáférés hitelesítéséhez.

* Az a logikai alkalmazás, amelyben használni szeretné a [felügyelt identitásokat támogató eseményindítót vagy műveleteket](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Felügyelt identitás engedélyezése

A használni kívánt felügyelt identitás beállításához kövesse az adott identitásra mutató hivatkozást:

* [Rendszerhez rendelt identitás](#system-assigned)
* [Felhasználó által hozzárendelt identitás](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Rendszeráltal hozzárendelt identitás engedélyezése

A felhasználó által hozzárendelt identitásokkal ellentétben nem kell manuálisan létrehoznia a rendszer által hozzárendelt identitást. A rendszeráltal hozzárendelt identitás beállítása a logikai alkalmazáshoz az alábbiakat használhatja:

* [Azure-portál](#azure-portal-system-logic-app)
* [Azure Resource Manager-sablonok](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Rendszeráltal hozzárendelt identitás engedélyezése az Azure Portalon

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designerben.

1. A logikai alkalmazás **menüjének Beállítások**területén válassza az **Identitás**lehetőséget. Válassza a**On** > **Mentéskor** **hozzárendelt** > rendszer lehetőséget. Amikor az Azure megerősítést kér, válassza az **Igen**lehetőséget.

   ![A rendszer által hozzárendelt identitás engedélyezése](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Ha olyan hibaüzenetet kap, hogy csak egyetlen felügyelt identitással rendelkezhet, a logikai alkalmazás már társítva van a felhasználó által hozzárendelt identitáshoz. A rendszer által hozzárendelt identitás hozzáadása előtt először el kell *távolítania* a felhasználó által hozzárendelt identitást a logikai alkalmazásból.

   A logikai alkalmazás most már használhatja a rendszer által hozzárendelt identitást, amely regisztrálva van az Azure Active Directoryban, és egy objektumazonosító képviseli.

   ![A rendszer által hozzárendelt identitás objektumazonosítója](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Objektumazonosító** | <*identitás-erőforrás-azonosító*> | Globálisan egyedi azonosító (GUID), amely a rendszeráltal hozzárendelt identitást jelöli a logikai alkalmazáshoz egy Azure AD-bérlőben |
   ||||

1. Most kövesse [azidentitás hozzáférését az erőforráshoz a](#access-other-resources) témakör későbbi részében.

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Rendszeralapú identitás engedélyezése az Azure Resource Manager-sablonban

Az Azure-erőforrások, például a logikai alkalmazások létrehozásának és üzembe helyezésének automatizálásához [használhatja az Azure Resource Manager-sablonokat.](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md) Ha engedélyezni szeretné a rendszeráltal hozzárendelt felügyelt identitást a `identity` sablonban `type` a logikai alkalmazáshoz, adja hozzá az objektumot és a gyermektulajdonságot a logikai alkalmazás erőforrás-definíciójához a sablonban, például:

```json
{
   "apiVersion": "2016-06-01",
   "type": "Microsoft.logic/workflows",
   "name": "[variables('logicappName')]",
   "location": "[resourceGroup().location]",
   "identity": {
      "type": "SystemAssigned"
   },
   "properties": {
      "definition": {
         "$schema": "https://schema.management.azure.com/providers/Microsoft.Logic/schemas/2016-06-01/workflowdefinition.json#",
         "actions": {},
         "parameters": {},
         "triggers": {},
         "contentVersion": "1.0.0.0",
         "outputs": {}
   },
   "parameters": {},
   "dependsOn": []
}
```

Amikor az Azure létrehozza a `identity` logikai alkalmazás erőforrás-definícióját, az objektum a következő további tulajdonságokat kapja:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Ingatlan (JSON) | Érték | Leírás |
|-----------------|-------|-------------|
| `principalId` | <*megbízó-azonosító*> | A globálisan egyedi azonosító (GUID) a szolgáltatás egyszerű objektum a felügyelt identitás, amely a logikai alkalmazást az Azure AD-bérlőben képviseli. Ez a GUID néha "objektumazonosítóként" vagy `objectID`. |
| `tenantId` | <*Azure-AD-tenant-ID*> | A globálisan egyedi azonosító (GUID), amely az Azure AD-bérlőt jelöli, ahol a logikai alkalmazás már tagja. Az Azure AD-bérlőn belül a szolgáltatásnév a logikai alkalmazáspéldány. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Felhasználó által hozzárendelt identitás engedélyezése

A logikai alkalmazás felhasználó által hozzárendelt felügyelt identitás beállításához először létre kell hoznia ezt az identitást külön önálló Azure-erőforrásként. Az alábbiakat használhatja:

* [Azure-portál](#azure-portal-user-identity)
* [Azure Resource Manager-sablonok](#template-user-identity)
* Azure PowerShell
  * [Felhasználó által hozzárendelt identitás létrehozása](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
  * [Szerepkör-hozzárendelés hozzáadása](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* Azure CLI
  * [Felhasználó által hozzárendelt identitás létrehozása](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
  * [Szerepkör-hozzárendelés hozzáadása](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)
* Azure REST API
  * [Felhasználó által hozzárendelt identitás létrehozása](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)
  * [Szerepkör-hozzárendelés hozzáadása](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-user-identity"></a>

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Felhasználó által hozzárendelt identitás létrehozása az Azure Portalon

1. Az [Azure Portalon](https://portal.azure.com)a keresőmezőbe bármelyik `managed identities`lapon írja be a be írását, és válassza a **Felügyelt identitások**lehetőséget.

   ![A "Felügyelt identitások" megkeresése és kiválasztása](./media/create-managed-service-identity/find-select-managed-identities.png)

1. A **Felügyelt identitások csoportban**válassza **a Hozzáadás**lehetőséget.

   ![Új felügyelt identitás hozzáadása](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Adja meg a felügyelt identitással kapcsolatos adatokat, majd válassza a **Létrehozás**lehetőséget, például:

   ![Felhasználó által hozzárendelt felügyelt identitás létrehozása](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Tulajdonság | Kötelező | Érték | Leírás |
   |----------|----------|-------|-------------|
   | **Erőforrás neve** | Igen | <*felhasználó által hozzárendelt identitás-név*> | A felhasználó által hozzárendelt identitást megadó név. Ez a példa a "Fabrikam-user-assigned-identity" (Fabrikam-user-assigned-identity) parancsot használja. |
   | **Előfizetés** | Igen | <*Azure-előfizetés-név*> | A használandó Azure-előfizetés neve |
   | **Erőforráscsoport** | Igen | <*Azure-erőforrás-csoport név*> | A használandó erőforráscsoport neve. Hozzon létre egy új csoportot, vagy jelöljön ki egy meglévő csoportot. Ez a példa létrehoz egy új csoportot a "fabrikam-managed-identities-RG" nevű. |
   | **Helyen** | Igen | <*Azure-régió*> | Az Azure-régió, ahol az erőforrás adatait tárolja. Ez a példa a "West USA" (USA nyugati) |
   |||||

   Most már hozzáadhatja a felhasználó által hozzárendelt identitást a logikai alkalmazáshoz. A logikai alkalmazáshoz nem adhat hozzá egynél több felhasználó által hozzárendelt identitást.

1. Az Azure Portalon keresse meg és nyissa meg a logikai alkalmazást a Logic App Designerben.

1. A logikai alkalmazás **menüjében**válassza a Beállítások **csoportidentitás parancsát,** majd a **Hozzáadás felhasználó** > **jaként**lehetőséget.

   ![Felhasználó által hozzárendelt felügyelt identitás hozzáadása](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. A **Felhasználó hozzárendelt felügyelt identitásának hozzáadása** ablaktáblán az **Előfizetés** listából válassza ki az Azure-előfizetést, ha még nincs kiválasztva. Az előfizetés *összes* felügyelt identitását megjelenítő listából keresse meg és válassza ki a kívánt felhasználó által hozzárendelt identitást. A lista szűréséhez a **Felhasználó által hozzárendelt felügyelt identitások** keresőmezőjében adja meg az identitás vagy erőforráscsoport nevét. Ha elkészült, válassza a **Hozzáadás**lehetőséget.

   ![Válassza ki a használni kívánt felhasználó által hozzárendelt identitást](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Ha olyan hibaüzenetet kap, hogy csak egyetlen felügyelt identitással rendelkezhet, a logikai alkalmazás már társítva van a rendszer által hozzárendelt identitáshoz. A felhasználó által hozzárendelt identitás hozzáadása előtt először le kell tiltania a rendszer által hozzárendelt identitást a logikai alkalmazásban.

   A logikai alkalmazás most már társítva van a felhasználó által hozzárendelt felügyelt identitás.

   ![Társítás a felhasználó által hozzárendelt identitással](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Most kövesse [azidentitás hozzáférését az erőforráshoz a](#access-other-resources) témakör későbbi részében.

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Felhasználó által hozzárendelt identitás létrehozása Azure Resource Manager-sablonban

Az Azure-erőforrások, például a logikai alkalmazások létrehozásának és üzembe helyezésének automatizálásához [használhatja az Azure Resource Manager-sablonokat,](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)amelyek támogatják [a felhasználó által hozzárendelt identitásokat a hitelesítéshez.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md) A sablon szakaszában `resources` a logikai alkalmazás erőforrás-definíciója a következő elemeket igényli:

* Olyan `identity` objektum, `type` amelynek tulajdonsága`UserAssigned`

* Az `userAssignedIdentities` identitás erőforrásazonosítóját megadó gyermekobjektum, amely egy másik `principalId` gyermekobjektum, amely rendelkezik a és `clientId` tulajdonságokkal

Ez a példa egy LOGIC App erőforrás-definícióját mutatja be `identity` egy HTTP PUT-kérelemhez, és nem paraméterezett objektumot tartalmaz. A PUT-kérelemre és az azt `identity` követő GET-műveletre adott válasz ban is szerepel ez az objektum:

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {<template-parameters>},
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicappName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "/subscriptions/<Azure-subscription-ID>/resourceGroups/<Azure-resource-group-name>/providers/Microsoft.ManagedIdentity/userAssignedIdentities/<user-assigned-identity-name>": {
                  "principalId": "<principal-ID>",
                  "clientId": "<client-ID>"
               }
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": []
      },
   ],
   "outputs": {}
}
```

| Ingatlan (JSON) | Érték | Leírás |
|-----------------|-------|-------------|
| `principalId` | <*megbízó-azonosító*> | A globálisan egyedi azonosító (GUID) a felhasználó által hozzárendelt felügyelt identitás az Azure AD-bérlő |
| `clientId` | <*ügyfél-Azonosító*> | Globálisan egyedi azonosító (GUID) a logikai alkalmazás új identitásához, amelyet futásidőben történő hívásokhoz használnak |
||||

Ha a sablon tartalmazza a felügyelt identitás erőforrás-definícióját `identity` is, paraméterezheti az objektumot. Ez a példa `userAssignedIdentities` azt mutatja `userAssignedIdentity` be, hogy a gyermekobjektum `variables` hogyan hivatkozik a sablon szakaszában megadott változóra. Ez a változó a felhasználó által hozzárendelt identitás erőforrásazonosítójára hivatkozik.

```json
{
   "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
   "contentVersion": "1.0.0.0",
   "parameters": {
      "Template_LogicAppName": {
         "type": "string"
      },
      "Template_UserAssignedIdentityName": {
         "type": "securestring"
      }
   },
   "variables": {
      "logicAppName": "[parameters(`Template_LogicAppName')]",
      "userAssignedIdentityName": "[parameters('Template_UserAssignedIdentityName')]"
   },
   "resources": [
      {
         "apiVersion": "2016-06-01",
         "type": "Microsoft.logic/workflows",
         "name": "[variables('logicAppName')]",
         "location": "[resourceGroup().location]",
         "identity": {
            "type": "UserAssigned",
            "userAssignedIdentities": {
               "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]": {}
            }
         },
         "properties": {
            "definition": {<logic-app-workflow-definition>}
         },
         "parameters": {},
         "dependsOn": [
            "[resourceId('Microsoft.ManagedIdentity/userAssignedIdentities/', variables('userAssignedIdentityName'))]"
         ]
      },
      {
         "apiVersion": "2018-11-30",
         "type": "Microsoft.ManagedIdentity/userAssignedIdentities",
         "name": "[parameters('Template_UserAssignedIdentityName')]",
         "location": "[resourceGroup().location]",
         "properties": {
            "tenantId": "<tenant-ID>",
            "principalId": "<principal-ID>",
            "clientId": "<client-ID>"
         }
      }
  ]
}
```

| Ingatlan (JSON) | Érték | Leírás |
|-----------------|-------|-------------|
| `tenantId` | <*Azure-AD-tenant-ID*> | A globálisan egyedi azonosító (GUID), amely az Azure AD-bérlőt jelöli, ahol a felhasználó által hozzárendelt identitás már tag. Az Azure AD-bérlőn belül a szolgáltatásnév a felhasználó által hozzárendelt identitásnév. |
| `principalId` | <*megbízó-azonosító*> | A globálisan egyedi azonosító (GUID) a felhasználó által hozzárendelt felügyelt identitás az Azure AD-bérlő |
| `clientId` | <*ügyfél-Azonosító*> | Globálisan egyedi azonosító (GUID) a logikai alkalmazás új identitásához, amelyet futásidőben történő hívásokhoz használnak |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Identitás-hozzáférés biztosítása az erőforrásokhoz

Mielőtt használhatja a logikai alkalmazás felügyelt identitáshitelesítéshez, állítsa be a hozzáférést az adott identitás az Azure-erőforrás, ahol azt tervezi, hogy az identitás használatát. A feladat végrehajtásához rendelje hozzá a megfelelő szerepkört az adott identitáshoz a cél Azure-erőforráson. Az alábbiakat használhatja:

* [Azure-portál](#azure-portal-assign-access)
* [Azure Resource Manager-sablon](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)) – További információ: [Szerepkör-hozzárendelés hozzáadása az Azure RBAC és az Azure PowerShell használatával című témakörben.](../role-based-access-control/role-assignments-powershell.md)
* Azure CLI ([az szerepkör-hozzárendelés létrehozása](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)) – További információ: [Szerepkör-hozzárendelés hozzáadása az Azure RBAC és az Azure CLI használatával](../role-based-access-control/role-assignments-cli.md)című témakörben.
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Hozzáférés hozzárendelése az Azure Portalon

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg az Azure-erőforrást, ahol szeretné, hogy a felügyelt identitás hozzáférjen.

1. Az erőforrás menüjében válassza a **Hozzáférés-vezérlési (IAM)** > **szerepkör-hozzárendeléseket,** ahol megtekintheti az adott erőforrás aktuális szerepkör-hozzárendeléseit. Az eszköztáron válassza a**Szerepkör-hozzárendelés** **hozzáadása** > lehetőséget.

   ![Válassza a "Hozzáadás" > a "Szerepkör-hozzárendelés hozzáadása" lehetőséget.](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Ha a **Szerepkör-hozzárendelés hozzáadása** beállítás le van tiltva, akkor valószínűleg nincs engedélye. Az erőforrások szerepköreinek kezelését lehetővé tévő engedélyekről az [Azure Active Directory rendszergazdai szerepkör-engedélyei](../active-directory/users-groups-roles/directory-assign-admin-roles.md)című témakörben talál további információt.

1. A **Szerepkör-hozzárendelés hozzáadása**csoportban válasszon ki egy **szerepkört,** amely megadja az identitásnak a szükséges hozzáférést a célerőforráshoz.

   Ebben a témakörben például az identitás szüksége van egy [szerepkör, amely elérheti a blob egy Azure Storage-tárolóban.](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights)

   ![Válassza a "Storage Blob Data Contributor" szerepkört](./media/create-managed-service-identity/select-role-for-identity.png)

1. A felügyelt identitáshoz kövesse az alábbi lépéseket:

   * **Rendszerhez rendelt identitás**

     1. A **Hozzáférés hozzárendelése** a mezőben válassza a **Logikai alkalmazás lehetőséget.** Amikor megjelenik az **Előfizetés** tulajdonság, válassza ki az identitásához társított Azure-előfizetést.

        ![Hozzáférés kiválasztása a rendszerhez rendelt identitáshoz](./media/create-managed-service-identity/assign-access-system.png)

     1. A **Kijelölés** mezőben válassza ki a logikai alkalmazást a listából. Ha a lista túl hosszú, a **Kijelölés** mezővel szűrheti a listát.

        ![Logikai alkalmazás kiválasztása a rendszerhez rendelt identitáshoz](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Felhasználó által hozzárendelt identitás**

     1. A **Hozzáférés hozzárendelése** a mezőben válassza a **Felhasználó által hozzárendelt felügyelt identitás lehetőséget.** Amikor megjelenik az **Előfizetés** tulajdonság, válassza ki az identitásához társított Azure-előfizetést.

        ![Hozzáférés kiválasztása a felhasználó által hozzárendelt identitáshoz](./media/create-managed-service-identity/assign-access-user.png)

     1. A **Kijelölés** mezőben válassza ki az identitását a listából. Ha a lista túl hosszú, a **Kijelölés** mezővel szűrheti a listát.

        ![A felhasználó által hozzárendelt identitás kiválasztása](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Amikor elkészült, válassza a **Mentés** lehetőséget.

   A célerőforrás szerepkör-hozzárendelések listája mostantól a kiválasztott felügyelt identitást és szerepkört jeleníti meg. Ez a példa bemutatja, hogyan használhatja a rendszer által hozzárendelt identitás egy logikai alkalmazás és egy felhasználó által hozzárendelt identitás egy csoport más logikai alkalmazások.

   ![Felügyelt identitások és szerepkörök hozzáadása az erőforrás megcélzásához](./media/create-managed-service-identity/added-roles-for-identities.png)

   További információkért [rendeljen hozzá egy felügyelt identitás-hozzáférést egy erőforráshoz az Azure Portal használatával.](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md)

1. Most kövesse a lépéseket a [hozzáférés hitelesítéséhez az identitással](#authenticate-access-with-identity) egy eseményindító vagy művelet, amely támogatja a felügyelt identitások.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Hozzáférés hitelesítése felügyelt identitással

Miután [engedélyezte a felügyelt identitást a logikai alkalmazáshoz,](#azure-portal-system-logic-app) és hozzáférést [ad az identitásnak a célerőforráshoz vagy entitáshoz,](#access-other-resources)használhatja ezt az identitást a [felügyelt identitásokat támogató eseményindítókban és műveletekben.](logic-apps-securing-a-logic-app.md#managed-identity-authentication)

> [!IMPORTANT]
> Ha rendelkezik egy Azure-funkcióval, ahol a rendszerhez rendelt identitást szeretné használni, először [engedélyezze a hitelesítést az Azure-függvényekhez.](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions)

Ezek a lépések bemutatják, hogyan használhatja a felügyelt identitást egy eseményindítóval vagy művelettel az Azure Portalon keresztül. A felügyelt identitás megadása egy eseményindító vagy művelet alapjául szolgáló JSON-definíció, lásd: [Felügyelt identitás hitelesítése.](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication)

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designerben.

1. Ha még nem tette meg, adja hozzá a [felügyelt identitásokat támogató eseményindítót vagy műveletet.](logic-apps-securing-a-logic-app.md#managed-identity-authentication)

   A HTTP-eseményindító vagy -művelet például használhatja a rendszer által kijelölt identitást, amelyet a logikai alkalmazáshoz engedélyezett. A HTTP-eseményindító vagy -művelet általában ezeket a tulajdonságokat használja az elérni kívánt erőforrás vagy entitás megadására:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Módszer** | Igen | A futtatni kívánt művelet által használt HTTP-módszer |
   | **Uri** | Igen | A végpont URL-címe a cél Azure-erőforrás vagy entitás eléréséhez. Az URI-szintaxis általában tartalmazza az Azure-erőforrás vagy szolgáltatás [erőforrás-azonosítóját.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) |
   | **Fejlécek** | Nem | A kimenő kérelemben szükséges vagy felvenni kívánt fejlécértékek, például a tartalomtípus |
   | **Lekérdezések** | Nem | A kérelemben szükséges vagy felvenni kívánt lekérdezési paraméterek, például egy adott művelet paramétere vagy a futtatni kívánt művelet API-verziója |
   | **Hitelesítés** | Igen | A célerőforráshoz vagy entitáshoz való hozzáférés hitelesítéséhez használandó hitelesítési típus |
   ||||

   Egy adott példa, tegyük fel, hogy szeretné futtatni a [Pillanatkép blob művelet](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) egy blob az Azure Storage-fiókban, ahol korábban beállított a hozzáférést az identitáshoz. Azonban az [Azure Blob Storage-összekötő](https://docs.microsoft.com/connectors/azureblob/) jelenleg nem kínálja ezt a műveletet. Ehelyett futtathatja ezt a műveletet a [HTTP-művelet](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) vagy egy másik [Blob Service REST API-művelet](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs)használatával.

   > [!IMPORTANT]
   > Ha HTTP-kérelmek és felügyelt identitások használatával szeretné elérni a tűzfalak mögötti Azure storage-fiókokat, győződjön meg arról, hogy a tárfiókot is beállította azzal a [kivétellel, amely lehetővé teszi a megbízható Microsoft-szolgáltatások hozzáférését.](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)

   A [Snapshot Blob művelet](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)futtatásához a HTTP-művelet a következő tulajdonságokat adja meg:

   | Tulajdonság | Kötelező | Példaérték | Leírás |
   |----------|----------|---------------|-------------|
   | **Módszer** | Igen | `PUT`| A Pillanatkép Blob művelet által használt HTTP-metódus |
   | **Uri** | Igen | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Egy Azure Blob Storage-fájl erőforrásazonosítója az Azure Global (nyilvános) környezetben, amely ezt a szintaxist használja |
   | **Fejlécek** | Igen, az Azure Storage-hoz | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Az `x-ms-blob-type` `x-ms-version` Azure Storage-műveletekhez szükséges fejlécértékek. <p><p>**Fontos:** Az Azure Storage kimenő HTTP-eseményindító- és `x-ms-version` műveletkérelmekben a fejléchez a futtatni kívánt művelethez a tulajdonság és az API-verzió szükséges. <p>További információt az alábbi témakörökben talál: <p><p>- [Kérelemfejlécek – Pillanatképblob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Verziószámozás az Azure Storage-szolgáltatásokhoz](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Lekérdezések** | Igen, ehhez a művelethez. | `comp` = `snapshot` | A lekérdezési paraméter neve és értéke a Snapshot Blob művelet. |
   |||||

   Íme a HTTP-művelet, amely az összes tulajdonságértéket megjeleníti:

   ![HTTP-művelet hozzáadása egy Azure-erőforrás eléréséhez](./media/create-managed-service-identity/http-action-example.png)

1. Most adja hozzá a **Hitelesítés** tulajdonságot a HTTP-művelethez. Az **Új paraméter hozzáadása** listában válassza a **Hitelesítés**lehetőséget.

   !["Authentication" tulajdonság hozzáadása http-művelethez](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Nem minden eseményindítók és műveletek támogatása lehetővé teszi egy hitelesítési típus hozzáadása. További információt a [Hitelesítés hozzáadása kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)című témakörben talál.

1. A **Hitelesítéstípus** listában válassza a **Felügyelt identitás lehetőséget.**

   ![A "Hitelesítés" mezőben válassza a "Felügyelt identitás" lehetőséget.](./media/create-managed-service-identity/select-managed-identity.png)

1. A felügyelt identitáslistában válassza ki a forgatókönyv alapján elérhető lehetőségeket.

   * Ha beállítja a rendszer által hozzárendelt identitást, válassza a **Rendszer hozzárendelt felügyelt identitását,** ha még nincs bejelölve.

     ![Válassza a "Rendszerhez rendelt felügyelt identitás" lehetőséget.](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Ha felhasználó által hozzárendelt identitást állít be, jelölje ki azt az identitást, ha még nincs kijelölve.

     ![A felhasználó által hozzárendelt identitás kiválasztása](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Ez a példa a **rendszerhez rendelt felügyelt identitással**folytatódik.

1. Egyes eseményindítóknál és műveleteknél megjelenik a **Célközönség** tulajdonság is, amely a célerőforrás-azonosító beállításához kerül. Állítsa be a **Célközönség** tulajdonságot [a célerőforrás vagy -szolgáltatás erőforrás-azonosítójára.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) Ellenkező esetben alapértelmezés **Audience** szerint a `https://management.azure.com/` Közönség tulajdonság az erőforrás-azonosítót használja, amely az Azure Resource Manager erőforrásazonosítója.

   > [!IMPORTANT]
   > Győződjön meg arról, hogy a célerőforrás-azonosító *pontosan megegyezik* az Azure Active Directory (AD) által várt értékkel, beleértve a szükséges követő perjeleket is. Például az összes Azure Blob Storage-fiók erőforrás-azonosítója egy követő perjelet igényel. Azonban az erőforrás-azonosító egy adott tárfiókhoz nem igényel követő perjelet. Ellenőrizze az [Azure-szolgáltatásokat támogató Azure-szolgáltatások erőforrás-azonosítóit.](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

   Ez a példa úgy `https://storage.azure.com/` **állítja** be a Audience tulajdonságot, hogy a hitelesítéshez használt hozzáférési jogkivonatok minden tárfiókra érvényesek. Azonban azt is megadhatja a `https://fabrikamstorageaccount.blob.core.windows.net`gyökérszolgáltatás URL-címét, egy adott tárfiókhoz.

   ![A "Célközönség" tulajdonság beállítása az erőforrás-azonosító célzására](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   Az Azure AD azure Storage-hoz való hozzáférés engedélyezéséről az alábbi témakörökben olvashat bővebben:

   * [Hozzáférés engedélyezése az Azure-blobokhoz és várólistákhoz az Azure Active Directory használatával](../storage/common/storage-auth-aad.md)
   * [Hozzáférés engedélyezése az Azure Storage-hoz az Azure Active Directoryval](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Folytassa a logikai alkalmazás kívánt módon való felépítését.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Felügyelt identitás letiltása

Ha le szeretné állítani a felügyelt identitás használatát a logikai alkalmazáshoz, az alábbi lehetőségek közül választhat:

* [Azure-portál](#azure-portal-disable)
* [Azure Resource Manager-sablonok](#template-disable)
* Azure PowerShell
  * [Szerepkör-hozzárendelés eltávolítása](../role-based-access-control/role-assignments-powershell.md)
  * [Felhasználó által hozzárendelt identitás törlése](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-powershell.md)
* Azure CLI
  * [Szerepkör-hozzárendelés eltávolítása](../role-based-access-control/role-assignments-cli.md)
  * [Felhasználó által hozzárendelt identitás törlése](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-cli.md)
* Azure REST API
  * [Szerepkör-hozzárendelés eltávolítása](../role-based-access-control/role-assignments-rest.md)
  * [Felhasználó által hozzárendelt identitás törlése](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-rest.md)

Ha törli a logikai alkalmazást, az Azure automatikusan eltávolítja a felügyelt identitást az Azure AD-ből.

<a name="azure-portal-disable"></a>

### <a name="disable-managed-identity-in-the-azure-portal"></a>Felügyelt identitás letiltása az Azure Portalon

Az Azure Portalon először távolítsa el az identitás hozzáférését [a célerőforráshoz.](#disable-identity-target-resource) Ezután kapcsolja ki a rendszer által hozzárendelt identitást, vagy távolítsa el a felhasználó által hozzárendelt identitást [a logikai alkalmazásból.](#disable-identity-logic-app)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Identitás-hozzáférés eltávolítása az erőforrásokból

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a cél Azure-erőforrás, ahol el szeretné távolítani a felügyelt identitás eléréséhez.

1. A célerőforrás menüjében válassza a **Hozzáférés-vezérlés (IAM) lehetőséget.** Az eszköztár alatt válassza a **Szerepkör-hozzárendelések**lehetőséget.

1. A szerepkörök listájában jelölje ki az eltávolítani kívánt felügyelt identitásokat. Az eszköztáron válassza az **Eltávolítás gombot.**

   > [!TIP]
   > Ha az **Eltávolítás** lehetőség le van tiltva, akkor valószínűleg nincs engedélye. Az erőforrások szerepköreinek kezelését lehetővé tévő engedélyekről az [Azure Active Directory rendszergazdai szerepkör-engedélyei](../active-directory/users-groups-roles/directory-assign-admin-roles.md)című témakörben talál további információt.

A felügyelt identitás most már törlődik, és már nem fér hozzá a célerőforráshoz.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Felügyelt identitás letiltása a logikai alkalmazásban

1. Az [Azure Portalon](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic App Designerben.

1. A logikai alkalmazás **menüjében**a Beállítások csoportban válassza az **Identitás**lehetőséget, majd kövesse az identitáslépéseit:

   * Válassza a**On** > **Mentéskor** **hozzárendelt** > rendszer lehetőséget. Amikor az Azure megerősítést kér, válassza az **Igen**lehetőséget.

     ![A rendszer által hozzárendelt identitás letiltása](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Válassza **a Hozzárendelt felhasználó** és a felügyelt identitás lehetőséget, majd válassza az Eltávolítás **lehetőséget.** Amikor az Azure megerősítést kér, válassza az **Igen**lehetőséget.

     ![A felhasználó által hozzárendelt identitás eltávolítása](./media/create-managed-service-identity/remove-user-assigned-identity.png)

A felügyelt identitás most le van tiltva a logikai alkalmazásban.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Felügyelt identitás letiltása az Azure Resource Manager-sablonban

Ha a logikai alkalmazás felügyelt identitását egy Azure Resource Manager-sablon `type` használatával hozta `None`létre, állítsa be az `identity` objektum gyermektulajdonságát. A rendszer által felügyelt identitás esetén ez a művelet is törli a fő azonosítót az Azure AD-ből.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>További lépések

* [Biztonságos hozzáférés és adatok az Azure Logic-alkalmazásokban](../logic-apps/logic-apps-securing-a-logic-app.md)