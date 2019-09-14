---
title: Hitelesítés felügyelt identitásokkal – Azure Logic Apps
description: A bejelentkezés nélküli hitelesítéshez létrehozhat egy felügyelt identitást (korábbi nevén Managed Service Identity vagy MSI), így a logikai alkalmazás más Azure Active Directory (Azure AD-) bérlők erőforrásaihoz is hozzáférhet a hitelesítő adatok vagy titkos kódok nélkül
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 03/29/2019
ms.openlocfilehash: d6cf19a07829afea924d3d799b1309cfc5f6329f
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70959971"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Felügyelt identitásokkal rendelkező erőforrások hitelesítése és elérése Azure Logic Apps

Ha más Azure Active Directory-(Azure AD-) bérlők erőforrásaihoz szeretne hozzáférni, és a bejelentkezés nélkül hitelesíti az identitást, a logikai alkalmazás a hitelesítő adatok és a titkos kódok helyett [felügyelt identitást](../active-directory/managed-identities-azure-resources/overview.md) (korábbi nevén Managed Service Identity vagy msi) is használhat. Az Azure kezeli ezt az identitást, és segít a hitelesítő adatok biztonságossá tételében, mert nem kell a titkokat megadnia vagy elforgatnia. Ebből a cikkből megtudhatja, hogyan állíthatja be és használhatja a logikai alkalmazás rendszerhez rendelt felügyelt identitását. A felügyelt identitásokkal kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitása?](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> A logikai alkalmazás csak a felügyelt identitásokat támogató összekötők használatával tud felügyelt identitásokat használni. Jelenleg csak a HTTP-összekötő támogatja a felügyelt identitásokat.
>
> Az egyes Azure-előfizetésekben jelenleg akár 100 logikai alkalmazás-munkafolyamattal is rendelkezhet, amely rendszerhez rendelt felügyelt identitásokkal rendelkezik.

## <a name="prerequisites"></a>Előfeltételek

* Egy Azure-előfizetést, vagy ha nincs előfizetése, [regisztráljon egy ingyenes Azure-fiókra](https://azure.microsoft.com/free/).

* Az a logikai alkalmazás, amelyben a rendszerhez rendelt felügyelt identitást szeretné használni. Ha nem rendelkezik logikai alkalmazással, tekintse [meg az első logikai alkalmazás munkafolyamatának létrehozását](../logic-apps/quickstart-create-first-logic-app-workflow.md)ismertető témakört.

<a name="enable-identity"></a>

## <a name="enable-managed-identity"></a>Felügyelt identitás engedélyezése

A rendszer által hozzárendelt felügyelt identitások esetében nem kell manuálisan létrehoznia ezt az identitást. A logikai alkalmazáshoz a rendszerhez rendelt felügyelt identitás beállításához a következő módszereket használhatja: 

* [Azure Portal](#azure-portal) 
* [Azure Resource Manager sablonok](#template) 
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 

<a name="azure-portal"></a>

### <a name="azure-portal"></a>Azure Portal

Ha a logikai alkalmazáshoz a Azure Portalon keresztül kívánja engedélyezni a rendszerhez rendelt felügyelt identitást, kapcsolja be a **rendszerhez rendelt** beállítást a logikai alkalmazás identitás-beállításaiban.

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A logikai alkalmazás menüjének **Beállítások**területén válassza az **identitás**elemet.

1. A **rendszerhez rendelt** > **állapot**területen válassza **a be**lehetőséget. Ezután válassza a **Mentés** > **Igen**lehetőséget.

   ![Felügyelt identitás beállításának bekapcsolása](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

   A logikai alkalmazás most már rendelkezik egy, a Azure Active Directoryban regisztrált, rendszerhez rendelt felügyelt identitással:

   ![Objektumazonosító GUID azonosítói](./media/create-managed-service-identity/object-id.png)

   | Tulajdonság | Érték | Leírás |
   |----------|-------|-------------|
   | **Objektumazonosító** | <*identitás – erőforrás-azonosító*> | Globálisan egyedi azonosító (GUID), amely az Azure AD-bérlőben a logikai alkalmazás rendszerhez rendelt felügyelt identitását jelöli |
   ||||

<a name="template"></a>

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

Ha automatizálni szeretné az Azure-erőforrások, például a Logic apps létrehozását és üzembe helyezését, [Azure Resource Manager sablonokat](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)is használhat. Ha a logikai alkalmazáshoz rendszer által hozzárendelt felügyelt identitást szeretne létrehozni egy sablonon `"identity"` keresztül, `"type"` adja hozzá az elemet és a tulajdonságot a logikai alkalmazás munkafolyamatának definíciójában a telepítési sablonban: 

```json
"identity": {
   "type": "SystemAssigned"
}
```

Példa:

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

Amikor az Azure létrehozza a logikai alkalmazást, a logikai alkalmazás munkafolyamat-definíciója a következő további tulajdonságokat tartalmazza:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Tulajdonság | Érték | Leírás |
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | Egy globálisan egyedi azonosító (GUID), amely az Azure AD-bérlőben található logikai alkalmazást jelöli, és néha "objektumazonosító"-ként jelenik meg, vagy`objectID` |
| **tenantId** | <*Azure-AD-tenant-ID*> | Egy globálisan egyedi azonosító (GUID), amely az Azure AD-bérlőt jelképezi, ahol a logikai alkalmazás most már tagja. Az Azure AD-bérlőn belül az egyszerű szolgáltatásnév neve megegyezik a Logic app-példánnyal. |
||||

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Erőforrások elérése felügyelt identitással

Miután létrehozott egy rendszerhez rendelt felügyelt identitást a logikai alkalmazáshoz, megadhatja [, hogy az identitás hozzáférjen más Azure-erőforrásokhoz](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Ezután használhatja ezt az identitást a hitelesítéshez, ugyanúgy, mint bármely más [egyszerű szolgáltatáshoz](../active-directory/develop/app-objects-and-service-principals.md). 

> [!NOTE]
> A rendszer által hozzárendelt felügyelt identitásnak és az erőforrásnak, amelyhez a hozzáférést hozzá kívánja rendelni, ugyanazzal az Azure-előfizetéssel kell rendelkeznie.

### <a name="assign-access-to-managed-identity"></a>Hozzáférés kiosztása a felügyelt identitáshoz

Ha hozzáférést szeretne adni egy másik Azure-erőforráshoz a logikai alkalmazás rendszer által hozzárendelt felügyelt identitásához, kövesse az alábbi lépéseket:

1. A Azure Portal keresse meg azt az Azure-erőforrást, amelyhez hozzáférést szeretne rendelni a felügyelt identitáshoz.

1. Az erőforrás menüjében válassza a **hozzáférés-vezérlés (iam)** lehetőséget. Az eszköztáron válassza a **Hozzáadás** > **szerepkör-hozzárendelés hozzáadása**elemet.

   ![Szerepkör-hozzárendelés hozzáadása](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. A **szerepkör-hozzárendelés hozzáadása**területen válassza ki az identitáshoz használni kívánt **szerepkört** .

1. Ha még nincs kiválasztva a **hozzáférés** a tulajdonsághoz, válassza az **Azure ad-felhasználó, csoport vagy szolgáltatásnév**lehetőséget.

1. A logikai alkalmazás nevének első karakterével kezdődően a **Select (kiválasztás** ) mezőben adja meg a logikai alkalmazás nevét. Amikor megjelenik a logikai alkalmazás, válassza ki a logikai alkalmazást.

   ![Logikai alkalmazás kiválasztása felügyelt identitással](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Ha elkészült, kattintson a **Mentés** gombra.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>Hitelesítés felügyelt identitással a logikai alkalmazásban

Miután beállította a logikai alkalmazást egy rendszerhez rendelt felügyelt identitással, és hozzárendelte az identitáshoz használni kívánt erőforráshoz, mostantól ezt az identitást is használhatja a hitelesítéshez. HTTP-műveletet használhat például úgy, hogy a logikai alkalmazás HTTP-kérést küldjön, vagy meghívja az adott erőforrást. 

1. A logikai alkalmazásban adja hozzá a **http** -műveletet.

1. Adja meg az adott művelethez szükséges adatokat, például a kérés **módszerét** és a hívni kívánt erőforrás **URI** -helyét.

   Tegyük fel például, hogy Azure Active Directory (Azure AD) hitelesítést használ az [Azure ad-t támogató Azure-szolgáltatások egyikével](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). Az **URI** mezőben adja meg az Azure-szolgáltatás végpontjának URL-címét. Ha azonban Azure Resource Manager használ, adja meg ezt az értéket az **URI** tulajdonságban:

   `https://management.azure.com/subscriptions/<Azure-subscription-ID>?api-version=2016-06-01`

1. A HTTP-műveletben válassza a **Speciális beállítások megjelenítése lehetőséget**.

1. A **hitelesítés** listából válassza a **felügyelt identitás**elemet. A hitelesítés kiválasztása után a **célközönség** tulajdonság az alapértelmezett erőforrás-azonosító értékkel jelenik meg:

   !["Felügyelt identitás" kiválasztása](./media/create-managed-service-identity/select-managed-service-identity.png)

   > [!IMPORTANT]
   > 
   > A **célközönség** tulajdonságban az erőforrás-azonosító értékének pontosan egyeznie kell azzal, amit az Azure ad vár, beleértve a szükséges záró perjeleket is. 
   > Ezek az erőforrás-azonosító értékek az [Azure ad-t támogató Azure-szolgáltatásokat ismertető táblázatban](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication)találhatók. 
   > Ha például a Azure Resource Manager erőforrás-azonosítót használja, győződjön meg róla, hogy az URI-nak záró perjele van.

1. Folytassa a logikai alkalmazás létrehozásával a kívánt módon.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Felügyelt identitás eltávolítása

Ha egy rendszer által hozzárendelt felügyelt identitást szeretne letiltani a logikai alkalmazáson, kövesse az identitás beállításához hasonló lépéseket a Azure Portal, Azure Resource Manager üzembe helyezési sablonok vagy a Azure PowerShell használatával.

Ha törli a logikai alkalmazást, az Azure automatikusan eltávolítja a logikai alkalmazás rendszer által hozzárendelt identitását az Azure AD-ből.

### <a name="azure-portal"></a>Azure Portal

Ha el szeretné távolítani a logikai alkalmazás rendszerhez rendelt felügyelt identitását a Azure Portalon keresztül, kapcsolja ki a **rendszerhez rendelt** beállítást a logikai alkalmazás identitás-beállításaiban.

1. A [Azure Portalban](https://portal.azure.com)nyissa meg a logikai alkalmazást a Logic app Designerben.

1. A logikai alkalmazás menüjének **Beállítások**területén válassza az **identitás**elemet.

1. A **rendszerhez rendelt** > **állapot**területen válassza a **ki**lehetőséget. Ezután válassza a **Mentés** > **Igen**lehetőséget.

   ![Felügyelt identitás beállításának kikapcsolása](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Üzembehelyezési sablon

Ha a logikai alkalmazás rendszerhez rendelt felügyelt identitását egy Azure Resource Manager telepítési sablonnal hozta létre, állítsa az `"identity"` `"type"` elem tulajdonságát `"None"`a következőre:. Ez a művelet törli a résztvevő AZONOSÍTÓját is az Azure AD-ből.

```json
"identity": {
   "type": "None"
}
```

## <a name="next-steps"></a>További lépések

* [Biztonságos hozzáférés és az adatAzure Logic Apps](../logic-apps/logic-apps-securing-a-logic-app.md)
