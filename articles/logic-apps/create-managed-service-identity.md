---
title: Elérheti és bejelentkezés – Azure Logic Apps használata nélkül végezzen hitelesítést |} A Microsoft Docs
description: Hozzon létre egy felügyelt identitás, hogy a logikai alkalmazás hitelesítheti és a többi Azure Active Directory (Azure AD) bérlő a hitelesítő adatok nélkül erőforrásaihoz
author: kevinlam1
ms.author: klam
ms.reviewer: estfan, LADocs
services: logic-apps
ms.service: logic-apps
ms.suite: integration
ms.topic: article
ms.date: 09/24/2018
ms.openlocfilehash: fb1c31e6e7c075e20191a4e51d7b1a9323f3b979
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46973966"
---
# <a name="access-resources-and-authenticate-as-managed-identities-in-azure-logic-apps"></a>Erőforrások eléréséhez, és hitelesítse magát az Azure Logic Appsben a felügyelt identitásokból

A többi Azure Active Directory (Azure AD) bérlő erőforrásaihoz, és a személyazonosság hitelesítéséhez bejelentkezés nélkül, létrehozhat egy [identitás](../active-directory/managed-identities-azure-resources/overview.md) , amely a logikai alkalmazás helyett a hitelesítő adatait használja. Az Azure felügyeli ezt az identitást, és segít biztosítani a hitelesítő adatait, mert nincs vagy titkos kulcsok elforgatása. Ez a cikk bemutatja, hogyan hozhat létre, és a egy felügyelt identitás használata a logikai alkalmazás. További információkért lásd: [identitások az Azure-erőforrások kezeléséhez](../app-service/app-service-managed-service-identity.md).

> [!NOTE]
> Felügyelt identitások Azure-erőforrások számára a helyettesítő név, a szolgáltatás-ként ismert, Felügyeltszolgáltatás-identitás (MSI).

## <a name="prerequisites"></a>Előfeltételek

* Azure-előfizetéssel, vagy ha Ön nem rendelkezik előfizetéssel, <a href="https://azure.microsoft.com/free/" target="_blank">regisztráljon egy ingyenes Azure-fiókkal</a>.

* A logikai alkalmazás, ahol a felügyelt identitást használni kívánt. Ha a logikai alkalmazás nem rendelkezik, tekintse meg [az első logikai alkalmazás munkafolyamatának létrehozása](../logic-apps/quickstart-create-first-logic-app-workflow.md).

<a name="create-identity"></a>

## <a name="create-managed-identity"></a>Hozzon létre felügyelt identitás

Hozzon létre, vagy egy felügyelt identitás engedélyezése a logikai alkalmazás az Azure portal, az Azure Resource Manager-sablonok vagy az Azure PowerShell használatával. 

### <a name="azure-portal"></a>Azure Portal

Hozzon létre egy felügyelt identitás, a logikai alkalmazás az Azure Portalon keresztül, kapcsolja be a **regisztrálása az Azure Active Directory** beállítása az a logikai alkalmazás munkafolyamat-beállításokat.

