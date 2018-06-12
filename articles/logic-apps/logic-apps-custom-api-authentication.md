---
title: Hitelesítés hozzáadása egyéni API - Azure Logic Apps |} Microsoft Docs
description: Az egyéni API-kat a logic Apps alkalmazásokból hívások hitelesítés beállítása
author: ecfan
manager: jeconnoc
editor: ''
services: logic-apps
documentationcenter: ''
ms.assetid: ''
ms.service: logic-apps
ms.workload: logic-apps
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/22/2017
ms.author: LADocs; estfan
ms.openlocfilehash: 705abb2a3cc25c965bdce364eb169b4e3a814bff
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35298549"
---
# <a name="secure-calls-to-your-custom-apis-from-logic-apps"></a>Az egyéni API-kat a logic Apps alkalmazásokból biztonságos hívások

Az API-kat hívásainak védelme érdekében állíthat be Azure Active Directory (Azure AD-) hitelesítés az Azure portálon keresztül így nem kell frissíteni a kódot. Alternatív megoldásként a hitelesítés előírásához és kényszerítéséhez az API kódját is használhatja.

## <a name="authentication-options-for-your-api"></a>Az API-hitelesítési beállítások

Az alábbi módszerek az egyéni API-hívások biztonságossá teheti:

* [Nincs kódmódosításokat](#no-code): az API-t védelme [Azure Active Directory (Azure AD)](../active-directory/active-directory-whatis.md) az Azure-portálon, így nem kell frissítse a kódot, vagy telepítse újra az API-t.

  > [!NOTE]
  > Alapértelmezés szerint az Azure AD-alapú hitelesítés, amely az Azure portálon kapcsolja be a minden részletre kiterjedő engedélyezési nem biztosít. Ez a hitelesítés például az API-t egy adott bérlő, nem pedig egy adott felhasználó vagy alkalmazás zárolja. 

* [Frissítse az API-kódot](#update-code): az API védelme kényszerítésével [tanúsítványhitelesítés](#certificate), [az egyszerű hitelesítés](#basic), vagy [az Azure AD-alapú hitelesítés](#azure-ad-code) keresztül a kódot.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Az API-hívások hitelesítéséhez programkód módosítása nélkül

Ehhez a metódushoz általános lépései a következők:

1. Hozzon létre két Azure Active Directory (Azure AD) alkalmazás identitások: egy a Logic Apps alkalmazást, egy, a webes alkalmazás (vagy API-alkalmazás).

2. Az API-hívások hitelesítéséhez, a hitelesítő adatait (ügyfél-Azonosítót és titkos kulcs) használni a szolgáltatás egyszerű a Logic Apps alkalmazást az Azure AD alkalmazás identitásának társított.

3. Vegye fel a Alkalmazásazonosítók a logic app-definíciót.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>1. lépés: Hozzon létre egy Azure AD alkalmazás-azonosítót a logikai alkalmazásnak

A Logic Apps alkalmazást az Azure AD alkalmazás identitását használja az Azure AD hitelesítő. Csak kell beállítania a identitást egy alkalommal a címtáron. Például is választja a logic apps ugyanazzal az identitással használandó annak ellenére, hogy minden logikai alkalmazás egyedi azonosítók hozhat létre. Állítsa be ezeket az identitásokat az Azure portálon, vagy használjon [PowerShell](#powershell).

**A logikai alkalmazásnak az alkalmazásazonosító létrehozása az Azure-portálon**

1. Az a [Azure-portálon](https://portal.azure.com "https://portal.azure.com"), válassza a **Azure Active Directory**. 

2. Győződjön meg arról, hogy a webes alkalmazás vagy API-alkalmazás könyvtárába van-e.

   > [!TIP]
   > Könyvtárak váltáshoz válassza ki a profilt, és válasszon másik címtárat. Másik lehetőségként válasszon **áttekintése** > **kapcsoló directory**.

3. A directory menü alatt **kezelése**, válassza a **App regisztrációk** > **új alkalmazás regisztrációja**.

   > [!TIP]
   > Alapértelmezés szerint a regisztrációk alkalmazáslistájában összes alkalmazás regisztrációja megjelenik a könyvtárban. Válassza ki, ha csak az alkalmazás regisztrációk mellett a keresőmezőbe, **alkalmazásaimat**. 

   ![Hozzon létre új alkalmazás regisztrálása](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Nevezze el az alkalmazás azonosítóját, hagyja **alkalmazástípus** beállítása **webalkalmazás / API**, adjon meg egy egyedi karakterláncot formázni egy tartományt a **bejelentkezési URL-cím**, és válassza a **létrehozása**.

   ![Adjon meg nevet és a bejelentkezés URL-címet az alkalmazás-azonosító](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   Az alkalmazás azonosítóját a Logic Apps alkalmazást most létrehozott alkalmazást regisztrációk listájában jelenik meg.

   ![A logikai alkalmazásnak identitása](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Az alkalmazás regisztrációk listában válassza ki az új alkalmazás azonosítója. Másolja ki és mentse a **Alkalmazásazonosító** használja az "ügyfél-azonosító" a Logic Apps alkalmazást a 3. rész.

   ![Másolja ki és mentse a logikai alkalmazás azonosítója](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Ha az alkalmazás identitását beállításait nem látható, válassza a **beállítások** vagy **összes beállítás**.

7. A **API-hozzáférés**, válassza a **kulcsok**. A **leírás**, adja meg a kulcs nevét. A **Expires**, válasszon egy időtartamot a kulcshoz.

   A létrehozni kívánt kulcs az Alkalmazásidentitás "titkos" vagy a logikai alkalmazásnak jelszó funkcionál.

   ![Logic app identitás-kulcs létrehozása](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Az eszköztáron válassza **mentése**. A **érték**, a kulcs csomópontként jelenik meg. 
**Ügyeljen arra, hogy másolja ki és mentse a kulcsot** későbbi használatra, mert a kulcs rejtett ha hagyja a **kulcsok** lap.

   A Logic Apps alkalmazást a 3. rész konfigurálásakor meg kell adnia ezt a kulcsot, mint a "secret" vagy a jelszó.

   ![Másolja ki és mentse a kulcsot későbbi használatra](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**A logikai alkalmazásnak az alkalmazásazonosító létrehozása a PowerShell**

Ez a feladat keresztül Azure Resource Manager PowerShell használatával végezheti el. A PowerShellben futtassa a következő parancsokat:

1. `Switch-AzureMode AzureResourceManager`

2. `Add-AzureAccount`

3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password "identity-password"`

4. Ügyeljen arra, hogy másolja a **Bérlőazonosító** (az Azure AD-bérlő GUID), a **Alkalmazásazonosító**, és a használt jelszót.

További információ megtudhatja, hogyan [egyszerű szolgáltatás létrehozása a PowerShell segítségével férhetnek hozzá az erőforrásokhoz](../azure-resource-manager/resource-group-authenticate-service-principal.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>2. lépés: Hozzon létre egy Azure AD alkalmazás-azonosítót a webes alkalmazás vagy API-alkalmazás

Ha a webes alkalmazás vagy API-alkalmazás már telepítve van, kapcsolja be a hitelesítés, és az alkalmazásazonosító létrehozása az Azure portálon. Ellenkező esetben is [Azure Resource Manager-sablon telepítésekor hitelesítés bekapcsolásához](#authen-deploy). 

**Az alkalmazásazonosító létrehozásához, és központilag telepített alkalmazások az Azure portálon hitelesítés bekapcsolása**

1. Az a [Azure-portálon](https://portal.azure.com "https://portal.azure.com"), található, és válassza ki a webalkalmazás vagy API-alkalmazásba. 

2. A **beállítások**, válassza a **hitelesítési/engedélyezési**. A **App Service hitelesítés**, kapcsolja be a hitelesítési **a**. A **hitelesítésszolgáltatókat**, válassza a **Azure Active Directory**.

   ![Hitelesítés bekapcsolása](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Most létrehoz egy alkalmazás azonosítóját, a webes alkalmazás vagy API-alkalmazás, ahogy az itt látható. Az a **Azure Active Directory beállításai** lap **üzemmód** való **Express**. Válasszon **új AD-alkalmazás létrehozása**. Nevezze el az alkalmazás azonosítóját, és válassza a **OK**. 

   ![Az Alkalmazásidentitás a webes alkalmazás vagy API-alkalmazás létrehozása](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. A **Hitelesítés/Engedélyezés** lapon kattintson a **Mentés** gombra.

Most meg kell keresnie az ügyfél és bérlői azonosító az alkalmazásazonosító, amely a webes alkalmazás vagy API-alkalmazás van társítva. Ezek az azonosítók használhatja a 3. rész. Így folytatja ezeket a lépéseket az Azure-portálon.

**Alkalmazás azonosítója ügyfél-azonosító és Bérlőazonosító keresése a webes alkalmazás vagy API-alkalmazás az Azure-portálon**

1. A **hitelesítésszolgáltatókat**, válassza a **Azure Active Directory**. 

   ![Válassza az „Azure Active Directory” elemet](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Az a **Azure Active Directory beállításai** lap **üzemmód** való **speciális**.

3. Másolás a **ügyfél-azonosító**, és mentse a GUID azonosítóját használja a 3. rész.

   > [!TIP] 
   > Ha **ügyfél-azonosító** és **kiállítójának URL-címe** nem jelenik meg, próbálja meg frissíteni az Azure-portálon, és ismételje meg az 1.

4. A **kiállítójának URL-címe**, másolja ki és mentse a GUID csak a 3. rész. Is használhatja a GUID a webalkalmazás vagy API-alkalmazás központi telepítési sablont, ha szükséges.

   A GUID az adott bérlő GUID-azonosító ("bérlő azonosítója"), és meg kell jelennie az URL-cím: `https://sts.windows.net/{GUID}`

5. A módosítások mentése nélkül zárja be a **Azure Active Directory beállításai** lap.

<a name="authen-deploy"></a>

**Amikor telepít egy Azure Resource Manager sablonnal hitelesítés bekapcsolása**

A logikai alkalmazásnak az alkalmazás identitását továbbra is készítsen a webes alkalmazás vagy API-alkalmazást, amely eltér az Azure AD alkalmazás identitást. Az alkalmazásazonosító létrehozásához kövesse az előző lépést a 2. rész a az Azure-portálon. 

Is is hajtsa végre az 1. rész utasításait, de ügyeljen arra, hogy a webes alkalmazás vagy API-alkalmazás tényleges `https://{URL}` a **bejelentkezési URL-cím** és **App ID URI**. A fenti lépéseket hogy menti az ügyfél-azonosító és a bérlő azonosítója használható az alkalmazás központi telepítési sablont és a 3. rész.

> [!NOTE]
> Az Azure AD identitása a webes alkalmazás vagy API-alkalmazás létrehozásakor az Azure-portálon nem PowerShell kell használnia. A PowerShell-parancsmag segítségével nem állítsa be a szükséges engedélyekkel a felhasználók bejelentkeznek egy webhelyet.

Az ügyfél és bérlői azonosító kap, miután tartalmazza ezek az azonosítók egy subresource a webalkalmazás vagy a központi telepítési sablont API-alkalmazásba:

``` json
"resources": [ {
    "apiVersion": "2015-08-01",
    "name": "web",
    "type": "config",
    "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
    "properties": {
        "siteAuthEnabled": true,
        "siteAuthSettings": {
            "clientId": "{client-ID}",
            "issuer": "https://sts.windows.net/{tenant-ID}/",
        }
    }
} ]
```

Automatikus telepítése egy üres web app és az Azure Active Directory-hitelesítés, és logikai alkalmazás [sablon megjelenítése a teljes Itt](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json), vagy kattintson a **az Azure telepítéséhez** itt:

[![Üzembe helyezés az Azure-ban](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>3. lépés: A Logic Apps alkalmazást engedélyezési című feltöltése

Az előző címsablonnak már beállítása engedélyezési ismertetjük, de ha közvetlenül a logic app, meg kell adni a teljes körű engedéllyel szakaszban.

Nyissa meg a logic app-definíciót a kód nézetre, lépjen a **HTTP** művelet a szakaszban található a **engedélyezési** szakaszt, és ezt a sort tartalmaz:

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| Elem | Szükséges | Leírás | 
| ------- | -------- | ----------- | 
| bérlő | Igen | Az Azure AD-bérlő GUID azonosítója | 
| Célközönség | Igen | A GUID azonosítóját a cél erőforráson, amelyet hozzá szeretne férni, ami a webalkalmazás vagy API-alkalmazás az alkalmazásidentitás az ügyfél-azonosító | 
| clientId | Igen | Az ügyfél hozzáférést igényelnek, amely az ügyfél-Azonosítót a logikai alkalmazásnak az alkalmazásidentitás a GUID azonosítója | 
| titkos kód | Igen | A kulcs és a jelszót az alkalmazásazonosító, hogy az ügyfél által kért a hozzáférési jogkivonat | 
| type | Igen | A hitelesítési típus. ActiveDirectoryOAuth hitelesítéshez értéke `ActiveDirectoryOAuth`. | 
|||| 

Példa:

``` json
{
   "actions": {
      "some-action": {
         "conditions": [],
         "inputs": {
            "method": "post",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         },
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Kód biztonságos API-hívások

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Tanúsítványhitelesítés

A bejövő kéréseket a Logic Apps alkalmazást-webalkalmazást vagy API-alkalmazás az érvényesítéshez ügyfél tanúsítványokat is használhat. Állítsa be a kódját, ismerje meg [TLS kölcsönös hitelesítés konfigurálásával](../app-service/app-service-web-configure-tls-mutual-auth.md).

Az a **engedélyezési** szakaszban, ezt a sort tartalmaz: 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| Elem | Szükséges | Leírás | 
| ------- | -------- | ----------- | 
| type | Igen | A hitelesítési típus. Az ügyfél SSL-tanúsítványok, az értéknek kell lennie `ClientCertificate`. | 
| jelszó | Igen | Az ügyféltanúsítvány (PFX-fájl) eléréséhez szükséges jelszót | 
| pfx | Igen | A base64-kódolású tartalmak az ügyféltanúsítvány (PFX-fájl) | 
|||| 

<a name="basic"></a>

#### <a name="basic-authentication"></a>Alapszintű hitelesítés

Ellenőrizze a webalkalmazás vagy API-alkalmazás a Logic Apps alkalmazást a bejövő kéréseket, alapszintű hitelesítés, mint például a felhasználónév és jelszó is használhat. Az egyszerű hitelesítés egy közös mintát, és a hitelesítés a webalkalmazás vagy API-alkalmazás létrehozásához használt nyelvi is használhatja.

Az a **engedélyezési** szakaszban, ezt a sort tartalmaz:

`{"type": "basic", "username": "username", "password": "password"}`.

| Elem | Szükséges | Leírás | 
| ------- | -------- | ----------- | 
| type | Igen | A használni kívánt hitelesítési típussal. Az alapszintű hitelesítés, az értéknek kell lennie `Basic`. | 
| felhasználónév | Igen | A hitelesítéshez használni kívánt felhasználónevet | 
| jelszó | Igen | A hitelesítéshez használni kívánt jelszót | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Az Azure Active Directory hitelesítési kód

Alapértelmezés szerint az Azure AD-alapú hitelesítés, amely az Azure portálon kapcsolja be a minden részletre kiterjedő engedélyezési nem biztosít. Ez a hitelesítés például az API-t egy adott bérlő, nem pedig egy adott felhasználó vagy alkalmazás zárolja. 

API-hozzáférés korlátozása kód a Logic Apps alkalmazást, bontsa ki a fejlécet, amely rendelkezik a JSON webes jogkivonat (JWT). Ellenőrizze a hívó identitását, és nem egyezik a kérelmeket.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/app-service-authentication-overview.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>További lépések

* [Központi telepítése, és hívja meg egyedi API-kat a logic app munkafolyamatok](../logic-apps/logic-apps-custom-api-host-deploy-call.md)