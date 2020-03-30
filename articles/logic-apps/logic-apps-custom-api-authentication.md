---
title: Hitelesítés hozzáadása az egyéni API-k hívásainak védelméhez
description: A hitelesítés beállítása az Azure Logic Apps egyéni API-k hívásainak biztonságának növelése érdekében
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 09/22/2017
ms.openlocfilehash: 110a684cf6ad21c13411d3bc2ada84750744f00e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77191410"
---
# <a name="increase-security-for-calls-to-custom-apis-from-azure-logic-apps"></a>Az Azure Logic Apps egyéni API-khívásainak biztonsága

Az API-k hívásainak biztonsága érdekében az Azure Active Directory (Azure AD) hitelesítést az Azure Portalon keresztül állíthatja be, így nem kell frissítenie a kódot. Alternatív megoldásként a hitelesítés előírásához és kényszerítéséhez az API kódját is használhatja.

## <a name="authentication-options-for-your-api"></a>Az API hitelesítési lehetőségei

Az egyéni API-hoz való hívások biztonságát az alábbi módokon növelheti:

* [Nincs kódmódosítás:](#no-code)Védje az API-t az [Azure Active Directory (Azure AD)](../active-directory/fundamentals/active-directory-whatis.md) használatával az Azure Portalon keresztül, így nem kell frissítenie a kódot, vagy újratelepítenie kell az API-t.

  > [!NOTE]
  > Alapértelmezés szerint az Azure AD-hitelesítés, amely bevan kapcsolva az Azure Portalon nem nyújt részletes engedélyezést. Például ez a hitelesítés zárolja az API-t csak egy adott bérlő, nem egy adott felhasználó vagy alkalmazás. 

* [Az API-kód frissítése:](#update-code)Az API védelme [tanúsítványhitelesítés,](#certificate) [alapfokú hitelesítés](#basic)vagy [Azure AD-hitelesítés](#azure-ad-code) kódon keresztüli kényszerítésével.

<a name="no-code"></a>

### <a name="authenticate-calls-to-your-api-without-changing-code"></a>Az API-ba irányuló hívások hitelesítése a kód módosítása nélkül

A módszer általános lépései:

1. Hozzon létre két Azure Active Directory (Azure AD) alkalmazásidentitást: egyet a logikai alkalmazáshoz, egyet pedig a webalkalmazáshoz (vagy AZ API-alkalmazáshoz).

2. Az API-hoz való hívások hitelesítéséhez használja a hitelesítő adatokat (ügyfél-azonosító és titkos) az Azure AD-alkalmazás identitásához társított a logikai alkalmazáshoz tartozó.

3. Az alkalmazásazonosítók felvétele a logikai alkalmazás definíciójába.

#### <a name="part-1-create-an-azure-ad-application-identity-for-your-logic-app"></a>1. rész: Hozzon létre egy Azure AD-alkalmazás identitását a logikai alkalmazáshoz

A logikai alkalmazás ezt az Azure AD-alkalmazásidentitást használja az Azure AD-vel való hitelesítéshez. Ezt az identitást csak egyszer kell beállítania a címtárhoz. Például dönthet úgy, hogy ugyanazt az identitást használja az összes logikai alkalmazáshoz, még akkor is, ha minden logikai alkalmazáshoz létrehozhat egyedi identitásokat. Ezeket az identitásokat az Azure Portalon állíthatja be, vagy használhatja a [PowerShellt.](#powershell)

**Az alkalmazásidentitás létrehozása a logikai alkalmazáshoz az Azure Portalon**

1. Az [Azure Portalon](https://portal.azure.com "https://portal.azure.com")válassza az **Azure Active Directory**lehetőséget. 

2. Ellenőrizze, hogy ugyanabban a címtárban van-e, mint a webalkalmazás vagy az API-alkalmazás.

   > [!TIP]
   > Könyvtárak közötti váltáshoz válassza ki a profilját, és válasszon másik könyvtárat. Vagy válassza **az Áttekintés** > **Átváltsa a könyvtárat**.

3. A címtár menü **Kezelése területén**válassza az Új**alkalmazásregisztráció alkalmazásregisztráció** **lehetőséget.** > 

   > [!TIP]
   > Alapértelmezés szerint az alkalmazásregisztrációs lista a címtárban lévő összes alkalmazásregisztrációt megjeleníti. Ha csak az alkalmazásregisztrációkat szeretné megtekinteni, a keresőmező mellett válassza a **Saját alkalmazások**lehetőséget. 

   ![Új alkalmazásregisztráció létrehozása](./media/logic-apps-custom-api-authentication/new-app-registration-azure-portal.png)

4. Adjon nevet az alkalmazásidentitásnak, hagyja **az alkalmazástípust** **webalkalmazásra /API-ra**állítva, adjon meg egy egyedi karakterláncot, amely a **bejelentkezési URL-cím**tartományaként van formázva, majd válassza **a Create (Létrehozás) lehetőséget.**

   ![Név és bejelentkezési URL-cím megadásához az alkalmazásidentitáshoz](./media/logic-apps-custom-api-authentication/logic-app-identity-azure-portal.png)

   A logikai alkalmazáshoz létrehozott alkalmazásidentitás most megjelenik az alkalmazásregisztrációk listájában.

   ![A logikai alkalmazás alkalmazásidentitása](./media/logic-apps-custom-api-authentication/logic-app-identity-created.png)

5. Az alkalmazásregisztrációk listájában válassza ki az új alkalmazásidentitást. Másolja és mentse az **alkalmazásazonosítót,** hogy a logikai alkalmazás "ügyfélazonosítójaként" használja a 3.

   ![Alkalmazásazonosító másolása és mentése a logikai alkalmazáshoz](./media/logic-apps-custom-api-authentication/logic-app-application-id.png)

6. Ha az alkalmazásidentitás-beállítások nem láthatók, válassza a **Beállítások** vagy **a Minden beállítás lehetőséget.**

7. Az **API Access csoportban**válassza a **Kulcsok lehetőséget.** A **Leírás csoportban**adja meg a kulcs nevét. A **Lejár,** válassza ki a kulcs időtartamát.

   A létrehozott kulcs az alkalmazásidentitás "titkos" vagy jelszóként működik a logikai alkalmazáshoz.

   ![Kulcs létrehozása a logikai alkalmazás identitáshoz](./media/logic-apps-custom-api-authentication/create-logic-app-identity-key-secret-password.png)

8. Az eszköztáron válassza a **Mentés gombot.** Az **Érték**csoportban megjelenik a kulcs. 
**Győződjön meg arról, hogy a kulcsot** későbbi használatra másolja és menti, mert a kulcs rejtve marad, amikor elhagyja a **Kulcsok** lapot.

   Amikor konfigurálja a logikai alkalmazást a 3.

   ![Kulcs másolása és mentése későbbi használatra](./media/logic-apps-custom-api-authentication/logic-app-copy-key-secret-password.png)

<a name="powershell"></a>

**Az alkalmazásidentitás létrehozása a logikai alkalmazáshoz a PowerShellben**

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ezt a feladatot az Azure Resource Manager powershell segítségével hajthatja végre. A PowerShellben futtassa a következő parancsokat:

1. `Add-AzAccount`

1. `$SecurePassword = Read-Host -AsSecureString`

1. Írjon be egy jelszót, és nyomja le az Enter billentyűt.

1. `New-AzADApplication -DisplayName "MyLogicAppID" -HomePage "http://mydomain.tld" -IdentifierUris "http://mydomain.tld" -Password $SecurePassword`

1. Győződjön meg arról, hogy másolja a **bérlői azonosítót** (GUID az Azure AD-bérlő), az **alkalmazásazonosítót**és a használt jelszót.

További információkért megtudhatja, hogyan [hozhat létre egyszerű szolgáltatást a PowerShell használatával az erőforrások eléréséhez.](../active-directory/develop/howto-authenticate-service-principal-powershell.md)

#### <a name="part-2-create-an-azure-ad-application-identity-for-your-web-app-or-api-app"></a>2. rész: Hozzon létre egy Azure AD-alkalmazás identitását a webalkalmazáshoz vagy az API-alkalmazáshoz

Ha a webalkalmazás vagy AZ API-alkalmazás már telepítve van, bekapcsolhatja a hitelesítést, és létrehozhatja az alkalmazás identitását az Azure Portalon. Ellenkező esetben [bekapcsolhatja a hitelesítést, amikor egy Azure Resource Manager-sablonnal telepíti.](#authen-deploy) 

**Hozza létre az alkalmazásidentitást, és kapcsolja be a hitelesítést az Azure Portalon telepített alkalmazásokhoz**

1. Az [Azure Portalon](https://portal.azure.com "https://portal.azure.com")keresse meg és válassza ki a webalkalmazást vagy API-alkalmazást. 

2. A **Beállítások csoportban**válassza a **Hitelesítés/engedélyezés**lehetőséget. Az **App Service-hitelesítés**csoportban kapcsolja be **a hitelesítést.** A **Hitelesítésszolgáltatók csoportban**válassza az **Azure Active Directory**lehetőséget.

   ![A hitelesítés bekapcsolása](./media/logic-apps-custom-api-authentication/custom-web-api-app-authentication.png)

3. Most hozzon létre egy alkalmazásidentitást a webalkalmazáshoz vagy az API-alkalmazáshoz, ahogy az itt látható. Az **Azure Active Directory beállításai** lapon állítsa **a Felügyeleti módot** **expresszre.** Válassza **az Új AD-alkalmazás létrehozása**lehetőséget. Adjon nevet az alkalmazásidentitásának, és válassza **az OK gombot.** 

   ![Alkalmazásidentitás létrehozása a webalkalmazáshoz vagy az API-alkalmazáshoz](./media/logic-apps-custom-api-authentication/custom-api-application-identity.png)

4. A **Hitelesítés/Engedélyezés** lapon kattintson a **Mentés** gombra.

Most meg kell találnia az ügyfél-azonosítót és a bérlői azonosítót a webalkalmazáshoz vagy API-alkalmazáshoz társított alkalmazásidentitáshoz. Ezeket az azonosítókat a 3. Így folytassa ezeket a lépéseket az Azure Portalon.

**Az alkalmazásidentitás ügyfélazonosítójának és bérlői azonosítójának megkeresése a webalkalmazáshoz vagy API-alkalmazáshoz az Azure Portalon**

1. A **Hitelesítésszolgáltatók csoportban**válassza az **Azure Active Directory**lehetőséget. 

   ![Válassza az „Azure Active Directory” elemet](./media/logic-apps-custom-api-authentication/custom-api-app-identity-client-id-tenant-id.png)

2. Az **Azure Active Directory beállításai** lapon állítsa **a Felügyeleti módot** **Speciális beállításra.**

3. Másolja az **ügyfélazonosítót**, és mentse a GUID azonosítót a 3.

   > [!TIP] 
   > Ha **az ügyfélazonosító** és **a kiállító url-címe** nem jelenik meg, próbálja meg frissíteni az Azure Portalt, és ismételje meg az 1.

4. A **Kiállító url-címét csoportban**másolja és mentse csak a 3. Ezt a GUID-ot a webalkalmazásban vagy az API-alkalmazás központi telepítési sablonjában is használhatja, ha szükséges.

   Ez a GUID az adott bérlő GUID azonosítója ("bérlőazonosító"), és meg kell jelennie ebben az URL-címen:`https://sts.windows.net/{GUID}`

5. A módosítások mentése nélkül zárja be az **Azure Active Directory beállításai** lapot.

<a name="authen-deploy"></a>

**A hitelesítés bekapcsolása Azure Resource Manager-sablonnal történő üzembe helyezéskor**

Továbbra is létre kell hoznia egy Azure AD-alkalmazás identitását a webalkalmazáshoz vagy AZ API-alkalmazáshoz, amely eltér a logikai alkalmazás identitásától. Az alkalmazásidentitás létrehozásához kövesse az Azure Portal 2. 

Az `https://{URL}` **Sign-on URL** **1.** Ezekből a lépésekből mentheti az ügyfélazonosítót és a bérlői azonosítót az alkalmazás központi telepítési sablonjában való használatra, valamint a 3.

> [!NOTE]
> Amikor létrehozza az Azure AD-alkalmazás identitását a webalkalmazáshoz vagy AZ API-alkalmazáshoz, az Azure Portalt kell használnia, nem a PowerShellt. A PowerShell-parancslap nem állítja be a felhasználók webhelyre való bejelentkezéséhez szükséges engedélyeket.

Miután letette az ügyfélazonosítót és a bérlői azonosítót, adja meg ezeket az azonosítókat a webalkalmazás vagy az API-alkalmazás alerőforrásaként a központi telepítési sablonban:

``` json
"resources": [ 
   {
      "apiVersion": "2015-08-01",
      "name": "web",
      "type": "config",
      "dependsOn": ["[concat('Microsoft.Web/sites/','parameters('webAppName'))]"],
      "properties": {
         "siteAuthEnabled": true,
         "siteAuthSettings": {
            "clientId": "<client-ID>",
            "issuer": "https://sts.windows.net/<tenant-ID>/"
         }
      }
   } 
]
```

Ha egy üres webalkalmazást és egy logikai alkalmazást az Azure Active Directory-hitelesítéssel együtt automatikusan üzembe szeretne helyezni, [tekintse meg a teljes sablont itt,](https://github.com/Azure/azure-quickstart-templates/tree/master/201-logic-app-custom-api/azuredeploy.json)vagy kattintson a Telepítés az **Azure-ba** gombra:

[![Üzembe helyezés az Azure-ban](media/logic-apps-custom-api-authentication/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

#### <a name="part-3-populate-the-authorization-section-in-your-logic-app"></a>3. rész: Az Engedélyezés szakasz feltöltése a logikai alkalmazásban

Az előző sablon már rendelkezik ezzel az engedélyezési szakaszbeállítással, de ha közvetlenül a logikai alkalmazást hozza létre, akkor a teljes engedélyezési szakaszt is tartalmaznia kell.

Nyissa meg a logikai alkalmazás definícióját kódnézetben, lépjen a **HTTP-műveletdefinícióra,** keresse meg az **Engedélyezés szakaszt,** és adja meg az alábbi tulajdonságokat:

```json
{
   "tenant": "<tenant-ID>",
   "audience": "<client-ID-from-Part-2-web-app-or-API app>", 
   "clientId": "<client-ID-from-Part-1-logic-app>",
   "secret": "<key-from-Part-1-logic-app>", 
   "type": "ActiveDirectoryOAuth"
}
```

| Tulajdonság | Kötelező | Leírás | 
| -------- | -------- | ----------- | 
| Bérlő | Igen | Az Azure AD-bérlő GUID azonosítója | 
| Közönség | Igen | A célerőforrás GUID azonosítója, amely a webalkalmazás vagy AZ API-alkalmazás alkalmazásidentitásából származó ügyfélazonosító | 
| ügyfél-azonosító | Igen | A hozzáférést kérő ügyfél GUID azonosítója, amely a logikai alkalmazás alkalmazásidentitásából származó ügyfélazonosító | 
| titkos kód | Igen | A hozzáférési jogkivonatot kérő ügyfél alkalmazásidentitásából származó kulcs vagy jelszó | 
| type | Igen | A hitelesítés típusa. ActiveDirectoryOAuth hitelesítés esetén az `ActiveDirectoryOAuth`érték . | 
|||| 

Példa:

``` json
{
   "actions": {
      "HTTP": {
         "inputs": {
            "method": "POST",
            "uri": "https://your-api-azurewebsites.net/api/your-method",
            "authentication": {
               "tenant": "tenant-ID",
               "audience": "client-ID-from-azure-ad-app-for-web-app-or-api-app",
               "clientId": "client-ID-from-azure-ad-app-for-logic-app",
               "secret": "key-from-azure-ad-app-for-logic-app",
               "type": "ActiveDirectoryOAuth"
            }
         }
      }
   }
}
```

<a name="update-code"></a>

### <a name="secure-api-calls-through-code"></a>Biztonságos API-hívások kódon keresztül

<a name="certificate"></a>

#### <a name="certificate-authentication"></a>Tanúsítványhitelesítés

A logikai alkalmazásból a webalkalmazásba vagy az API-alkalmazásba érkező bejövő kérelmek érvényesítéséhez használhatja az ügyféltanúsítványokat. A kód beállításáról megtudhatja, [hogyan konfigurálható a TLS kölcsönös hitelesítése.](../app-service/app-service-web-configure-tls-mutual-auth.md)

Az **Engedélyezés** szakaszban adja meg a következő tulajdonságokat:

```json
{
   "type": "ClientCertificate",
   "password": "<password>",
   "pfx": "<long-pfx-key>"
} 
```

| Tulajdonság | Kötelező | Leírás |
| -------- | -------- | ----------- |
| `type` | Igen | A hitelesítés típusa. SSL-ügyféltanúsítványok esetén az `ClientCertificate`értéknek . |
| `password` | Nem | Az ügyféltanúsítvány elérésének jelszava (PFX-fájl) |
| `pfx` | Igen | Az ügyféltanúsítvány base64 kódolású tartalma (PFX-fájl) |
||||

<a name="basic"></a>

#### <a name="basic-authentication"></a>Alapszintű hitelesítés

A logikai alkalmazásból a webalkalmazásba vagy az API-alkalmazásba érkező bejövő kérelmek érvényesítéséhez használhatja az alapfokú hitelesítést, például a felhasználónevet és a jelszót. Az alapfokú hitelesítés gyakori minta, és ezt a hitelesítést a webalkalmazás vagy AZ API-alkalmazás létrehozásához használt bármely nyelven használhatja.

Az **Engedélyezés** szakaszban adja meg a következő tulajdonságokat:

```json
{
   "type": "Basic",
   "username": "<username>",
   "password": "<password>"
}
```

| Tulajdonság | Kötelező | Leírás | 
| -------- | -------- | ----------- | 
| type | Igen | A használni kívánt hitelesítési típus. Az alapfokú hitelesítéshez `Basic`az értéknek . | 
| felhasználónév | Igen | A hitelesítéshez használni kívánt felhasználónév | 
| jelszó | Igen | A hitelesítéshez használni kívánt jelszó | 
|||| 

<a name="azure-ad-code"></a>

#### <a name="azure-active-directory-authentication-through-code"></a>Azure Active Directory-hitelesítés kódon keresztül

Alapértelmezés szerint az Azure AD-hitelesítés, amely bevan kapcsolva az Azure Portalon nem nyújt részletes engedélyezést. Például ez a hitelesítés zárolja az API-t csak egy adott bérlő, nem egy adott felhasználó vagy alkalmazás. 

Az API-hozzáférés korlátozása a logikai alkalmazáshoz a kódon keresztül, bontsa ki a fejlécet, amely a JSON-webtoken (JWT). Ellenőrizze a hívó identitását, és utasítsa el a nem egyező kéréseket.

<!-- Going further, to implement this authentication entirely in your own code, 
and not use the Azure portal, learn how to 
[authenticate with on-premises Active Directory in your Azure app](../app-service/overview-authentication-authorization.md).

To create an application identity for your logic app and use that identity to call your API, 
you must follow the previous steps. -->

## <a name="next-steps"></a>További lépések

* [Egyéni API-k üzembe helyezése és hívása logikai alkalmazás-munkafolyamatokból](../logic-apps/logic-apps-custom-api-host-deploy-call.md)