1. Az a [az Azure portal](https://portal.azure.com), nyissa meg a logikai alkalmazás a Logikaialkalmazás-tervezőben.

1. Kövesse az alábbi lépéseket: 

   1. A logikai alkalmazás menüjében alatt **beállítások**válassza **munkafolyamat-beállítások**. 

   1. A **felügyeltszolgáltatás-identitás** > 
    **regisztrálása az Azure Active Directory**, válassza a **a**.

   1. Ha elkészült, válassza ki a **mentése** az eszköztáron.

      ![Kapcsolja be a felügyelt identitás beállítása](./media/create-managed-service-identity/turn-on-managed-service-identity.png)

      Azure mostantól ezek a tulajdonságok és a logikai alkalmazás felügyelt identitás értékeit jeleníti meg:

      ![A résztvevő-azonosító és a bérlői azonosító GUID-AZONOSÍTÓI](./media/create-managed-service-identity/principal-tenant-id.png)

      | Tulajdonság | Érték | Leírás | 
      |----------|-------|-------------| 
      | **Résztvevő-azonosító** | <*résztvevő-azonosító – GUID*> | Egy globálisan egyedi azonosítóját (GUID), amely a logikai alkalmazás az Azure AD-bérlő | 
      | **Bérlő azonosítója** | <*Azure-AD-bérlő – azonosító GUID-azonosítója*> | Egy globálisan egyedi azonosítóját (GUID), amely az Azure AD-bérlővel, ahol a logikai alkalmazás most már a tagja. Az Azure AD-bérlőjében található egyszerű szolgáltatás rendelkezik a neve megegyezik a logikaialkalmazás-példányt. | 
      ||| 

### <a name="deployment-template"></a>Központi telepítési sablon

Automatizálhatja a létrehozása és üzembe helyezése Azure-erőforrások például a logic apps, állíthat be az Azure Resource Manager-sablonok. További információkért lásd: [létrehozása és üzembe helyezése Azure Resource Manager-sablonokkal rendelkező logikai alkalmazások](../logic-apps/logic-apps-create-deploy-azure-resource-manager-templates.md). 

A logikai alkalmazás egy sablon segítségével felügyelt identitás létrehozásához adja hozzá a **identitás** elem és **típus** a logikai alkalmazás munkafolyamat-definíció a központi telepítési sablont a tulajdonságot. Ezek a beállítások jelzik, hogy az Azure létrehozza és felügyeli a logikai alkalmazás ezt az identitást:

```json
"identity": {
    "type": "SystemAssigned"
}
```

Ha például a logikai alkalmazás ezen verziója hasonló lehet:

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
    "principalId": "<principal-ID-GUID>",
    "tenantId": "<Azure-AD-tenant-ID>-GUID"
}
```

| Tulajdonság | Érték | Leírás | 
|----------|-------|-------------|
| **principalId** | <*résztvevő-azonosító – GUID*> | Egy globálisan egyedi azonosítóját (GUID), amely a logikai alkalmazás az Azure AD-bérlőben | 
| **bérlő azonosítója** | <*Azure-AD-bérlő – azonosító GUID-azonosítója*> | Egy globálisan egyedi azonosítóját (GUID), amely az Azure AD-bérlővel, ahol a logikai alkalmazás most már a tagja. Az Azure AD-bérlőjében található egyszerű szolgáltatás rendelkezik a neve megegyezik a logikaialkalmazás-példányt. | 
||| 

<a name="access-other-resources"></a>

## <a name="access-resources-with-managed-identity"></a>Erőforrások eléréséhez a felügyelt identitással

A logikai alkalmazás létrehoz egy felügyelt identitás, miután [adott identitás hozzáférést biztosíthat más erőforrásokhoz](../active-directory/managed-identities-azure-resources/howto-assign-access-portal.md). Ezután használhatja az adott felügyelt identitás a hitelesítéshez, mint minden más [szolgáltatásnév](../active-directory/develop/app-objects-and-service-principals.md). 

Tegyük fel, hogy már beállította egy felügyelt identitás, amely egy másik erőforrás hozzáfér a logikai alkalmazást. Most már hozzáadhat egy HTTP-művelet, így a logikai alkalmazás HTTP-kérést küldött és az adott erőforrásra irányuló hívás. 

1. A logikai alkalmazást, adja hozzá a **HTTP** művelet. 

1. Adja meg a szükséges adatokat, hogy a művelet, például a kérés **metódus** és **URI** a hívni kívánt erőforrás helye.

1. Válassza a HTTP-művelet **speciális beállítások megjelenítése**. 

1. Az a **hitelesítési** listáról válassza ki **Felügyeltszolgáltatás-identitás**, amely majd bemutatja a **célközönség** tulajdonsága állítható be:

   ![Válassza a "Felügyelt Szolgáltatásidentitás"](./media/create-managed-service-identity/select-managed-service-identity.png)

1. Továbbra is a kívánt módon a logikai alkalmazás kiépítésének.

<a name="remove-identity"></a>

## <a name="remove-managed-identity"></a>Távolítsa el a felügyelt identitás

Egy felügyelt identitás, az a logikai alkalmazás letiltásához lépésekkel a hasonló hogyan hozott létre az identitás, az Azure portal, az Azure Resource Manager központi telepítési sablonok vagy az Azure PowerShell használatával. 

A logikai alkalmazás törlése, ha az Azure automatikusan eltávolítja a logikai alkalmazás alapértelmezett identitás az Azure ad-ből.

### <a name="azure-portal"></a>Azure Portal

1. Logikaialkalmazás-Tervező nyissa meg a logikai alkalmazást.

1. Kövesse az alábbi lépéseket: 

   1. A logikai alkalmazás menüjében alatt **beállítások**válassza **munkafolyamat-beállítások**. 
   
   1. A **felügyeltszolgáltatás-identitás**, válassza a **ki** számára a **regisztrálása az Azure Active Directory** tulajdonság.

   1. Ha elkészült, válassza ki a **mentése** az eszköztáron.

      ![Kapcsolja ki a felügyelt identitás beállítása](./media/create-managed-service-identity/turn-off-managed-service-identity.png)

### <a name="deployment-template"></a>Központi telepítési sablon

Üzembe helyezés Azure Resource Manager-sablonnal létrehozta a logikai alkalmazás felügyelt identitás, állítsa be a `"identity"` elem `"type"` tulajdonságot `"None"`. Ez a művelet is törli a résztvevő-azonosító az Azure ad-ből. 

```json
"identity": {
    "type": "None"
}
```

## <a name="get-support"></a>Támogatás kérése

* A kérdéseivel látogasson el az [Azure Logic Apps fórumára](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* A funkciókkal kapcsolatos ötletek elküldéséhez vagy megszavazásához látogasson el a [Logic Apps felhasználói visszajelzéseinek oldalára](http://aka.ms/logicapps-wish).
