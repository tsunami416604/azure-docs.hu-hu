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
ms.date: 10/05/2018
ms.openlocfilehash: 84529e1097678ba7a039ffaeec57a9293c93dafd
ms.sourcegitcommit: fbdfcac863385daa0c4377b92995ab547c51dd4f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2018
ms.locfileid: "50229639"
---
# <a name="authenticate-and-access-resources-with-managed-identities-in-azure-logic-apps"></a>Felügyelt identitások Azure Logic Apps-erőforrások eléréséhez és hitelesítéséhez

A többi Azure Active Directory (Azure AD) bérlő erőforrásaihoz, és a személyazonosság hitelesítéséhez bejelentkezés nélkül, használja a logikai alkalmazás egy [identitás](../active-directory/managed-identities-azure-resources/overview.md) (korábbi nevén a Felügyeltszolgáltatás-identitást vagy MSI), nem pedig hitelesítő adatok vagy titkos kulcsok. Az Azure felügyeli ezt az identitást, és segít biztosítani a hitelesítő adatait, mert nincs vagy titkos kulcsok elforgatása. Ez a cikk bemutatja, hogyan hozhat létre, és a logikai alkalmazás alapértelmezett felügyelt identitás használatára. Felügyelt identitások kapcsolatos további információkért lásd: [Mi az Azure-erőforrások felügyelt identitások?](../active-directory/managed-identities-azure-resources/overview.md)

> [!NOTE]
> Akkor is jelenleg legfeljebb 10 logikai alkalmazások munkafolyamataiba, a rendszer által hozzárendelt felügyelt minden egyes Azure-előfizetésben azonosítók.

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetéssel, vagy ha Ön nem rendelkezik előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókkal</a>.

