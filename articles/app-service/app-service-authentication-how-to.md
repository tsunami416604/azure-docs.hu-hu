---
title: A hitelesítés és az engedélyezés speciális használata – Azure App Service | Microsoft Docs
description: Bemutatja, hogyan szabhatja testre a hitelesítést és az engedélyezést App Serviceban, és hogyan szerezhet be felhasználói jogcímeket és különböző jogkivonatokat.
services: app-service
documentationcenter: ''
author: cephalin
manager: cfowler
editor: ''
ms.service: app-service
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: multiple
ms.topic: article
ms.date: 11/08/2018
ms.author: cephalin
ms.custom: seodec18
ms.openlocfilehash: b4b70a45758f697c469895bcef6ea8d203065e26
ms.sourcegitcommit: 470041c681719df2d4ee9b81c9be6104befffcea
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2019
ms.locfileid: "67853976"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>A hitelesítés és az engedélyezés speciális használata Azure App Service

Ez a cikk bemutatja, hogyan szabhatja testre a [app Service beépített hitelesítését és engedélyezését](overview-authentication-authorization.md), és hogyan kezelheti az alkalmazás identitását. 

A gyors kezdéshez tekintse meg a következő oktatóanyagok egyikét:

* [Oktatóanyag: Felhasználók hitelesítése és engedélyezése Azure App Service (Windows)](app-service-web-tutorial-auth-aad.md)
* [Oktatóanyag: Felhasználók hitelesítése és engedélyezése a Azure App Service Linux rendszeren](containers/tutorial-auth-aad.md)
* [Az alkalmazás konfigurálása az Azure Active Directory-bejelentkezés használatára](configure-authentication-provider-aad.md)
* [Az alkalmazás konfigurálása a Facebook-bejelentkezés használatára](configure-authentication-provider-facebook.md)
* [Az alkalmazás konfigurálása a Google-bejelentkezés használatára](configure-authentication-provider-google.md)
* [Az alkalmazás konfigurálása a Microsoft-fiókbejelentkezés használatára](configure-authentication-provider-microsoft.md)
* [Az alkalmazás konfigurálása a Twitter-bejelentkezés használatára](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Több bejelentkezési szolgáltató használata

A portál konfigurációja nem biztosít kulcsrakész módot arra, hogy több bejelentkezési szolgáltatót nyújtson be a felhasználók számára (például Facebook és Twitter). Azonban nem nehéz felvenni a funkciót az alkalmazáshoz. A lépéseket a következőképpen vázoljuk fel:

Először a Azure Portal **hitelesítés/engedélyezés** lapján konfigurálja az összes engedélyezni kívánt identitás-szolgáltatót.

**Ha a kérelem nem hitelesítve van**, válassza a **Névtelen kérelmek engedélyezése (nincs művelet)** lehetőséget.

A bejelentkezési oldalon vagy a navigációs sávon vagy az alkalmazás bármely más helyén vegyen fel egy bejelentkezési hivatkozást az összes engedélyezett szolgáltatóhoz (`/.auth/login/<provider>`). Példa:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Amikor a felhasználó rákattint az egyik hivatkozásra, megnyílik a megfelelő bejelentkezési oldal, amely bejelentkezik a felhasználóba.

Ha át szeretné irányítani a felhasználói bejelentkezést egy egyéni URL-címre, használja `post_login_redirect_url` a lekérdezési karakterlánc paramétert (a rendszer nem tévesztendő össze az identitás-szolgáltatói konfigurációban található átirányítási URI azonosítóval). Ha például a felhasználót a bejelentkezés után szeretné `/Home/Index` navigálni, használja a következő HTML-kódot:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Jogkivonatok érvényesítése a szolgáltatóktól

Az ügyfél által irányított bejelentkezés során az alkalmazás manuálisan bejelentkezik a felhasználónak a szolgáltatóba, majd elküldi a hitelesítési jogkivonatot az ellenőrzéshez App Service (lásd: [hitelesítési folyamat](overview-authentication-authorization.md#authentication-flow)). Ez az érvényesítés önmagában nem biztosít hozzáférést a kívánt alkalmazás-erőforrásokhoz, de sikeres ellenőrzés esetén egy munkamenet-tokent ad meg, amely az alkalmazás erőforrásainak elérésére használható. 

A szolgáltatói jogkivonat érvényesítéséhez a App Service alkalmazást először konfigurálni kell a kívánt szolgáltatóval. Futásidőben a szolgáltatótól kapott hitelesítési jogkivonat lekérése után a tokent `/.auth/login/<provider>` az érvényesítéshez tegye közzé. Példa: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

A jogkivonat formátuma a szolgáltatótól függően némileg eltér. A részletekért tekintse meg az alábbi táblázatot:

| Szolgáltató értéke | A kérelem törzsében kötelező | Megjegyzések |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | A `expires_in` tulajdonság megadása nem kötelező. <br/>Ha az élő szolgáltatásokból kér jogkivonatot, mindig `wl.basic` kérje a hatókört. |
| `google` | `{"id_token":"<id_token>"}` | A `authorization_code` tulajdonság megadása nem kötelező. Ha meg van adva, azt is elvégezheti a `redirect_uri` tulajdonsággal együtt. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Érvényes [felhasználói hozzáférési tokent](https://developers.facebook.com/docs/facebook-login/access-tokens) használhat a Facebook-ból. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Ha a szolgáltatói jogkivonat ellenőrzése sikeresen megtörtént, az API a válasz `authenticationToken` törzsében, azaz a munkamenet-jogkivonatban tér vissza. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Ha ezzel a munkamenet-jogkivonattal rendelkezik, a védett alkalmazás erőforrásaihoz a `X-ZUMO-AUTH` fejlécet a http-kérelmekhez hozzáadva érheti el. Példa: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Kijelentkezés a munkamenetből

A felhasználók elindíthatnak egy kijelentkezést az `GET` `/.auth/logout` alkalmazás végpontjának küldött kérelem elküldésével. A `GET` kérelem a következő műveleteket végzi el:

- Törli a hitelesítési cookie-kat az aktuális munkamenetből.
- Törli az aktuális felhasználó jogkivonatait a jogkivonat-tárolóból.
- A Azure Active Directory és a Google esetében a kiszolgálóoldali kijelentkezést hajt végre az identitás-szolgáltatón.

Íme egy egyszerű kijelentkezési hivatkozás egy weblapon:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Alapértelmezés szerint a sikeres kijelentkezés az URL-címre `/.auth/logout/done`irányítja át az ügyfelet. A kijelentkezési átirányítási lapot a `post_logout_redirect_uri` lekérdezési paraméter hozzáadásával módosíthatja. Példa:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Javasoljuk, hogy kódolja [](https://wikipedia.org/wiki/Percent-encoding) a értékét `post_logout_redirect_uri`.

Teljes URL-címek használatakor az URL-címnek vagy ugyanabban a tartományban kell lennie, vagy az alkalmazás számára engedélyezett külső átirányítási URL-címnek kell lennie. A következő példában a nem ugyanabban a tartományban `https://myexternalurl.com` üzemeltetett átirányítás:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

A következő parancsot kell futtatnia a [Azure Cloud Shellban](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>URL-töredékek megőrzése

Miután a felhasználók bejelentkeznek az alkalmazásba, általában ugyanarra a lapra szeretnének átirányítani, például `/wiki/Main_Page#SectionZ`:. Mivel azonban a rendszer soha nem küldi el a `#SectionZ`kiszolgálónak az [URL-töredékeket](https://wikipedia.org/wiki/Fragment_identifier) (például), a rendszer alapértelmezés szerint nem őrzi meg őket a OAuth bejelentkezés befejezése után, és visszairányítja az alkalmazásba. A felhasználók ezt követően optimális élményt kapnak, amikor újra kell navigálni a kívánt horgonyhoz. Ez a korlátozás az összes kiszolgálóoldali hitelesítési megoldásra vonatkozik.

App Service hitelesítésnél megőrizheti az URL-töredékeket a OAuth-bejelentkezés során. Ehhez állítson be egy nevű `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` `true`alkalmazást. Ezt megteheti a [Azure Portalban](https://portal.azure.com), vagy egyszerűen futtathatja a következő parancsot a [Azure Cloud Shellban](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Felhasználói jogcímek elérése

A App Service speciális fejlécek használatával átadja az alkalmazásnak a felhasználói jogcímeket. A külső kérelmek nem állíthatják be ezeket a fejléceket, ezért csak akkor jelennek meg, ha App Service. A fejlécek például a következők:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

A bármely nyelven vagy keretrendszerben írt kód a fenti fejlécek által igényelt információkat kérheti le. A ASP.NET 4,6 alkalmazások esetében a **ClaimsPrincipal** automatikusan a megfelelő értékekkel van beállítva.

Az alkalmazás a meghívásával `/.auth/me`további részleteket is kérhet a hitelesített felhasználóról. Az Mobile Apps Server SDK-k segítő módszereket biztosítanak az adatkezeléshez. További információ: [az azure Mobile apps Node. js SDK használata](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)és az Azure-beli .net-háttérrendszer- [kiszolgáló SDK-val való együttműködés. Mobile apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Jogkivonatok beolvasása az alkalmazás kódjában

A kiszolgáló kódjából a szolgáltatóra jellemző jogkivonatokat a rendszer a kérelem fejlécébe helyezi, így könnyen elérheti őket. A következő táblázat a lehetséges tokenek fejlécének neveit tartalmazza:

| Szolgáltató | Fejléc neve |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook-token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft-fiók | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Küldjön http `GET` - `/.auth/me`kérést az ügyfél kódjából (például egy mobil alkalmazásból vagy egy böngészőből származó JavaScriptből). A visszaadott JSON a szolgáltatóra jellemző jogkivonatokat tartalmaz.

> [!NOTE]
> A hozzáférési tokenek a szolgáltatói erőforrások eléréséhez szükségesek, így csak akkor jelennek meg, ha a szolgáltatót egy ügyfél titkos kulcsával konfigurálja. A frissítési tokenek beszerzésével kapcsolatban lásd: hozzáférési tokenek frissítése.

## <a name="refresh-identity-provider-tokens"></a>Identitás-szolgáltatói tokenek frissítése

Ha a szolgáltató hozzáférési jogkivonata (nem a [munkamenet](#extend-session-token-expiration-grace-period)jogkivonata) lejár, újra hitelesítenie kell a felhasználót, mielőtt újra felhasználja a tokent. A jogkivonat lejáratának `GET` elkerüléséhez hívja meg az `/.auth/refresh` alkalmazás végpontját. A híváskor App Service automatikusan frissíti a hitelesített felhasználó hozzáférési jogkivonatait a jogkivonat-tárolóban. Az alkalmazás kódjának a jogkivonatokra vonatkozó későbbi kérelmei megkapják a frissített jogkivonatokat. Ahhoz azonban, hogy a jogkivonat-frissítés működjön, a jogkivonat-tárolónak tartalmaznia kell a szolgáltató [frissítési](https://auth0.com/learn/refresh-tokens/) jogkivonatait. A frissítési tokenek beszerzésének módját az egyes szolgáltatók dokumentálják, de a következő lista egy rövid összefoglalás:

- **Google**: Fűzze hozzá a `/.auth/login/google` lekérdezésikarakterláncparamétertazAPI`access_type=offline` -híváshoz. Ha a Mobile apps SDK-t használja, hozzáadhatja a paramétert a túlterhelések egyikéhez `LogicAsync` (lásd: [Google frissítési jogkivonatok](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: Nem biztosít frissítési jogkivonatokat. A hosszú élettartamú tokenek 60 nap múlva lejárnak (lásd [a Facebook lejáratát és a hozzáférési tokenek kiterjesztését](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: A hozzáférési jogkivonatok nem járnak le (lásd: [Twitter OAuth – gyakori kérdések](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Microsoft-fiók**: A [Microsoft-fiók hitelesítési beállításainak konfigurálásakor](configure-authentication-provider-microsoft.md)válassza `wl.offline_access` ki a hatókört.
- **Azure Active Directory**: A [https://resources.azure.com](https://resources.azure.com)alkalmazásban hajtsa végre a következő lépéseket:
    1. Az oldal tetején válassza az **írás/írás**lehetőséget.
    2. A bal oldali böngészőben navigáljon az ****  > előfizetések ** _\<előfizetés\_neve_**  > **resourceGroups** >  **_erőforráscsoporthoz\_\< név\_>_** **** **** **** **szolgáltatók Microsoft. Web**Sites > -alkalmazás >  **_neve>\_config\<_**  >  >  >  >  **authsettings elemre**. 
    3. Kattintson a **Szerkesztés** gombra.
    4. Módosítsa a következő tulajdonságot. Cserélje  _\<le\_az alkalmazás azonosítóját >_ az elérni kívánt szolgáltatás Azure Active Directory alkalmazás-azonosítójával.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Kattintson a **put**elemre. 

Miután konfigurálta a szolgáltatót, megkeresheti a [frissítési jogkivonatot és a hozzáférési token](#retrieve-tokens-in-app-code) lejárati idejét a jogkivonat-tárolóban. 

Ha bármikor frissíteni szeretné a hozzáférési jogkivonatot, `/.auth/refresh` csak tetszőleges nyelven hívhat meg. A következő kódrészlet a jQuery használatával frissíti a hozzáférési jogkivonatait egy JavaScript-ügyfélről.

```JavaScript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Ha a felhasználó visszavonja az alkalmazás számára biztosított engedélyeket, a hívása `/.auth/me` sikertelen `403 Forbidden` lehet. A hibák diagnosztizálásához az alkalmazás naplófájljaiban tájékozódhat a részletekről.

## <a name="extend-session-token-expiration-grace-period"></a>Munkamenet-jogkivonat lejárati türelmi időszakának kiterjesztése

A hitelesített munkamenet 8 óra elteltével lejár. A hitelesített munkamenet lejárta után a rendszer alapértelmezés szerint 72 órás türelmi időszakot használ. A türelmi időszakon belül lehetősége van a munkamenet-jogkivonat frissítésére App Service a felhasználó ismételt hitelesítése nélkül. Csak akkor hívható `/.auth/refresh` meg, ha a munkamenet-jogkivonat érvénytelenné válik, és nem kell nyomon követnie a jogkivonat lejáratát. Ha a 72 órás türelmi időszak lejár, a felhasználónak újra be kell jelentkeznie, hogy érvényes munkamenet-tokent kapjon.

Ha a 72 órán belül nincs elég idő, kiterjesztheti ezt a lejárati időszakot. Ha hosszabb időn keresztül kiterjeszti a lejáratot, jelentős biztonsági következményekkel járhat (például egy hitelesítési jogkivonat kiszivárgásakor vagy ellopásakor). Ezért hagyja meg az alapértelmezett 72 órát, vagy állítsa a kiterjesztési időszakot a legkisebb értékre.

Az alapértelmezett lejárati időszak meghosszabbításához futtassa a következő parancsot a [Cloud Shellban](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> A türelmi időszak csak a App Service hitelesített munkamenetre vonatkozik, nem az Identitáskezelő jogkivonatára. A lejárt szolgáltatói jogkivonatok esetében nincs türelmi időszak. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Bejelentkezési fiókok tartományának korlátozása

A Microsoft-fiók és a Azure Active Directory egyaránt lehetővé teszi több tartományból való bejelentkezést. A Microsoft-fiók például lehetővé teszi a _Outlook.com_, a _live.com_és a _hotmail.com_ fiók használatát. Azure Active Directory lehetővé teszi a bejelentkezési fiókok tetszőleges számú egyéni tartományának használatát. Előfordulhat, hogy ez a viselkedés egy belső alkalmazás esetében nem kívánatos, mert nem szeretné, hogy bárki _Outlook.com_ -fiókkal hozzáférhessen. A bejelentkezési fiókok tartománynevének korlátozásához kövesse az alábbi lépéseket.

A [https://resources.azure.com](https://resources.azure.com)alkalmazásban >  navigáljon **az előfizetés**- ** _\_ előfizetésneve\<_**  >  **** resourceGroups >  **_erőforráshoz\<\_ Csoportnév\_ >_** **** **** **** **_szolgáltatók Microsoft.webhelyekalkalmazás\_ neve >\<_**  >  >  >  >  >  **konfiguráció**  >  **authsettings elemre**. 

Kattintson a **Szerkesztés**gombra, módosítsa a következő tulajdonságot, majd kattintson a **put**elemre. Ügyeljen arra, hogy  _\<a\_tartománynevet >_ a kívánt tartományra cserélje le.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: A felhasználók teljes körű (Windows)](app-service-web-tutorial-auth-aad.md)
> [oktatóanyagának hitelesítése és engedélyezése: Felhasználók hitelesítése és engedélyezése végpontok közötti (Linux)](containers/tutorial-auth-aad.md)
