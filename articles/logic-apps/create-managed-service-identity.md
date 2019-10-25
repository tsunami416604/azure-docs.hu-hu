---
title: Hitelesítés felügyelt identitásokkal – Azure Logic Apps
description: Más Azure Active Directory bérlők erőforrásainak elérése a hitelesítő adatokkal vagy titkos kódokkal való bejelentkezés nélkül felügyelt identitás használatával
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 10/21/2019
ms.openlocfilehash: fdc5340c9affa7137815577af842aa8b43a552a8
ms.sourcegitcommit: be8e2e0a3eb2ad49ed5b996461d4bff7cba8a837
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72799602"
---
# <a name="authenticate-access-to-azure-resources-by-using-managed-identities-in-azure-logic-apps"></a>Az Azure-erőforrásokhoz való hozzáférés hitelesítése felügyelt identitások használatával Azure Logic Apps

Ha más Azure Active Directory-(Azure AD-) bérlők erőforrásaihoz szeretne hozzáférni, és a bejelentkezés nélkül hitelesíti az identitást, a logikai alkalmazás a rendszerhez rendelt [felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md) (korábbi nevén Managed Service Identity vagy msi) is használhatja, nem pedig hitelesítő adatok vagy titkos kódok. Az Azure kezeli ezt az identitást, és segít a hitelesítő adatok biztonságossá tételében, mert nem kell a titkokat megadnia vagy elforgatnia. Ez a cikk bemutatja, hogyan állíthatja be és használhatja a rendszerhez rendelt felügyelt identitást a logikai alkalmazásban.

További információkért tekintse meg a következő témaköröket:

* [A felügyelt identitásokat támogató eseményindítók és műveletek](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [Az Azure AD-hitelesítést támogató Azure-szolgáltatások felügyelt identitásokkal](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)
* [A kimenő hívások támogatott hitelesítési típusai](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound)
* [A Logic apps felügyelt identitási korlátai](../logic-apps/logic-apps-limits-and-config.md#managed-identity)

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetést, vagy ha nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/). Mind a felügyelt identitást, mind a cél Azure-erőforrást, amelyhez a hozzáférést szeretné használni, ugyanazt az Azure-előfizetést kell használnia.

* [Azure ad-rendszergazdai engedélyek](../active-directory/users-groups-roles/directory-assign-admin-roles.md) , amelyek a célként megadott erőforrással azonos Azure ad-bérlőn belül rendelhetnek szerepköröket a felügyelt identitásokhoz. Ahhoz, hogy felügyelt identitást lehessen biztosítani egy Azure-erőforráshoz, hozzá kell adnia egy szerepkört az identitáshoz a cél erőforráson.

* Az elérni kívánt cél Azure-erőforrás

* A [felügyelt identitásokat támogató eseményindítókat és műveleteket](../logic-apps/logic-apps-securing-a-logic-app.md#add-authentication-outbound) használó logikai alkalmazás

<a name="system-assigned"></a>

## <a name="enable-system-assigned-identity"></a>Rendszer által hozzárendelt identitás engedélyezése

A felhasználó által hozzárendelt identitásokkal ellentétben nem kell manuálisan létrehoznia a rendszer által hozzárendelt identitást. A logikai alkalmazás rendszerhez rendelt identitásának beállításához a következő lehetőségek használhatók:

* [Azure Portalra](#azure-portal-system-logic-app)
* [Azure Resource Manager sablonok](#template-system-logic-app)
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md)
* [Azure CLI](../active-directory/managed-identities-azure-resources/howto-assign-access-cli.md)

<a name="azure-portal-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-portal"></a>Rendszer által hozzárendelt identitás engedélyezése Azure Portal

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A logikai alkalmazás menü **Beállítások**területén válassza az **Identity** > **rendszer hozzárendelve**elemet. Az **állapot**területen válassza **a** > **Mentés** > **Igen**lehetőséget.

   ![A rendszer által hozzárendelt identitás engedélyezése](./media/create-managed-service-identity/turn-on-system-assigned-identity.png)

   A logikai alkalmazás mostantól a rendszer által hozzárendelt identitást is használhatja, amely Azure Active Directory regisztrálva van, és egy objektumazonosító jelképezi.

   ![Rendszer által hozzárendelt identitáshoz tartozó objektumazonosító](./media/create-managed-service-identity/object-id.png)

   | Tulajdonság | Value (Díj) | Leírás |
   |----------|-------|-------------|
   | **Objektumazonosító** | <*identitás – erőforrás-azonosító*> | Globálisan egyedi azonosító (GUID), amely az Azure AD-bérlőben a logikai alkalmazás rendszer által hozzárendelt identitását jelöli |
   ||||

1. Ezután kövesse az [identitás elérését biztosító lépéseket az erőforráshoz](#access-other-resources).

<a name="template-system-logic-app"></a>

### <a name="enable-system-assigned-identity-in-azure-resource-manager-template"></a>Rendszerhez rendelt identitás engedélyezése Azure Resource Manager sablonban

Az Azure-erőforrások, például a Logic apps létrehozásának és üzembe helyezésének automatizálásához [Azure Resource Manager sablonokat](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)használhat. Ha engedélyezni szeretné a logikai alkalmazáshoz a rendszer által hozzárendelt felügyelt identitást a sablonban, adja hozzá a `identity` objektumot és a `type` Child tulajdonságot a logikai alkalmazás erőforrás-definíciójában a sablonban, például:

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

Amikor az Azure létrehozza a logikai alkalmazás erőforrás-definícióját, a `identity` objektum a következő tulajdonságokat kapja meg:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Tulajdonság (JSON) | Value (Díj) | Leírás |
|-----------------|-------|-------------|
| `principalId` | <*rendszerbiztonsági tag azonosítója*> | Az Azure AD-bérlőben a logikai alkalmazást képviselő felügyelt identitás globálisan egyedi azonosítója (GUID). Ez a GUID időnként "objektumazonosító" vagy `objectID`néven jelenik meg. |
| `tenantId` | <*Azure-ad-bérlői azonosító*> | A globálisan egyedi azonosító (GUID), amely az Azure AD-bérlőt jelképezi, ahol a logikai alkalmazás most már tagja. Az Azure AD-bérlőn belül az egyszerű szolgáltatásnév neve megegyezik a Logic app-példánnyal. |
||||

<a name="access-other-resources"></a>

## <a name="give-identity-access-to-resources"></a>Identitás-hozzáférés biztosítása az erőforrásokhoz

Miután beállította egy felügyelt identitást a logikai alkalmazáshoz, megadhatja, [hogy az identitás hozzáférjen más Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Ezt az identitást használhatja a hitelesítéshez.

1. A [Azure Portal](https://portal.azure.com)nyissa meg azt az Azure-erőforrást, ahol a felügyelt identitásának hozzáférése van.

1. Az erőforrás menüjében válassza a **hozzáférés-vezérlés (iam)**  > **szerepkör-hozzárendelések** lehetőséget, ahol áttekintheti az adott erőforráshoz tartozó aktuális szerepkör-hozzárendeléseket. Az eszköztáron válassza a **hozzáadás** > **szerepkör-hozzárendelés hozzáadása**elemet.

   ![Válassza a "Hozzáadás" > "szerepkör-hozzárendelés hozzáadása" lehetőséget.](./media/create-managed-service-identity/add-role-to-resource.png)

   > [!TIP]
   > Ha a **szerepkör-hozzárendelés hozzáadása** lehetőség le van tiltva, akkor valószínűleg nincs engedélye. További információ azokról az engedélyekről, amelyek lehetővé teszik az erőforrások szerepköreinek kezelését: [Azure Active Directory rendszergazdai szerepkör engedélyei](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

1. A **szerepkör-hozzárendelés hozzáadása**területen válassza ki azt a **szerepkört** , amely megadja a személyazonosságához szükséges hozzáférést a cél erőforráshoz.

   Ebben a témakörben az identitásának olyan szerepkörre van szüksége, [amely hozzáfér a blobhoz egy Azure Storage-tárolóban](../storage/common/storage-auth-aad.md#assign-rbac-roles-for-access-rights):

   ![A "Storage blob adatközreműködői" szerepkör kiválasztása](./media/create-managed-service-identity/assign-role.png)

1. A **hozzáférés kiosztása** mezőben válassza az **Azure ad-felhasználó,-csoport vagy egyszerű szolgáltatásnév**lehetőséget.

   ![A rendszerhez rendelt identitás hozzáférésének kiválasztása](./media/create-managed-service-identity/assign-access-system.png)

1. A **Select (kiválasztás** ) mezőben keresse meg és válassza ki a logikai alkalmazást.

   ![Logikai alkalmazás kiválasztása rendszerhez rendelt identitáshoz](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Amikor elkészült, válassza a **Mentés** lehetőséget.

   A cél erőforrás szerepkör-hozzárendelési listája most megjeleníti a kiválasztott felügyelt identitást és szerepkört.

   ![Felügyelt identitások és Szerepkörök hozzáadása az erőforrás megcélzásához](./media/create-managed-service-identity/added-roles-for-identities.png)

1. Ezután kövesse a [lépéseket a hozzáférés hitelesítéséhez](#authenticate-access-with-identity) egy triggerben vagy műveletben, amely támogatja a felügyelt identitásokat.

<a name="authenticate-access-with-identity"></a>

## <a name="authenticate-access-with-managed-identity"></a>Hozzáférés hitelesítése felügyelt identitással

Miután [engedélyezte a logikai alkalmazás felügyelt identitását](#azure-portal-system-logic-app) , és lehetővé teszi, [hogy az identitás hozzáférhessen a célként megadott erőforráshoz](#access-other-resources), ezt az identitást használhatja az [eseményindítókban és a felügyelt identitásokat támogató műveletekben](logic-apps-securing-a-logic-app.md#managed-identity-authentication).

> [!IMPORTANT]
> Ha rendelkezik olyan Azure-függvénnyel, ahol a rendszer által hozzárendelt identitást szeretné használni, először [engedélyezze az Azure functions hitelesítést](../logic-apps/logic-apps-azure-functions.md#enable-authentication-for-azure-functions).

Ezek a lépések bemutatják, hogyan használható a felügyelt identitás egy triggerrel vagy művelettel a Azure Portalon keresztül. A felügyelt identitás egy trigger vagy művelet mögöttes JSON-definíciójában való megadásához lásd: [felügyelt identitások hitelesítése](../logic-apps/logic-apps-securing-a-logic-app.md#managed-identity-authentication).

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. Ha még nem tette meg, adja hozzá a [felügyelt identitásokat támogató](logic-apps-securing-a-logic-app.md#managed-identity-authentication)triggert vagy műveletet.

   Tegyük fel például, hogy az Azure Storage-fiókban lévő blobon szeretné futtatni a [Pillanatkép-blob műveletet](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob) , ahol korábban már beállította a hozzáférést az identitásához, de az [Azure Blob Storage-összekötő](/connectors/azureblob/) jelenleg nem nyújtja ezt a műveletet. Ehelyett a [http-művelettel](../logic-apps/logic-apps-workflow-actions-triggers.md#http-action) futtathatja a műveletet vagy bármely más [blob Service REST API műveletet](https://docs.microsoft.com/rest/api/storageservices/operations-on-blobs). A hitelesítéshez a HTTP-művelet a logikai alkalmazáshoz engedélyezett rendszerhez rendelt identitást használhatja. A HTTP-művelet ezeket a tulajdonságokat is használja az elérni kívánt erőforrás megadásához:

   * Az **URI** tulajdonság a cél Azure-erőforrás eléréséhez használt végpont URL-címét adja meg. Ez az URI-szintaxis általában magában foglalja az Azure-erőforrás vagy-szolgáltatás [erőforrás-azonosítóját](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication) .

   * A **headers (fejlécek** ) tulajdonság megadja a kívánt fejléc-értékeket, vagy a kérelembe belefoglalni kívánja őket, például a cél erőforráson futtatni kívánt művelet API-verzióját.

   * A **lekérdezések** tulajdonság megadja a kérelemben szerepeltetni kívánt lekérdezési paramétereket, például egy adott művelet paraméterét vagy egy adott API-verziót, ha szükséges.

   A [Pillanatkép-blob művelet](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob)futtatásához a http-művelet a következő tulajdonságokat adja meg:

   * **Metódus**: a `PUT` műveletet adja meg.

   * **URI**: egy Azure Blob Storage-fájl erőforrás-azonosítóját adja meg az Azure globális (nyilvános) környezetben, és ezt a szintaxist használja:

     `https://{storage-account-name}.blob.core.windows.net/{blob-container-name}/{folder-name-if-any}/{blob-file-name-with-extension}`

   * **Headers (fejlécek**): meghatározza a pillanatkép-blob művelethez `x-ms-blob-type` `BlockBlob` és `2019-02-02` `x-ms-version`ként. További információkért lásd: [fejlécek kérése – pillanatkép-blob](https://docs.microsoft.com/rest/api/storageservices/snapshot-blob#request) és [verziószámozás az Azure Storage-szolgáltatásokhoz](https://docs.microsoft.com/rest/api/storageservices/versioning-for-the-azure-storage-services).

   * **Lekérdezések**: megadja `comp` a lekérdezési paraméter neve és a paraméter értékeként `snapshot`.

   Az alábbi példa HTTP-művelet az összes tulajdonság értékét megjeleníti:

   ![HTTP-művelet hozzáadása az Azure-erőforrásokhoz való hozzáféréshez](./media/create-managed-service-identity/http-action-example.png)

   Az összes elérhető Azure REST API művelettel kapcsolatos további információkért tekintse meg az [azure REST API-referenciát](https://docs.microsoft.com/rest/api/azure/).

1. A **hitelesítés** listából válassza a **felügyelt identitás**elemet. Ha a [ **hitelesítési** tulajdonság támogatott](logic-apps-securing-a-logic-app.md#add-authentication-outbound) , de rejtett, nyissa meg az **új paraméter hozzáadása** listát, és válassza a **hitelesítés**lehetőséget.

   > [!NOTE]
   > Nem minden eseményindító és művelet teszi lehetővé hitelesítési típus kiválasztását. További információ: [hitelesítés hozzáadása kimenő hívásokhoz](logic-apps-securing-a-logic-app.md#add-authentication-outbound).

   ![A "hitelesítés" tulajdonságban válassza a "felügyelt identitás" lehetőséget.](./media/create-managed-service-identity/select-managed-identity.png)

1. Miután kiválasztotta a **felügyelt identitást**, megjelenik a **célközönség** tulajdonság egyes eseményindítók és műveletek esetében. Ha a **célközönség** tulajdonság támogatott, de rejtett, nyissa meg az **új paraméter hozzáadása** listát, és válassza a **célközönség**lehetőséget.

1. Győződjön meg arról, hogy a **célközönség** értékét a cél erőforrás vagy szolgáltatás erőforrás-azonosítójára állítja be. Ellenkező esetben a **célközönség** tulajdonság a `https://management.azure.com/` erőforrás-azonosítót használja, amely a Azure Resource Manager erőforrás-azonosítója.

   > [!IMPORTANT]
   > Győződjön meg arról, hogy a célként megadott erőforrás-azonosító *pontosan megfelel* a Azure Active Directory (ad) által várt értéknek, beleértve a szükséges záró perjeleket is. Például az összes Azure Blob Storage-fiók erőforrás-azonosítója záró perjelet igényel. Egy adott Storage-fiók erőforrás-azonosítója azonban nem igényel záró perjelet. Keresse meg az Azure AD-t [támogató Azure-szolgáltatások erőforrás-azonosítóit](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication).

   Ez a példa a **célközönség** tulajdonságot úgy állítja be, hogy `https://storage.azure.com/`, hogy a hitelesítéshez használt hozzáférési jogkivonatok érvényesek legyenek az összes Storage-fiókra. Megadhatja azonban a gyökérszintű szolgáltatás URL-címét is, `https://fabrikamstorageaccount.blob.core.windows.net`egy adott Storage-fiókhoz.

   ![Cél erőforrás-azonosító meghatározása a "közönség" tulajdonságban](./media/create-managed-service-identity/specify-audience-url-target-resource.png)

   További információ az Azure AD-hez való hozzáférés engedélyezéséről az Azure Storage-ban:

   * [Azure-blobok és-várólisták hozzáférésének engedélyezése Azure Active Directory használatával](../storage/common/storage-auth-aad.md)
   * [Hozzáférés engedélyezése az Azure Storage-hoz a Azure Active Directory](https://docs.microsoft.com/rest/api/storageservices/authorize-with-azure-active-directory#use-oauth-access-tokens-for-authentication)

<a name="remove-identity"></a>

## <a name="remove-system-assigned-identity"></a>Rendszer által hozzárendelt identitás eltávolítása

A logikai alkalmazás rendszer által hozzárendelt identitásának leállításához a következő lehetőségek közül választhat:

* [Azure Portalra](#azure-portal-disable)
* [Azure Resource Manager sablonok](#template-disable)
* [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.resources/remove-azroleassignment)
* [Azure CLI](https://docs.microsoft.com/cli/azure/role/assignment?view=azure-cli-latest#az-role-assignment-delete)

Ha törli a logikai alkalmazást, az Azure automatikusan eltávolítja a felügyelt identitást az Azure AD-ből.

<a name="azure-portal-disable"></a>

### <a name="remove-system-assigned-identity-in-the-azure-portal"></a>A rendszer által hozzárendelt identitás eltávolítása a Azure Portal

A Azure Portal távolítsa el a rendszer által hozzárendelt identitást a [logikai alkalmazásból](#disable-identity-logic-app) , és az identitás hozzáférését [a célként megadott erőforrásból](#disable-identity-target-resource).

<a name="disable-identity-logic-app"></a>

#### <a name="remove-system-assigned-identity-from-logic-app"></a>Rendszer által hozzárendelt identitás eltávolítása a logikai alkalmazásból

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A logikai alkalmazás menü **Beállítások**területén válassza az **Identity** > **rendszer hozzárendelve**elemet. Az **állapot**alatt válassza **ki** > **Mentés** > **Igen**lehetőséget.

   ![Rendszerhez rendelt identitás használatának leállítása](./media/create-managed-service-identity/turn-off-system-assigned-identity.png)

<a name="disable-identity-target-resource"></a>

#### <a name="remove-identity-access-from-resources"></a>Identitás-hozzáférés eltávolítása az erőforrásokból

1. A [Azure Portal](https://portal.azure.com)nyissa meg a cél Azure-erőforrást, ahol el szeretné távolítani a hozzáférést egy felügyelt identitáshoz.

1. A cél erőforrás menüjében válassza a **hozzáférés-vezérlés (iam)** lehetőséget. Az eszköztár alatt válassza a **szerepkör-hozzárendelések**lehetőséget.

1. Válassza ki az eltávolítani kívánt felügyelt identitásokat a szerepkörök listából. Az eszköztáron válassza az **Eltávolítás**lehetőséget.

   > [!TIP]
   > Ha az **Eltávolítás** lehetőség le van tiltva, valószínűleg nincs engedélye. További információ azokról az engedélyekről, amelyek lehetővé teszik az erőforrások szerepköreinek kezelését: [Azure Active Directory rendszergazdai szerepkör engedélyei](../active-directory/users-groups-roles/directory-assign-admin-roles.md).

A felügyelt identitás már el lett távolítva, és már nem fér hozzá a célként megadott erőforráshoz.

<a name="template-disable"></a>

### <a name="disable-managed-identity-in-azure-resource-manager-template"></a>Felügyelt identitás letiltása Azure Resource Manager sablonban

Ha a logikai alkalmazás rendszerfelügyelt identitását Azure Resource Manager sablon használatával engedélyezte, az `identity` objektum `type` gyermek tulajdonságát `None`értékre kell állítani. Ez a művelet törli a rendszerfelügyelt identitás rendszerbiztonsági AZONOSÍTÓját is az Azure AD-ből.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>Következő lépések

* [Biztonságos hozzáférés és az adatAzure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
