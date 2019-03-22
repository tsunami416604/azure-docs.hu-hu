---
title: Hitelesítés a felügyelt identitásokból – Azure Logic Apps |} A Microsoft Docs
description: A hitelesítést a bejelentkezés nélkül, létrehozhat egy felügyelt identitás (korábbi nevén a Felügyeltszolgáltatás-identitást vagy MSI), a logikai alkalmazás más erőforrások eléréséhez az Azure Active Directory (Azure AD) hitelesítő adatok és titkos kódok nélkül bérlők
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 01/22/2019
ms.openlocfilehash: 046aed64d3551d5c0b6ddae44b925452c01c297a
ms.sourcegitcommit: 02d17ef9aff49423bef5b322a9315f7eab86d8ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58337581"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Felügyelt identitások Azure Logic Apps-erőforrások eléréséhez és hitelesítéséhez

A többi Azure Active Directory (Azure AD) bérlő erőforrásaihoz, és a személyazonosság hitelesítéséhez bejelentkezés nélkül, használja a logikai alkalmazás egy [identitás](../active-directory/managed-identities-azure-resources/overview.md) (korábbi nevén a Felügyeltszolgáltatás-identitást vagy MSI), nem pedig hitelesítő adatok vagy titkos kulcsok. Az Azure felügyeli ezt az identitást, és segít biztosítani a hitelesítő adatait, mert nincs vagy titkos kulcsok elforgatása. Ez a cikk bemutatja, hogyan állíthat be, és a logikai alkalmazás alapértelmezett felügyelt identitás használatára. Felügyelt identitások kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitások?](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> Akkor is jelenleg legfeljebb 10 logikai alkalmazások munkafolyamataiba, a rendszer által hozzárendelt felügyelt minden egyes Azure-előfizetésben azonosítók.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetéssel, vagy ha Ön nem rendelkezik előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókkal</a>.

* A logikai alkalmazás, ahol szeretné használni, a rendszer által hozzárendelt felügyelt identitás. Ha a logikai alkalmazás nem rendelkezik, tekintse meg [az első logikai alkalmazás munkafolyamatának létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="enable-identity"></a>

## <a name="enable-managed-identity"></a>Felügyelt identitás engedélyezése

Felügyelt identitások rendszer által hozzárendelt nem kell manuálisan létrehozni az identitásukat. A rendszer által hozzárendelt felügyelt identitást a logikai alkalmazás állíthat be, használhatja az alábbi módszerek: 

* [Azure Portal](#azure-portal) 
* [Az Azure Resource Manager-sablonok](#template) 
* [Azure PowerShell](../active-directory/managed-identities-azure-resources/howto-assign-access-powershell.md) 

<a name="azure-portal"></a>

### <a name="azure-portal"></a>Azure Portal

Ahhoz, hogy a rendszer által hozzárendelt felügyelt identitás a logikai alkalmazás az Azure Portalon keresztül, kapcsolja be a **rendszerhez rendelt** a logikai alkalmazás nastavení identity beállítást.

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a logikai alkalmazás a Logikaialkalmazás-tervezőben.

1. A logikai alkalmazás menüjében alatt **beállítások**válassza **identitás**. 

1. A **rendszerhez rendelt** > **állapot**, válassza a **a**. Ezután válassza ki **mentése** > **Igen**.

   ![Kapcsolja be a felügyelt identitás beállítása](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

   A logikai alkalmazás most már rendelkezik egy az Azure Active Directoryban felügyelt rendszer által hozzárendelt identitások:

   ![GUID azonosítójú objektum](./media/create-managed-service-identity/object-id.png)

   | Tulajdonság | Érték | Leírás | 
   |----------|-------|-------------| 
   | **Objektumazonosító:** | <*identity-resource-ID*> | Egy globálisan egyedi azonosítóját (GUID), amely jelöli, a rendszer által hozzárendelt felügyelt identitás a logikai alkalmazás az Azure AD-bérlő | 
   ||| 

<a name="template"></a>

### <a name="azure-resource-manager-template"></a>Azure Resource Manager-sablon

Ha automatizálni szeretné létrehozni és üzembe helyezése Azure-erőforrások például a logic apps, [Azure Resource Manager-sablonok](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md). Alapértelmezett felügyelt identitás-sablon segítségével a logikai alkalmazás létrehozásához adja hozzá a `"identity"` elem és `"type"` tulajdonságát a logikai alkalmazás munkafolyamat-definíció a központi telepítési sablon: 

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

Ha az Azure létrehozza a logikai alkalmazást, a logikai alkalmazás munkafolyamat-definíció tartalmazza ezeket a további tulajdonságokat:

```json
"identity": {
   "type": "SystemAssigned",
   "principalId": "<principal-ID>",
   "tenantId": "<Azure-AD-tenant-ID>"
}
```

| Tulajdonság | Érték | Leírás | 
|----------|-------|-------------|
| **principalId** | <*principal-ID*> | Egy globálisan egyedi azonosítóját (GUID), amely a logikai alkalmazás az Azure AD-bérlőben, és néha "Objektumazonosító" néven jelenik meg, vagy `objectID` | 
| **tenantId** | <*Azure-AD-tenant-ID*> | Egy globálisan egyedi azonosítóját (GUID), amely az Azure AD-bérlővel, ahol a logikai alkalmazás most már a tagja. Az Azure AD-bérlőjében található egyszerű szolgáltatás rendelkezik a neve megegyezik a logikaialkalmazás-példányt. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Erőforrások eléréséhez a felügyelt identitással

Miután a rendszer által hozzárendelt felügyelt identitást hoz létre a logikai alkalmazás, [adott identitás hozzáférést biztosíthat más Azure-erőforrások](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Ezután használhatja az identitás a hitelesítéshez, mint minden más [szolgáltatásnév](../active-directory/develop/app-objects-and-service-principals.md). 

> [!NOTE]
> A rendszer által hozzárendelt felügyelt identitás és az erőforrást, amelyben hozzáférést hozzárendelni kívánt is rendelkeznie kell Azure-előfizetéshez.

### <a name="assign-access-to-managed-identity"></a>Hozzáférések hozzárendelése felügyelt identitás

Hozzáférés biztosítása a logikai alkalmazás alapértelmezett felügyelt identitás egy másik Azure-erőforráshoz, kövesse az alábbi lépéseket:

1. Az Azure Portalon lépjen az Azure-erőforrás, a felügyelt identitás hozzáférést hozzárendelni kívánt. 

1. Az erőforrás menüben válassza ki a **hozzáférés-vezérlés (IAM)**, és válassza a **szerepkör-hozzárendelés hozzáadása**. 

   ![Szerepkör-hozzárendelés hozzáadása](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. A **szerepkör-hozzárendelés hozzáadása**, jelölje be a **szerepkör** az identitást használni szeretne. 

1. Az a **rendelhet hozzáféréseket** tulajdonság, jelölje be **az Azure AD-felhasználó, csoport vagy szolgáltatásnév**, ha még nem lenne kiválasztva.

1. Az a **kiválasztása** rendszertől kezdve a logikai alkalmazás nevét, az első karakter mezőben adja meg a logikai alkalmazás nevét. Amikor megjelenik a logikai alkalmazást, válassza ki a logikai alkalmazást.

   ![Válassza ki a logikai alkalmazás felügyelt identitással](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Ha elkészült, kattintson a **Mentés** gombra.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>A logikai alkalmazás felügyelt identitás a hitelesítéshez

Miután beállította a logikai alkalmazás egy rendszer által hozzárendelt identitás és hozzáférés az erőforráshoz rendelt azt szeretné, hogy az identitáshoz tartozó, mostantól használhatja az identitás a hitelesítéshez. Használhatja például egy HTTP-művelet, így a logikai alkalmazás HTTP-kérelem küldése vagy adott erőforrásra irányuló hívás is. 

1. A logikai alkalmazást, adja hozzá a **HTTP** művelet. 

1. Adja meg a szükséges adatokat, hogy a művelet, például a kérés **metódus** és **URI** a hívni kívánt erőforrás helye.

   Tegyük fel például, használja az Azure Active Directory (Azure AD) hitelesítési [az Azure-szolgáltatások, amelyek támogatják az Azure ad-ben egyik](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). 
   Az a **URI** adja meg a végpont URL-címe az Azure szolgáltatás. 
   Tehát, ha az Azure Resource Manager használata esetén meg ezt az értéket a **URI** tulajdonság:

   `https://management.azure.com/subscriptions/<Azure-subscription-ID>?api-version-2016-06-01`

1. Válassza a HTTP-művelet **speciális beállítások megjelenítése**. 

1. Az a **hitelesítési** listáról válassza ki **felügyelt identitás**. Miután kiválasztotta a hitelesítés a **célközönség** tulajdonság jelenik meg, amelyen az erőforrás-Azonosítójának alapértelmezett értéke:

   ![Válassza a "Felügyelt identitás"](./media/create-managed-service-identity/select-managed-service-identity.png)

   > [!IMPORTANT]
   > 
   > Az a **célközönség** tulajdonság, az erőforrás-Azonosítójának értéke pontosan egyeznie kell azzal az Azure AD vár, így azokat is szükséges a záró perjeleket. 
   > Ez az ezen erőforrás-azonosító értékeinek található [táblázat: Azure szolgáltatásokban, amelyek támogatják az Azure AD](../active-directory/managed-identities-azure-resources/services-support-managed-identities.md#azure-services-that-support-azure-ad-authentication). 
   > Például ha Azure-erőforrással Manager erőforrás-Azonosítóját használja, győződjön meg arról, az URI-t rendelkezik záró perjellel.

1. Továbbra is a kívánt módon a logikai alkalmazás kiépítésének.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Távolítsa el a felügyelt identitás

A rendszer által hozzárendelt felügyelt identitás a logikai alkalmazás letiltásához lépésekkel a hasonló hogyan állíthatja be az identitást, az Azure portal, az Azure Resource Manager központi telepítési sablonok vagy az Azure PowerShell használatával. 

A logikai alkalmazás törlése, ha az Azure automatikusan eltávolítja a logikai alkalmazás alapértelmezett identitás az Azure ad-ből.

### <a name="azure-portal"></a>Azure Portal

Távolítsa el a rendszer által hozzárendelt felügyelt identitás a logikai alkalmazás az Azure Portalon keresztül, kapcsolja ki a **rendszerhez rendelt** a logikai alkalmazás nastavení identity beállítást.

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a logikai alkalmazás a Logikaialkalmazás-tervezőben.

1. A logikai alkalmazás menüjében alatt **beállítások**válassza **identitás**. 

1. A **rendszerhez rendelt** > **állapot**, válassza a **ki**. Ezután válassza ki **mentése** > **Igen**.

   ![Kapcsolja ki a felügyelt identitás beállítása](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Központi telepítési sablon

Ha felügyelt identitás a logikai alkalmazás alapértelmezett üzembe helyezés Azure Resource Manager-sablonnal létrehozott, állítsa be a `"identity"` elem `"type"` tulajdonságot `"None"`. Ez a művelet is törli a résztvevő-azonosító az Azure ad-ből. 

```json
"identity": {
   "type": "None"
}
```

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](https://aka.ms/logicapps-wish).