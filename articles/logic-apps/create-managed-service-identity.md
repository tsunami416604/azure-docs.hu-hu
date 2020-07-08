---
title: Hitelesítés felügyelt identitásokkal
description: A Azure Active Directory által védett erőforrások elérése a hitelesítő adatokkal vagy titkos kódokkal való bejelentkezés nélkül felügyelt identitás használatával
services: logic-apps
ms.suite: integration
ms.reviewer: jonfan, logicappspm
ms.topic: article
ms.date: 02/10/2020
ms.openlocfilehash: 190cc74bc2967cdee7f3154e0d6a6fedd8ee90dd
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85565038"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Az Azure-erőforrásokhoz való hozzáférés hitelesítése felügyelt identitások használatával Azure Logic Apps

A Azure Active Directory (Azure AD) által védett egyéb erőforrások egyszerű eléréséhez és az identitás bejelentkezés nélküli hitelesítéséhez a logikai alkalmazás [felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md) (korábban Managed Service Identity vagy msi) is használhat a hitelesítő adatok és a titkos kódok helyett. Az Azure kezeli Ön helyett ezt az identitást, és segít biztonságban tartani az Ön hitelesítő adatait, mivel így nincs szükség titkos kulcsok megadására és rendszeres módosítására.

Azure Logic Apps a [*rendszer által hozzárendelt*](../active-directory/managed-identities-azure-resources/overview.md) és [*felhasználó által hozzárendelt*](../active-directory/managed-identities-azure-resources/overview.md) felügyelt identitásokat is támogatja. A logikai alkalmazás a rendszerhez hozzárendelt identitást vagy *egyetlen* felhasználó által hozzárendelt identitást is használhat, amely a logikai alkalmazások egy csoportján belül megosztható, de nem mindkettő. Jelenleg csak az [adott beépített eseményindítók és műveletek](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) támogatják a felügyelt identitásokat, nem felügyelt összekötőket és kapcsolatokat, például:

* HTTP
* Azure Functions
* Azure API Management
* Azure App Services

Ez a cikk bemutatja, hogyan állíthat be mindkét fajta felügyelt identitást a logikai alkalmazáshoz. További információt az alábbi témakörökben talál:

* [A felügyelt identitásokat támogató eseményindítók és műveletek](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [A kimenő hívások támogatott hitelesítési típusai](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [A Logic apps felügyelt identitási korlátai](../logic-apps/logic-apps-limits-and-config.md#managed-identity)
* [Az Azure AD-hitelesítést támogató Azure-szolgáltatások felügyelt identitásokkal](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetés. Ha még nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). A felügyelt identitásnak és a cél Azure-erőforrásnak is, amelyhez hozzáférésre van szüksége, ugyanazt az Azure-előfizetést kell használnia.

* Ahhoz, hogy felügyelt identitást lehessen biztosítani egy Azure-erőforráshoz, hozzá kell adnia egy szerepkört a cél erőforráshoz az identitáshoz. Szerepkörök hozzáadásához olyan [Azure ad-rendszergazdai engedélyekre](../active-directory/users-groups-roles/directory-assign-admin-roles.md) van szükség, amelyek szerepköröket rendelhetnek a megfelelő Azure ad-bérlőben található identitásokhoz.

* Az elérni kívánt cél Azure-erőforrás. Ezen az erőforráson hozzá fog adni egy szerepkört a felügyelt identitáshoz, amely segít a logikai alkalmazásnak a cél erőforráshoz való hozzáférés hitelesítésében.

* A logikai alkalmazás, amelyen használni szeretné a [felügyelt identitásokat támogató triggert vagy műveleteket](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)

## <a name="enable-managed-identity"></a>Felügyelt identitás engedélyezése

A használni kívánt felügyelt identitás beállításához kövesse az identitás hivatkozását:

* [Rendszer által hozzárendelt identitás](#system-assigned)
* [Felhasználó által hozzárendelt identitás](#user-assigned)

<a name="system-assigned"></a>

### <a name="enable-system-assigned-identity"></a>Rendszer által hozzárendelt identitás engedélyezése

A felhasználó által hozzárendelt identitásokkal ellentétben nem kell manuálisan létrehoznia a rendszer által hozzárendelt identitást. A logikai alkalmazás rendszer által hozzárendelt identitásának beállításához a következő lehetőségeket használhatja:

* [Azure Portalra](#azure-portal-system-logic-app)
* [Azure Resource Manager-sablonok](#template-system-logic-app)

<a name="azure-portal-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-portal"></a>Rendszer által hozzárendelt identitás engedélyezése Azure Portal

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A logikai alkalmazás menüjének **Beállítások**területén válassza az **identitás**elemet. Válassza **System assigned**  >  **a**  >  **mentéskor**hozzárendelt rendszer elemet. Ha az Azure kéri, hogy erősítse meg, válassza az **Igen**lehetőséget.

   ![A rendszer által hozzárendelt identitás engedélyezése](./media/create-managed-service-identity/enable-system-assigned-identity.png)

   > [!NOTE]
   > Ha olyan hibaüzenetet kap, amely csak egyetlen felügyelt identitással rendelkezhet, a logikai alkalmazás már társítva van a felhasználó által hozzárendelt identitással. A rendszerhez rendelt identitás hozzáadása előtt először *el kell távolítania* a felhasználó által hozzárendelt identitást a logikai alkalmazásból.

   A logikai alkalmazás mostantól a rendszer által hozzárendelt identitást is használhatja, amely Azure Active Directory regisztrálva van, és egy objektumazonosító jelképezi.

   ![Rendszer által hozzárendelt identitáshoz tartozó objektumazonosító](./media/create-managed-service-identity/object-id-system-assigned-identity.png)

   | Tulajdonság | Érték | Description |
   |----------|-------|-------------|
   | **Objektumazonosító** | <*identitás – erőforrás-azonosító*> | Globálisan egyedi azonosító (GUID), amely az Azure AD-bérlőben a logikai alkalmazás rendszer által hozzárendelt identitását jelöli |
   ||||

1. Ezután kövesse a jelen témakör későbbi részében található [, az erőforráshoz való hozzáféréshez szükséges lépéseket](#access-other-resources) .

<a name="template-system-logic-app"></a>

#### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Rendszerhez rendelt identitás engedélyezése Azure Resource Manager sablonban

Az Azure-erőforrások, például a Logic apps létrehozásának és üzembe helyezésének automatizálásához [Azure Resource Manager sablonokat](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)használhat. Ha engedélyezni szeretné a logikai alkalmazáshoz a rendszer által hozzárendelt felügyelt identitást a sablonban, adja hozzá az `identity` objektumot és a `type` gyermek tulajdonságot a logikai alkalmazás erőforrás-definíciójában a sablonban, például:

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

Amikor az Azure létrehozza a logikai alkalmazás erőforrás-definícióját, az `identity` objektum a következő további tulajdonságokat kapja:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Tulajdonság (JSON) | Érték | Description |
|-----------------|-------|-------------|
| `principalId` | <*elsődleges azonosító*> | Az Azure AD-bérlőben a logikai alkalmazást képviselő felügyelt identitás globálisan egyedi azonosítója (GUID). Ez a GUID időnként "objektumazonosító" vagy " `objectID` . |
| `tenantId` | <*Azure-AD-bérlő-azonosító*> | A globálisan egyedi azonosító (GUID), amely az Azure AD-bérlőt jelképezi, ahol a logikai alkalmazás most már tagja. Az Azure AD-bérlőn belül az egyszerű szolgáltatásnév neve megegyezik a Logic app-példánnyal. |
||||

<a name="user-assigned"></a>

### <a name="enable-user-assigned-identity"></a>Felhasználó által hozzárendelt identitás engedélyezése

Ha felhasználó által hozzárendelt felügyelt identitást szeretne beállítani a logikai alkalmazáshoz, először létre kell hoznia ezt az identitást külön önálló Azure-erőforrásként. A következő lehetőségek használhatók:

* [Azure Portalra](#azure-portal-user-identity)
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

#### <a name="create-user-assigned-identity-in-the-azure-portal"></a>Felhasználó által hozzárendelt identitás létrehozása a Azure Portalban

1. A [Azure Portal](https://portal.azure.com)bármely oldalon a keresőmezőbe írja be a kifejezést, `managed identities` majd válassza a **felügyelt identitások**lehetőséget.

   !["Felügyelt identitások" keresése és kiválasztása](./media/create-managed-service-identity/find-select-managed-identities.png)

1. A **felügyelt identitások**területen válassza a **Hozzáadás**lehetőséget.

   ![Új felügyelt identitás hozzáadása](./media/create-managed-service-identity/add-user-assigned-identity.png)

1. Adja meg a felügyelt identitásával kapcsolatos információkat, majd válassza a **Létrehozás**lehetőséget, például:

   ![Felhasználó által hozzárendelt felügyelt identitás létrehozása](./media/create-managed-service-identity/create-user-assigned-identity.png)

   | Tulajdonság | Kötelező | Érték | Description |
   |----------|----------|-------|-------------|
   | **Erőforrás neve** | Yes | <*felhasználó által hozzárendelt identitás neve*> | A felhasználó által hozzárendelt identitás nevét adja meg. Ez a példa a "Fabrikam-User-Assigned-Identity" kifejezést használja. |
   | **Előfizetés** | Yes | <*Azure-előfizetés – név*> | A használni kívánt Azure-előfizetés neve |
   | **Erőforráscsoport** | Yes | <*Azure-Erőforrás-csoport-név*> | A használni kívánt erőforráscsoport neve. Hozzon létre egy új csoportot, vagy válasszon ki egy meglévő csoportot. Ez a példa létrehoz egy "Fabrikam-Managed-idents-RG" nevű új csoportot. |
   | **Hely** | Yes | <*Azure-régió*> | Az Azure-régió, ahol az erőforrással kapcsolatos információk tárolhatók. Ez a példa a "West US"-t használja. |
   |||||

   Most hozzáadhatja a felhasználó által hozzárendelt identitást a logikai alkalmazáshoz. A logikai alkalmazáshoz nem adhat hozzá egynél több felhasználó által hozzárendelt identitást.

1. A Azure Portal a Logic app Designerben keresse meg és nyissa meg a logikai alkalmazást.

1. A logikai alkalmazás menüjének **Beállítások**területén válassza az **identitás**lehetőséget, majd válassza a **felhasználó által hozzárendelt**  >  **Hozzáadás**elemet.

   ![Felhasználóhoz rendelt felügyelt identitás hozzáadása](./media/create-managed-service-identity/add-user-assigned-identity-logic-app.png)

1. A **felhasználóhoz rendelt felügyelt identitás hozzáadása** panelen az **előfizetés** listából válassza ki az Azure-előfizetését, ha még nincs kiválasztva. Az előfizetésben található *összes* felügyelt identitást megjelenítő listában keresse meg és válassza ki a felhasználó által hozzárendelt identitást. A lista szűréséhez a felhasználó által **hozzárendelt felügyelt identitások** keresése mezőben adja meg az identitás vagy az erőforráscsoport nevét. Ha elkészült, válassza a **Hozzáadás**lehetőséget.

   ![Válassza ki a használni kívánt felhasználó által hozzárendelt identitást](./media/create-managed-service-identity/select-user-assigned-identity.png)

   > [!NOTE]
   > Ha olyan hibaüzenetet kap, amely csak egyetlen felügyelt identitással rendelkezhet, a logikai alkalmazás már társítva van a rendszer által hozzárendelt identitással. A felhasználó által hozzárendelt identitás hozzáadása előtt le kell tiltania a rendszer által hozzárendelt identitást a logikai alkalmazásban.

   A logikai alkalmazás most már társítva van a felhasználó által hozzárendelt felügyelt identitáshoz.

   ![Társítás felhasználó által hozzárendelt identitással](./media/create-managed-service-identity/added-user-assigned-identity.png)

1. Ezután kövesse a jelen témakör későbbi részében található [, az erőforráshoz való hozzáféréshez szükséges lépéseket](#access-other-resources) .

<a name="template-user-identity"></a>

#### <a name="create-user-assigned-identity-in-an-azure-resource-manager-template"></a>Felhasználó által hozzárendelt identitás létrehozása Azure Resource Manager sablonban

Az Azure-erőforrások (például a Logic apps) létrehozásának és üzembe helyezésének automatizálásához [Azure Resource Manager sablonokat](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)használhat, amelyek támogatják [a felhasználó által hozzárendelt identitásokat a hitelesítéshez](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-arm.md). A sablon `resources` szakaszában a logikai alkalmazás erőforrás-definíciója a következő elemeket igényli:

* Egy `identity` objektum, amelynek tulajdonsága a következőre van `type` beállítva`UserAssigned`

* Olyan gyermekobjektum `userAssignedIdentities` , amely meghatározza az identitás erőforrás-azonosítóját, amely egy másik gyermekobjektum, amely a `principalId` és `clientId` tulajdonságokkal rendelkezik.

Ez a példa egy logikai alkalmazás erőforrás-definícióját mutatja be egy HTTP PUT-kérelemhez, és tartalmaz egy nem paraméteres `identity` objektumot. Az PUT kérelemre és az azt követő lekérési műveletre adott válasz az alábbi `identity` objektummal is rendelkezik:

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

| Tulajdonság (JSON) | Érték | Description |
|-----------------|-------|-------------|
| `principalId` | <*elsődleges azonosító*> | A felhasználó által hozzárendelt felügyelt identitás globálisan egyedi azonosítója (GUID) az Azure AD-bérlőben |
| `clientId` | <*ügyfél-azonosító*> | Egy globálisan egyedi azonosító (GUID) a logikai alkalmazás új identitásához, amelyet a rendszer a Futtatás során hívásokhoz használ |
||||

Ha a sablon a felügyelt identitás erőforrás-definícióját is tartalmazza, akkor parametrizálja az `identity` objektumot. Ez a példa azt szemlélteti, hogy a gyermek `userAssignedIdentities` objektum hogyan hivatkozik a `userAssignedIdentity` sablon szakaszában definiált változóra `variables` . Ez a változó a felhasználó által hozzárendelt identitás erőforrás-AZONOSÍTÓJÁRA hivatkozik.

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

| Tulajdonság (JSON) | Érték | Description |
|-----------------|-------|-------------|
| `tenantId` | <*Azure-AD-bérlő-azonosító*> | A globálisan egyedi azonosító (GUID), amely az Azure AD-bérlőt jelképezi, ahol a felhasználó által hozzárendelt identitás most már tagja. Az Azure AD-bérlőn belül az egyszerű szolgáltatásnév neve megegyezik a felhasználó által hozzárendelt identitás nevével. |
| `principalId` | <*elsődleges azonosító*> | A felhasználó által hozzárendelt felügyelt identitás globálisan egyedi azonosítója (GUID) az Azure AD-bérlőben |
| `clientId` | <*ügyfél-azonosító*> | Egy globálisan egyedi azonosító (GUID) a logikai alkalmazás új identitásához, amelyet a rendszer a Futtatás során hívásokhoz használ |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Identitás-hozzáférés biztosítása az erőforrásokhoz

Ahhoz, hogy használni tudja a logikai alkalmazás felügyelt identitását a hitelesítéshez, állítsa be az identitás hozzáférését azon az Azure-erőforráson, ahol az identitást szeretné használni. A feladat elvégzéséhez rendelje hozzá a megfelelő szerepkört az identitáshoz a cél Azure-erőforráson. A következő lehetőségek használhatók:

* [Azure Portalra](#azure-portal-assign-access)
* [Azure Resource Manager-sablon](../role-based-access-control/role-assignments-template.md)
* Azure PowerShell ([New-AzRoleAssignment](https://docs.microsoft.com/powershell/module/az.resources/new-azroleassignment)) – További információ: szerepkör- [hozzárendelés hozzáadása az Azure RBAC és a Azure PowerShell használatával](../role-based-access-control/role-assignments-powershell.md).
* Azure CLI ([az szerepkör-hozzárendelés létrehozása](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-create)) – További információ: [szerepkör-hozzárendelés hozzáadása az Azure RBAC és az Azure CLI használatával](../role-based-access-control/role-assignments-cli.md).
* [Azure REST API](../role-based-access-control/role-assignments-rest.md)

<a name="azure-portal-assign-access"></a>

### <a name="assign-access-in-the-azure-portal"></a>Hozzáférés kiosztása a Azure Portal

1. A [Azure Portal](https://portal.azure.com)nyissa meg azt az Azure-erőforrást, ahol a felügyelt identitásának hozzáférése van.

1. Az erőforrás menüjében válassza a **hozzáférés-vezérlés (iam)**  >  **szerepkör-hozzárendelések** lehetőséget, ahol áttekintheti az adott erőforráshoz tartozó aktuális szerepkör-hozzárendeléseket. Az eszköztáron válassza a **Hozzáadás**  >  **szerepkör-hozzárendelés hozzáadása**elemet.

   ![Válassza a "Hozzáadás" > "szerepkör-hozzárendelés hozzáadása" lehetőséget.](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Ha a **szerepkör-hozzárendelés hozzáadása** lehetőség le van tiltva, akkor valószínűleg nincs engedélye. További információ azokról az engedélyekről, amelyek lehetővé teszik az erőforrások szerepköreinek kezelését: [Azure Active Directory rendszergazdai szerepkör engedélyei](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. A **szerepkör-hozzárendelés hozzáadása**területen válassza ki azt a **szerepkört** , amely megadja a személyazonosságához szükséges hozzáférést a cél erőforráshoz.

   Ebben a témakörben az identitásnak olyan szerepkörre van szüksége, [amely hozzáfér a blobhoz egy Azure Storage-tárolóban](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights).

   ![A "Storage blob adatközreműködői" szerepkör kiválasztása](./media/create-managed-service-identity/select-role-for-identity.png)

1. Kövesse az alábbi lépéseket a felügyelt identitáshoz:

   * **Rendszer által hozzárendelt identitás**

     1. A **hozzáférés társítása** mezőben válassza a **logikai alkalmazás**lehetőséget. Amikor megjelenik az **előfizetés** tulajdonság, válassza ki az identitásához hozzárendelt Azure-előfizetést.

        ![A rendszerhez rendelt identitás hozzáférésének kiválasztása](./media/create-managed-service-identity/assign-access-system.png)

     1. A **Select (kiválasztás** ) mezőben válassza ki a logikai alkalmazást a listából. Ha a lista túl hosszú, a lista szűréséhez használja a **kiválasztás** mezőt.

        ![Logikai alkalmazás kiválasztása rendszerhez rendelt identitáshoz](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

   * **Felhasználó által hozzárendelt identitás**

     1. A **hozzáférés hozzárendelése** mezőben válassza ki a **felhasználóhoz rendelt felügyelt identitás**elemet. Amikor megjelenik az **előfizetés** tulajdonság, válassza ki az identitásához hozzárendelt Azure-előfizetést.

        ![Felhasználó által hozzárendelt identitás hozzáférésének kiválasztása](./media/create-managed-service-identity/assign-access-user.png)

     1. A **Select (kiválasztás** ) mezőben válassza ki az identitását a listából. Ha a lista túl hosszú, a lista szűréséhez használja a **kiválasztás** mezőt.

        ![Válassza ki a felhasználó által hozzárendelt identitást](./media/create-managed-service-identity/add-permissions-select-user-assigned-identity.png)

1. Amikor elkészült, válassza a **Mentés** lehetőséget.

   A cél erőforrás szerepkör-hozzárendelési listája most megjeleníti a kiválasztott felügyelt identitást és szerepkört. Ebből a példából megtudhatja, hogyan használhatja a rendszerszintű identitást egy logikai alkalmazáshoz, illetve egy felhasználó által hozzárendelt identitást más logikai alkalmazások csoportjához.

   ![Felügyelt identitások és Szerepkörök hozzáadása az erőforrás megcélzásához](./media/create-managed-service-identity/added-roles-for-identities.png)

   További információért [a Azure Portal használatával rendeljen hozzá egy felügyelt identitáshoz való hozzáférést az erőforráshoz](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md).

1. Ezután kövesse a [lépéseket a hozzáférés hitelesítéséhez](#authenticate-access-with-identity) egy triggerben vagy műveletben, amely támogatja a felügyelt identitásokat.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Hozzáférés hitelesítése felügyelt identitással

Miután [engedélyezte a logikai alkalmazás felügyelt identitását](#azure-portal-system-logic-app) , és lehetővé teszi, [hogy az identitás hozzáférjen a célként megadott erőforráshoz vagy entitáshoz](#access-other-resources), a [felügyelt identitásokat támogató eseményindítókban és műveletekben](logic-apps-securing-a-logic-app.md#managed-identity-authentication)is használhatja ezt az identitást.

> [!IMPORTANT]
> Ha rendelkezik olyan Azure-függvénnyel, ahol a rendszer által hozzárendelt identitást szeretné használni, először [engedélyezze az Azure functions hitelesítést](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

Ezek a lépések bemutatják, hogyan használható a felügyelt identitás egy triggerrel vagy művelettel a Azure Portalon keresztül. A felügyelt identitás egy trigger vagy művelet mögöttes JSON-definíciójában való megadásához lásd: [felügyelt identitások hitelesítése](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. Ha még nem tette meg, adja hozzá a [felügyelt identitásokat támogató triggert vagy műveletet](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

   A HTTP-trigger vagy a művelet például használhatja a logikai alkalmazáshoz engedélyezett rendszer által hozzárendelt identitást. Általánosságban elmondható, hogy a HTTP-trigger vagy-művelet ezeket a tulajdonságokat használja az elérni kívánt erőforrás vagy entitás megadásához:

   | Tulajdonság | Kötelező | Leírás |
   |----------|----------|-------------|
   | **Metódus** | Yes | A futtatni kívánt művelet által használt HTTP-metódus |
   | **URI** | Yes | A cél Azure-erőforrás vagy-entitás eléréséhez használt végpont URL-címe. Az URI-szintaxis általában magában foglalja az Azure-erőforrás vagy-szolgáltatás [erőforrás-azonosítóját](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) . |
   | **Fejlécek** | No | Minden szükséges vagy a kimenő kérelembe belefoglalni kívánt fejléc-érték, például a tartalom típusa |
   | **Lekérdezések** | No | A kérelembe belefoglalni kívánt lekérdezési paramétereket, például egy adott művelet paraméterét vagy a futtatni kívánt művelet API-verzióját |
   | **Hitelesítés** | Yes | A célként megadott erőforráshoz vagy entitáshoz való hozzáférés hitelesítéséhez használandó hitelesítési típus |
   ||||

   Tegyük fel, hogy a [Pillanatkép-blob műveletet](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) egy olyan Azure Storage-fiókban lévő blobon szeretné futtatni, amelyben korábban beállította a hozzáférést az identitásához. Az [Azure Blob Storage-összekötő](https://docs.microsoft.com/connectors/azureblob/) azonban jelenleg nem nyújtja ezt a műveletet. Ehelyett ezt a műveletet a [http-művelet](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) vagy egy másik [blob Service REST API művelet](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs)használatával futtathatja.

   > [!IMPORTANT]
   > Ha a tűzfal mögötti Azure Storage-fiókokat HTTP-kérések és felügyelt identitások használatával szeretné elérni, ügyeljen arra, hogy a Storage-fiókját a [megbízható Microsoft-szolgáltatások általi hozzáférést lehetővé tevő kivételt](../connectors/connectors-create-api-azureblobstorage.md#access-trusted-service)is állítsa be.

   A pillanatkép- [blob művelet](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)futtatásához a http-művelet a következő tulajdonságokat adja meg:

   | Tulajdonság | Kötelező | Példaérték | Description |
   |----------|----------|---------------|-------------|
   | **Metódus** | Yes | `PUT`| A pillanatkép-blob művelet által használt HTTP-metódus |
   | **URI** | Yes | `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}` | Egy Azure-Blob Storage fájl erőforrás-azonosítója az Azure globális (nyilvános) környezetében, amely ezt a szintaxist használja |
   | **Fejlécek** | Igen, az Azure Storage-hoz | `x-ms-blob-type` = `BlockBlob` <p>`x-ms-version` = `2019-02-02` | Az `x-ms-blob-type` `x-ms-version` Azure Storage-műveletekhez szükséges és a fejléc értékei. <p><p>**Fontos**: a kimenő http-triggerben és az Azure Storage-beli műveleti kérelmekben a fejléchez a `x-ms-version` futtatni kívánt művelethez szükséges tulajdonság és API-verzió szükséges. <p>További információt az alábbi témakörökben talál: <p><p>- [Kérések fejlécei – pillanatkép-blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) <br>- [Verziószámozás az Azure Storage-szolgáltatásokhoz](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services#specifying-service-versions-in-requests) |
   | **Lekérdezések** | Igen, ehhez a művelethez | `comp` = `snapshot` | A lekérdezési paraméter neve és értéke a pillanatkép-blob művelethez. |
   |||||

   Az alábbi példa HTTP-művelet az összes tulajdonság értékét megjeleníti:

   ![HTTP-művelet hozzáadása az Azure-erőforrásokhoz való hozzáféréshez](./media/create-managed-service-identity/http-action-example.png)

1. Most adja hozzá a **hitelesítési** tulajdonságot a http-művelethez. Az **új paraméter hozzáadása** listából válassza a **hitelesítés**lehetőséget.

   !["Hitelesítés" tulajdonság hozzáadása HTTP-művelethez](./media/create-managed-service-identity/add-authentication-property.png)

   > [!NOTE]
   > Nem minden eseményindító és művelet támogatja a hitelesítési típus hozzáadását. További információ: [hitelesítés hozzáadása kimenő hívásokhoz](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound).

1. A **Hitelesítés típusa** listából válassza a **felügyelt identitás**elemet.

   ![A "hitelesítés" beállításnál válassza a "felügyelt identitás" lehetőséget.](./media/create-managed-service-identity/select-managed-identity.png)

1. A felügyelt identitás listából válassza ki az elérhető lehetőségek közül a forgatókönyv alapján.

   * Ha a rendszer által hozzárendelt identitást állítja be, akkor válassza ki a **rendszerhez rendelt felügyelt identitás** elemet, ha még nincs kiválasztva.

     ![Válassza a "rendszerhez rendelt felügyelt identitás" lehetőséget.](./media/create-managed-service-identity/select-system-assigned-identity-for-action.png)

   * Ha felhasználó által hozzárendelt identitást állít be, válassza ki ezt az identitást, ha még nincs kiválasztva.

     ![Válassza ki a felhasználó által hozzárendelt identitást](./media/create-managed-service-identity/select-user-assigned-identity-for-action.png)

   Ez a példa folytatja a **rendszerhez rendelt felügyelt identitást**.

1. Egyes eseményindítók és műveletek esetében a **célközönség** tulajdonság is megjelenik a cél erőforrás-azonosító megadásához. A **célközönség** tulajdonság értékeként adja meg a [cél erőforrás vagy szolgáltatás erőforrás-azonosítóját](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). Ellenkező esetben a **célközönség** tulajdonság az `https://management.azure.com/` erőforrás-azonosítót használja, amely a Azure Resource Manager erőforrás-azonosítója.

   > [!IMPORTANT]
   > Győződjön meg arról, hogy a célként megadott erőforrás-azonosító *pontosan megfelel* a Azure Active Directory (ad) által várt értéknek, beleértve a szükséges záró perjeleket is. Például az összes Azure Blob Storage-fiók erőforrás-azonosítója záró perjelet igényel. Egy adott Storage-fiók erőforrás-azonosítója azonban nem igényel záró perjelet. Keresse meg az Azure AD-t [támogató Azure-szolgáltatások erőforrás-azonosítóit](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Ebben a példában a **célközönség** tulajdonságot úgy állítja be, hogy `https://storage.azure.com/` a hitelesítéshez használt hozzáférési jogkivonatok érvényesek legyenek az összes Storage-fiókra. Megadhatja azonban a gyökérszintű szolgáltatás URL-címét is `https://fabrikamstorageaccount.blob.core.windows.net` egy adott Storage-fiókhoz.

   ![A "hallgatóság" tulajdonság beállítása erőforrás-AZONOSÍTÓra](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   További információ az Azure AD-hez való hozzáférés engedélyezéséről az Azure Storage-ban:

   * [Azure-blobok és-várólisták hozzáférésének engedélyezése Azure Active Directory használatával](../storage/common/storage-auth-aad.md)
   * [Hozzáférés engedélyezése az Azure Storage-hoz a Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

1. Folytassa a logikai alkalmazás létrehozásával a kívánt módon.

<a name="remove-identity"></a>

## <a name="disable-managed-identity"></a>Felügyelt identitás letiltása

Ha nem szeretne felügyelt identitást használni a logikai alkalmazáshoz, a következő lehetőségek állnak rendelkezésre:

* [Azure Portalra](#azure-portal-disable)
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

### <a name="disable-managed-identity-in-the-azure-portal"></a>Felügyelt identitás letiltása a Azure Portalban

A Azure Portal először távolítsa el az identitás hozzáférését a [célként megadott erőforráshoz](#disable-identity-target-resource). Ezután kapcsolja ki a rendszer által hozzárendelt identitást, vagy távolítsa el a felhasználó által hozzárendelt identitást a [logikai alkalmazásból](#disable-identity-logic-app).

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Identitás-hozzáférés eltávolítása az erőforrásokból

1. A [Azure Portal](https://portal.azure.com)nyissa meg a cél Azure-erőforrást, ahol el szeretné távolítani a felügyelt identitás hozzáférését.

1. A cél erőforrás menüjében válassza a **hozzáférés-vezérlés (iam)** lehetőséget. Az eszköztár alatt válassza a **szerepkör-hozzárendelések**lehetőséget.

1. Válassza ki az eltávolítani kívánt felügyelt identitásokat a szerepkörök listából. Az eszköztáron válassza az **Eltávolítás**lehetőséget.

   > [!TIP]
   > Ha az **Eltávolítás** lehetőség le van tiltva, valószínűleg nincs engedélye. További információ azokról az engedélyekről, amelyek lehetővé teszik az erőforrások szerepköreinek kezelését: [Azure Active Directory rendszergazdai szerepkör engedélyei](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

A felügyelt identitás már el lett távolítva, és már nem fér hozzá a célként megadott erőforráshoz.

<a name="disable-identity-logic-app"></a>

#### <a name="disable-managed-identity-on-logic-app"></a>Felügyelt identitás letiltása a logikai alkalmazásban

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A logikai alkalmazás menüjének **Beállítások**területén válassza az **identitás**lehetőséget, majd kövesse az identitásának lépéseit:

   * Válassza **System assigned**  >  **a**  >  **mentéskor**hozzárendelt rendszer elemet. Ha az Azure kéri, hogy erősítse meg, válassza az **Igen**lehetőséget.

     ![A rendszer által hozzárendelt identitás letiltása](./media/create-managed-service-identity/disable-system-assigned-identity.png)

   * Válassza a **hozzárendelt felhasználó** és a felügyelt identitás lehetőséget, majd válassza az **Eltávolítás**lehetőséget. Ha az Azure kéri, hogy erősítse meg, válassza az **Igen**lehetőséget.

     ![A felhasználó által hozzárendelt identitás eltávolítása](./media/create-managed-service-identity/remove-user-assigned-identity.png)

A felügyelt identitás mostantól le van tiltva a logikai alkalmazásban.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Felügyelt identitás letiltása Azure Resource Manager sablonban

Ha a logikai alkalmazás felügyelt identitását egy Azure Resource Manager sablonnal hozta létre, állítsa be az `identity` objektum `type` gyermek tulajdonságát a következőre: `None` . A rendszer által felügyelt identitás esetében ez a művelet törli a résztvevő AZONOSÍTÓját is az Azure AD-ből.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>További lépések

* [Biztonságos hozzáférés és az adatAzure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
