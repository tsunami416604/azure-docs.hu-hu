---
title: Hitelesítés hozzáadása az egyéni API-k – Azure Logic Apps |} A Microsoft Docs
description: Az Azure Logic Apps egyéni API-k hívása a hitelesítés beállítása
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: klam, LADocs
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: d83a27d87ffadd15a27196a11ae3f69d84232efa
ms.sourcegitcommit: 549070d281bb2b5bf282bc7d46f6feab337ef248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/21/2018
ms.locfileid: "53719598"
---
# <a name="secure-calls-to-custom-apis-from-azure-logic-apps"></a>Az Azure Logic Apps egyéni API-k biztonságos hívások

Az API-hívások biztonságossá tételéhez, állíthat be az Azure Active Directory (Azure AD-) hitelesítést az Azure Portalon keresztül, nem kell a kód frissítése. Alternatív megoldásként a hitelesítés előírásához és kényszerítéséhez az API kódját is használhatja.

## <a name="authentication-options-for-your-api"></a>Hitelesítési lehetőségek az API-hoz

A következő lehetőségeket biztosítva az egyéni API-hívások tehetők biztonságossá:

* [Kódmódosítás nélkül](#no-code): Az API védelme az [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) az Azure Portalon, így nem kell a kód frissítése és ismételt üzembe helyezése az API-t.

  > [!NOTE]
  > Alapértelmezés szerint az Azure AD-hitelesítés, amely az Azure Portalon kapcsolja be a minden részletre kiterjedő engedélyezési nem biztosít. A hitelesítés például az API-t egy adott bérlővel, nem pedig egy adott felhasználó vagy alkalmazás zárolja magát. 

* [Az API kódjának frissítése](#update-code): Az API védelme kényszerítésével [Tanúsítványalapú hitelesítés](#certificate), [alapszintű hitelesítés](#basic), vagy [Azure AD-hitelesítés](#azure-ad-code) kódok segítségével.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Az API-hívások hitelesítéséhez a programkód módosítása nélkül

Ezzel a módszerrel az általános lépései a következők:

1. Hozzon létre két Azure Active Directory (Azure AD) application identitások: egy a logikai alkalmazást, egy, a web app (vagy API-alkalmazás).

2. Az API-hívások hitelesítést végezni, használja a hitelesítő adatokat (ügyfél-azonosító és titkos kulcs) a logikai alkalmazás az Azure AD alkalmazás identitáshoz tartozó egyszerű szolgáltatás számára.

3. Vegye fel az alkalmazásazonosítót a logikai alkalmazás definíciójának.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>1. rész: Hozzon létre egy Azure AD alkalmazás-azonosítót a logikai alkalmazás

A logikai alkalmazás hitelesítése az Azure ad-ben a az Azure AD alkalmazás-identitást használja. Csak akkor állítsa be ezt az identitást a címtár egy alkalommal. Például akkor lehet váltani, ugyanazzal az identitással használata a logic apps annak ellenére, hogy minden egyes logikai alkalmazás egyedi azonosítók is létrehozhat. Állítsa be ezeket az identitásokat az Azure Portalon, vagy használjon [PowerShell](#powershell).

**Az alkalmazásazonosító a logikai alkalmazás létrehozása az Azure Portalon**

1. Az a [az Azure portal](https://portal.azure.com "https://portal.azure.com"), válassza a **Azure Active Directory**. 

2. Győződjön meg arról, hogy használja-e a webalkalmazás vagy API-alkalmazás ugyanabban a címtárban.

   > [!TIP]
   > Válthat a címtárak, válassza ki a profilt, és válasszon egy másik címtárban. Vagy válasszon **áttekintése** > **címtár váltása**.

3. A címtár menü alatt **kezelés**, válassza a **alkalmazásregisztrációk** > **új alkalmazásregisztráció**.

   > [!TIP]
   > Alapértelmezés szerint a regisztrációk listáját a címtár összes alkalmazásregisztrációk jeleníti meg. A keresőmező melletti csak az alkalmazásregisztrációk megtekintéséhez jelölje ki **saját alkalmazások**. 

   ![Hozzon létre új alkalmazás regisztrálása](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Nevezze el az alkalmazás azonosítóját, és távozzon **alkalmazástípus** beállítása **webalkalmazás / API**, adjon meg egy egyedi karakterlánc formátumú esetében – tartomány **bejelentkezési URL-**, ,majd **Hozzon létre**.

   ![Adjon meg nevet és a bejelentkezés URL-címet az alkalmazás azonosítója](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   Az alkalmazás azonosítója, amelyet a logikai alkalmazás most már létrehozott alkalmazás regisztrációk listájában jelenik meg.

   ![A logikai alkalmazás identita aplikace](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Az alkalmazás regisztrációk listában válassza ki az új alkalmazás azonosítóját. Másolja ki és mentse a **Alkalmazásazonosító** a logikai alkalmazás a 3. rész az "ügyfél-azonosító" használandó.

   ![Másolja ki és mentse a logikai alkalmazás Alkalmazásazonosító](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Ha az alkalmazás identitását beállítások nem láthatók, válassza ki a **beállítások** vagy **minden beállítás**.

7. A **API-hozzáférés**, válassza a **kulcsok**. A **leírás**, adja meg a kulcs nevét. A **lejárat**, válasszon ki egy időtartamot a kulcshoz.

   A kulcs, amelyet hoz létre az Alkalmazásidentitás "secret" vagy a jelszó a logikai alkalmazás funkcionál.

   ![Logic app-identitás-kulcs létrehozása](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Az eszköztáron válassza **mentése**. A **érték**, most már megjelenik a kulcsot. 
**Győződjön meg arról, hogy kimásolja és elmentse a kulcs** későbbi használatra, mert a kulcs rejtett amikor hagyja a **kulcsok** lapot.

   A logikai alkalmazás a 3. rész konfigurálásakor meg kell adnia ezt a kulcsot, mint a "secret" vagy a jelszó.

   ![Másolja ki és mentse a kulcsot későbbi használatra](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Az alkalmazásazonosító a logikai alkalmazás létrehozása a PowerShellben**

Ez a feladat Azure Resource Managerben a PowerShell használatával is elvégezheti. A PowerShellben futtassa a következő parancsokat:

1. `Add-AzureRmAccount`

2. `$SecurePassword = Read-Host -AsSecureString` (Adja meg a jelszót, és az enter)

3. `New-AzureRmADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

4. Feltétlenül másolja az **Bérlőazonosító** (az Azure AD-bérlőhöz tartozó GUID), a **Alkalmazásazonosító**, és a használt jelszót.

További információ megtudhatja, hogyan [egyszerű szolgáltatás létrehozása erőforrások eléréséhez a PowerShell használatával](../active-directory/develop/howto-authenticate-service-principal-powershell.md).

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>2. rész: Hozzon létre egy Azure AD alkalmazás-azonosítót a webalkalmazás vagy API-alkalmazás

Ha a webalkalmazás vagy API-alkalmazás már üzembe helyezte, kapcsolja be a hitelesítést, és az alkalmazásazonosító létrehozása az Azure Portalon. Egyéb esetben [kapcsolja be a hitelesítést, ha telepít egy Azure Resource Manager-sablonnal](#authen-deploy). 

**Az alkalmazásazonosító létrehozásához, és kapcsolja be a hitelesítést az Azure Portalon üzembe helyezett alkalmazások**

1. Az a [az Azure portal](https://portal.azure.com "https://portal.azure.com"), keresse meg és válassza ki a webalkalmazás vagy API-alkalmazás. 

2. A **beállítások**, válassza a **hitelesítés/engedélyezés**. A **App Service-hitelesítés**, kapcsolja be a hitelesítési **a**. A **hitelesítésszolgáltatók**, válassza a **Azure Active Directory**.

   ![Kapcsolja be a hitelesítést](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Most hozzon létre egy alkalmazás azonosítóját a webalkalmazás vagy API-alkalmazás, itt látható módon. Az a **Azure Active Directory-beállítások** lap **felügyeleti mód** való **Express**. Válasszon **új AD-alkalmazás létrehozása**. Nevezze el az alkalmazás azonosítóját, és válassza a **OK**. 

   ![Identita aplikace a webalkalmazás vagy API-alkalmazás létrehozása](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. A **Hitelesítés/Engedélyezés** lapon kattintson a **Mentés** gombra.

Most meg kell keresnie az identitása, amelyhez társítva van a webalkalmazás vagy API-alkalmazás az ügyfél Azonosítóját és bérlőazonosítóját. 3. rész azonosítóit a részletekben fogja használni. Így továbbra is az ezeket a lépéseket az Azure Portalon.

**Identita aplikace ügyfél-azonosítója és bérlő azonosítója a webalkalmazás vagy API-alkalmazás keresése az Azure Portalon**

1. A **hitelesítésszolgáltatók**, válassza a **Azure Active Directory**. 

   ![Válassza az „Azure Active Directory” elemet](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Az a **Azure Active Directory-beállítások** lap **felügyeleti mód** való **speciális**.

3. Másolás a **ügyfél-azonosító**, és mentse a GUID Azonosítót használja a 3. rész.

   > [!TIP] 
   > Ha **ügyfél-azonosító** és **kiállítójának URL-címe** nem jelenik meg, próbálja meg frissíteni az Azure Portalon, és ismételje meg az 1.

4. A **kiállítójának URL-címe**, másolja és mentse a GUID csak a 3. rész. Használhatja a GUID a webalkalmazás vagy API-alkalmazás központi telepítési sablont, ha szükséges.

   A GUID az egyes bérlők GUID ("bérlő azonosítója"), és meg kell jelennie az URL-cím: `https://sts.windows.net/{GUID}`

5. A módosítások mentése nélkül bezárja a **Azure Active Directory-beállítások** lapot.

<a name="authen-deploy"></a>

**Kapcsolja be a hitelesítést, ha telepít egy Azure Resource Manager-sablonnal**

Kell továbbra is létrehozhat egy Azure AD alkalmazás azonosítóját a webalkalmazás vagy API-alkalmazás, amely különbözik az alkalmazás-identitást a logikai alkalmazás. Az alkalmazásazonosító létrehozásához kövesse az előző lépéseket, a 2. részben a az Azure Portalon. 

Is is 1. rész lépéseit követve, de ügyeljen arra, hogy a webalkalmazás vagy API-alkalmazás tényleges `https://{URL}` a **bejelentkezési URL-** és **Alkalmazásazonosító URI-t**. Az alábbi lépéseket hogy mentse az ügyfél-Azonosítót és a bérlői azonosító használható az alkalmazás központi telepítési sablont és a 3. rész.

> [!NOTE]
> Az Azure ad-ben identitása a webalkalmazás vagy API-alkalmazás létrehozásakor az Azure portal, PowerShell-nem kell használnia. A PowerShell-parancsmag segítségével nem állítsa be a felhasználók jelentkezzen be a webhely szükséges engedélyekkel.

Kap, az ügyfél Azonosítóját és bérlőazonosítóját, miután egy webalkalmazás vagy API-alkalmazás központi telepítési sablonba alerőforrás azonosítóit a részletekben tartalmazza:

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

Automatikus telepítése egy üres webalkalmazást és a egy logikai alkalmazást az Azure Active Directory-hitelesítést, valamint [megtekintheti a teljes sablont](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json), vagy kattintson a **üzembe helyezés az Azure** itt:

[![Üzembe helyezés az Azure-ban](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>3. rész: A hitelesítési szakaszában a logikai alkalmazás feltöltése

Az előző sablon már rendelkezik az engedélyezési szakaszban állítsa be, de ha közvetlenül a logikai alkalmazás szerzői műveletek, akkor tartalmaznia kell a teljes körű engedéllyel szakaszt.

Nyissa meg a logikai alkalmazás definíciójának kódnézetben, nyissa meg a **HTTP** művelet szakaszban keresse meg a **engedélyezési** szakaszt, és ezt a sort:

`{"tenant": "{tenant-ID}", "audience": "{client-ID-from-Part-2-web-app-or-API app}", "clientId": "{client-ID-from-Part-1-logic-app}", "secret": "{key-from-Part-1-logic-app}", "type": "ActiveDirectoryOAuth" }`

| Elem | Szükséges | Leírás | 
| ------- | -------- | ----------- | 
| bérlő | Igen | Az Azure AD-bérlő GUID azonosítója | 
| Célközönség | Igen | A globálisan egyedi Azonosítót a célként megadott erőforrás, amely szeretné elérni, amely az alkalmazás azonosítóját a webalkalmazás vagy API-alkalmazás az ügyfél-azonosítója | 
| clientId | Igen | A globálisan egyedi Azonosítót, az ügyfél hozzáférést igényelnek, ami az identitása a logikai alkalmazás az ügyfél-azonosítója | 
| titkos kód | Igen | Kulcs vagy jelszó, az ügyfél, amely a hozzáférési jogkivonatot kér az alkalmazás azonosítója | 
| type | Igen | A hitelesítési típus. A ActiveDirectoryOAuth hitelesítéshez, az értéke `ActiveDirectoryOAuth`. | 
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

### <a name="secure-api-calls-through-code"></a>Biztonságos API-hívások kódok segítségével

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Tanúsítványalapú hitelesítés

A bejövő kérelmek, a logikai alkalmazás a webalkalmazás vagy API-alkalmazás ellenőrzéséhez használhatja az ügyféltanúsítványokat. Ismerje meg, állítsa be a kódot, hogy [TLS kölcsönös hitelesítés konfigurálása](../app-service/app-service-web-configure-tls-mutual-auth.md).

Az a **engedélyezési** szakaszban, ezt a sort: 

`{"type": "clientcertificate", "password": "password", "pfx": "long-pfx-key"}`

| Elem | Szükséges | Leírás | 
| ------- | -------- | ----------- | 
| type | Igen | A hitelesítési típus. Az ügyfél SSL-tanúsítványok, az értéknek kell lennie `ClientCertificate`. | 
| jelszó | Igen | A jelszót az ügyféltanúsítvány (PFX-fájl) eléréséhez | 
| pfx | Igen | A base64-kódolású tartalmak az ügyféltanúsítvány (PFX-fájl) | 
|||| 

<a name="basic"></a>

#### <a name="basic-authentication"></a>Alapszintű hitelesítés

Beérkező kérések a webalkalmazás vagy API-alkalmazás a logikai alkalmazás ellenőrzéséhez használhatja az alapszintű hitelesítés, például a felhasználónevet és jelszót. Az alapszintű hitelesítés, gyakori minta, és a hitelesítés a webalkalmazás vagy API-alkalmazást készíthet, bármilyen nyelven használható.

Az a **engedélyezési** szakaszban, ezt a sort:

`{"type": "basic", "username": "username", "password": "password"}`.

| Elem | Szükséges | Leírás | 
| ------- | -------- | ----------- | 
| type | Igen | A hitelesítési típus, amelyet használni szeretne. Az alapszintű hitelesítés, az értéknek kell lennie `Basic`. | 
| felhasználónév | Igen | A hitelesítéshez használni kívánt felhasználónevet | 
| jelszó | Igen | A hitelesítéshez használni kívánt jelszót | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Az Azure Active Directory hitelesítési kód

Alapértelmezés szerint az Azure AD-hitelesítés, amely az Azure Portalon kapcsolja be a minden részletre kiterjedő engedélyezési nem biztosít. A hitelesítés például az API-t egy adott bérlővel, nem pedig egy adott felhasználó vagy alkalmazás zárolja magát. 

API-hozzáférés korlátozása a logikai alkalmazás-kódon keresztül, bontsa ki a fejlécet, amely rendelkezik a JSON webes jogkivonat (JWT). Ellenőrizze a hívójának identitását, és nem egyezik a kérelmeket.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>További lépések

* [Üzembe helyezése és az egyéni API-k hívása a logic app-munkafolyamatok](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