* A logikai alkalmazás, ahol szeretné használni, a rendszer által hozzárendelt felügyelt identitás. Ha a logikai alkalmazás nem rendelkezik, tekintse meg [az első logikai alkalmazás munkafolyamatának létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-identity"></a>

## <a name="create-managed-identity"></a>Hozzon létre felügyelt identitás

Hozzon létre, vagy egy rendszer által hozzárendelt felügyelt identitás engedélyezése a logikai alkalmazás az Azure portal, az Azure Resource Manager-sablonok vagy az Azure PowerShell használatával. 

### <a name="azure-portal"></a>Azure Portal

Ahhoz, hogy a rendszer által hozzárendelt felügyelt identitás a logikai alkalmazás az Azure Portalon keresztül, kapcsolja be a **regisztrálása az Azure Active Directory** beállítása az a logikai alkalmazás munkafolyamat-beállításokat.

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a logikai alkalmazás a Logikaialkalmazás-tervezőben.

1. Kövesse az alábbi lépéseket: 

   1. A logikai alkalmazás menüjében alatt **beállítások**válassza **munkafolyamat-beállítások**. 

   1. A **felügyeltszolgáltatás-identitás** > 
    **regisztrálása az Azure Active Directory**, válassza a **a**.

   1. Ha elkészült, válassza ki a **mentése** az eszköztáron.

      ![Kapcsolja be a felügyelt identitás beállítása](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

      A logikai alkalmazás most már rendelkezik egy ezeket a tulajdonságokat és értékeket az Azure Active Directoryban regisztrált felügyelt rendszer által hozzárendelt identitások:

      ![A résztvevő-azonosító és a bérlői azonosító GUID-azonosítói](./media/create-managed-service-identity/principal-tenant-id.png)

      | Tulajdonság | Érték | Leírás | 
      |----------|-------|-------------| 
      | **Résztvevő-azonosító** | <*résztvevő-azonosító*> | Egy globálisan egyedi azonosítóját (GUID), amely a logikai alkalmazás az Azure AD-bérlő | 
      | **Bérlő azonosítója** | <*Az Azure-AD-Bérlőazonosító*> | Egy globálisan egyedi azonosítóját (GUID), amely az Azure AD-bérlővel, ahol a logikai alkalmazás most már a tagja. Az Azure AD-bérlőjében található egyszerű szolgáltatás rendelkezik a neve megegyezik a logikaialkalmazás-példányt. | 
      ||| 

### <a name="deployment-template"></a>Központi telepítési sablon

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
| **principalId** | <*résztvevő-azonosító*> | Egy globálisan egyedi azonosítóját (GUID), amely a logikai alkalmazás az Azure AD-bérlőben | 
| **bérlő azonosítója** | <*Az Azure-AD-Bérlőazonosító*> | Egy globálisan egyedi azonosítóját (GUID), amely az Azure AD-bérlővel, ahol a logikai alkalmazás most már a tagja. Az Azure AD-bérlőjében található egyszerű szolgáltatás rendelkezik a neve megegyezik a logikaialkalmazás-példányt. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Erőforrások eléréséhez a felügyelt identitással

Miután a rendszer által hozzárendelt felügyelt identitást hoz létre a logikai alkalmazás, [adott identitás hozzáférést biztosíthat más Azure-erőforrások](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Ezután használhatja az identitás a hitelesítéshez, mint minden más [szolgáltatásnév](../active-directory/develop/app-objects-and-service-principals.md). 

> [!NOTE]
> A rendszer által hozzárendelt felügyelt identitás és az erőforrást, amelyben hozzáférést hozzárendelni kívánt is rendelkeznie kell Azure-előfizetéshez.

### <a name="assign-access-to-managed-identity"></a>Hozzáférések hozzárendelése felügyelt identitás

Hozzáférés biztosítása a logikai alkalmazás alapértelmezett felügyelt identitás egy másik Azure-erőforráshoz, kövesse az alábbi lépéseket:

1. Az Azure Portalon lépjen az Azure-erőforrás, a felügyelt identitás hozzáférést hozzárendelni kívánt. 

1. Az erőforrás menüben válassza ki a **hozzáférés-vezérlés (IAM)**, és válassza a **Hozzáadás**. 

   ![Engedélyek hozzáadása](./media/create-managed-service-identity/add-permissions-logic-app.png)

1. A **engedélyek hozzáadása**, jelölje be a **szerepkör** az identitást használni szeretne. 

1. Az a **rendelhet hozzáféréseket** tulajdonság, jelölje be **az Azure AD-felhasználó, csoport vagy alkalmazás**, ha még nem lenne kiválasztva.

1. Az a **kiválasztása** rendszertől kezdve a logikai alkalmazás nevét, az első karakter mezőben adja meg a logikai alkalmazás nevét. Amikor megjelenik a logikai alkalmazást, válassza ki a logikai alkalmazást.

   ![Válassza ki a logikai alkalmazás felügyelt identitással](./media/create-managed-service-identity/add-permissions-select-logic-app.png)

1. Ha elkészült, kattintson a **Mentés** gombra.

### <a name="authenticate-with-managed-identity-in-logic-app"></a>A logikai alkalmazás felügyelt identitás a hitelesítéshez

Miután beállította a logikai alkalmazás egy rendszer által hozzárendelt identitás és hozzáférés az erőforráshoz rendelt azt szeretné, hogy az identitáshoz tartozó, mostantól használhatja az identitás a hitelesítéshez. Használhatja például egy HTTP-művelet, így a logikai alkalmazás HTTP-kérelem küldése vagy adott erőforrásra irányuló hívás is. 

1. A logikai alkalmazást, adja hozzá a **HTTP** művelet. 

1. Adja meg a szükséges adatokat, hogy a művelet, például a kérés **metódus** és **URI** a hívni kívánt erőforrás helye.

1. Válassza a HTTP-művelet **speciális beállítások megjelenítése**. 

1. Az a **hitelesítési** listáról válassza ki **Felügyeltszolgáltatás-identitás**, amely majd bemutatja a **célközönség** tulajdonsága állítható be:

   ![Válassza a "Felügyelt Szolgáltatásidentitás"](./media/create-managed-service-identity/select-managed-service-identity.png)

1. Továbbra is a kívánt módon a logikai alkalmazás kiépítésének.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Távolítsa el a felügyelt identitás

A rendszer által hozzárendelt felügyelt identitás a logikai alkalmazás letiltásához lépésekkel a hasonló hogyan hozott létre az identitás, az Azure portal, az Azure Resource Manager központi telepítési sablonok vagy az Azure PowerShell használatával. 

A logikai alkalmazás törlése, ha az Azure automatikusan eltávolítja a logikai alkalmazás alapértelmezett identitás az Azure ad-ből.

### <a name="azure-portal"></a>Azure Portal

1. Logikaialkalmazás-Tervező nyissa meg a logikai alkalmazást.

1. Kövesse az alábbi lépéseket: 

   1. A logikai alkalmazás menüjében alatt **beállítások**válassza **munkafolyamat-beállítások**. 
   
   1. A **felügyeltszolgáltatás-identitás**, válassza a **ki** számára a **regisztrálása az Azure Active Directory** tulajdonság.

   1. Ha elkészült, válassza ki a **mentése** az eszköztáron.

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

