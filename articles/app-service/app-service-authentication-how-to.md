---
title: A AuthN/AuthO speciális használata
description: Megtudhatja, hogyan szabhatja testre a hitelesítési és engedélyezési funkciót App Service különböző forgatókönyvek esetén, valamint felhasználói jogcímeket és különböző jogkivonatokat kaphat.
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: d57b196bf95ebdf31bc459ad4b9d718fd32ca495
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79280832"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>A hitelesítés és az engedélyezés speciális használata Azure App Service

Ez a cikk bemutatja, hogyan szabhatja testre a [app Service beépített hitelesítését és engedélyezését](overview-authentication-authorization.md), és hogyan kezelheti az alkalmazás identitását. 

A gyors kezdéshez tekintse meg a következő oktatóanyagok egyikét:

* [Oktatóanyag: Azure App Service teljes körű hitelesítése és engedélyezése a felhasználók számára (Windows)](app-service-web-tutorial-auth-aad.md)
* [Oktatóanyag: a felhasználók teljes körű hitelesítése és engedélyezése Azure App Service Linux rendszeren](containers/tutorial-auth-aad.md)
* [Az alkalmazás konfigurálása az Azure Active Directory-bejelentkezés használatára](configure-authentication-provider-aad.md)
* [Az alkalmazás konfigurálása a Facebook-bejelentkezés használatára](configure-authentication-provider-facebook.md)
* [Az alkalmazás konfigurálása a Google-bejelentkezés használatára](configure-authentication-provider-google.md)
* [Az alkalmazás konfigurálása a Microsoft-fiókbejelentkezés használatára](configure-authentication-provider-microsoft.md)
* [Az alkalmazás konfigurálása a Twitter-bejelentkezés használatára](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Több bejelentkezési szolgáltató használata

A portál konfigurációja nem biztosít kulcsrakész módot arra, hogy több bejelentkezési szolgáltatót nyújtson be a felhasználók számára (például Facebook és Twitter). Azonban nem nehéz felvenni a funkciót az alkalmazáshoz. A lépéseket a következőképpen vázoljuk fel:

Először a Azure Portal **hitelesítés/engedélyezés** lapján konfigurálja az összes engedélyezni kívánt identitás-szolgáltatót.

**Ha a kérelem nem hitelesítve van**, válassza a **Névtelen kérelmek engedélyezése (nincs művelet)** lehetőséget.

A bejelentkezési oldalon vagy a navigációs sávon vagy az alkalmazás bármely más helyén vegyen fel egy bejelentkezési hivatkozást az összes engedélyezett szolgáltatóhoz (`/.auth/login/<provider>`). Például:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Amikor a felhasználó rákattint az egyik hivatkozásra, megnyílik a megfelelő bejelentkezési oldal, amely bejelentkezik a felhasználóba.

Ha át szeretné irányítani a felhasználói bejelentkezést egy egyéni URL-címre, használja a `post_login_redirect_url` lekérdezési karakterlánc paramétert (a rendszer nem tévesztendő össze az átirányítási URI azonosítóval az identitás-szolgáltató konfigurációjában). Ha például a felhasználót a bejelentkezés után `/Home/Index` szeretné navigálni, használja a következő HTML-kódot:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Jogkivonatok érvényesítése a szolgáltatóktól

Az ügyfél által irányított bejelentkezés során az alkalmazás manuálisan bejelentkezik a felhasználónak a szolgáltatóba, majd elküldi a hitelesítési jogkivonatot az ellenőrzéshez App Service (lásd: [hitelesítési folyamat](overview-authentication-authorization.md#authentication-flow)). Ez az érvényesítés önmagában nem biztosít hozzáférést a kívánt alkalmazás-erőforrásokhoz, de sikeres ellenőrzés esetén egy munkamenet-tokent ad meg, amely az alkalmazás erőforrásainak elérésére használható. 

A szolgáltatói jogkivonat érvényesítéséhez a App Service alkalmazást először konfigurálni kell a kívánt szolgáltatóval. Futásidőben, miután lekérte a hitelesítési tokent a szolgáltatótól, az érvényesítéshez tegye közzé `/.auth/login/<provider>` a tokent. Például: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

A jogkivonat formátuma a szolgáltatótól függően némileg eltér. A részletekért tekintse meg az alábbi táblázatot:

| Szolgáltató értéke | A kérelem törzsében kötelező | Megjegyzések |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | A `expires_in` tulajdonság nem kötelező. <br/>Ha az élő szolgáltatásokból kér jogkivonatot, mindig a `wl.basic` hatókört kéri le. |
| `google` | `{"id_token":"<id_token>"}` | A `authorization_code` tulajdonság nem kötelező. Ha meg van adva, azt is elvégezheti a `redirect_uri` tulajdonsággal együtt. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Érvényes [felhasználói hozzáférési tokent](https://developers.facebook.com/docs/facebook-login/access-tokens) használhat a Facebook-ból. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Ha a szolgáltatói jogkivonatot a rendszer sikeresen érvényesíti, az API a válasz törzsében lévő `authenticationToken`t adja vissza, amely a munkamenet-jogkivonat. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Ha már rendelkezik ezzel a munkamenet-jogkivonattal, a védett alkalmazás-erőforrások eléréséhez adja hozzá a `X-ZUMO-AUTH` fejlécet a HTTP-kérésekhez. Például: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Kijelentkezés a munkamenetből

A felhasználók egy `GET` kérelem küldésével indíthatják el a kijelentkezést az alkalmazás `/.auth/logout`-végpontján. A `GET` kérelem a következő műveleteket végzi el:

- Törli a hitelesítési cookie-kat az aktuális munkamenetből.
- Törli az aktuális felhasználó jogkivonatait a jogkivonat-tárolóból.
- A Azure Active Directory és a Google esetében a kiszolgálóoldali kijelentkezést hajt végre az identitás-szolgáltatón.

Íme egy egyszerű kijelentkezési hivatkozás egy weblapon:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Alapértelmezés szerint a sikeres kijelentkezés a `/.auth/logout/done`URL-címre irányítja át az ügyfelet. Az `post_logout_redirect_uri` lekérdezési paraméter hozzáadásával módosíthatja a kijelentkezés utáni átirányítás lapot. Például:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Javasoljuk, hogy [kódolja](https://wikipedia.org/wiki/Percent-encoding) `post_logout_redirect_uri`értékét.

Teljes URL-címek használatakor az URL-címnek vagy ugyanabban a tartományban kell lennie, vagy az alkalmazás számára engedélyezett külső átirányítási URL-címnek kell lennie. Az alábbi példában átirányítjuk a `https://myexternalurl.com`, amely nem ugyanabban a tartományban található:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Futtassa a következő parancsot a [Azure Cloud Shellban](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>URL-töredékek megőrzése

Miután a felhasználók bejelentkeznek az alkalmazásba, általában ugyanarra a lapra szeretnének átirányítani, mint például a `/wiki/Main_Page#SectionZ`. Mivel azonban az [URL-töredékek](https://wikipedia.org/wiki/Fragment_identifier) (például `#SectionZ`) soha nem lesznek elküldve a kiszolgálónak, a rendszer alapértelmezés szerint nem őrzi meg őket a OAuth bejelentkezés befejezése után, és átirányítja azt az alkalmazásba. A felhasználók ezt követően optimális élményt kapnak, amikor újra kell navigálni a kívánt horgonyhoz. Ez a korlátozás az összes kiszolgálóoldali hitelesítési megoldásra vonatkozik.

App Service hitelesítésnél megőrizheti az URL-töredékeket a OAuth-bejelentkezés során. Ehhez állítson be egy `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` nevű alkalmazást `true`. Ezt megteheti a [Azure Portalban](https://portal.azure.com), vagy egyszerűen futtathatja a következő parancsot a [Azure Cloud Shellban](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Felhasználói jogcímek elérése

A App Service speciális fejlécek használatával átadja az alkalmazásnak a felhasználói jogcímeket. A külső kérelmek nem állíthatják be ezeket a fejléceket, ezért csak akkor jelennek meg, ha App Service. A fejlécek például a következők:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

A bármely nyelven vagy keretrendszerben írt kód a fenti fejlécek által igényelt információkat kérheti le. A ASP.NET 4,6 alkalmazások esetében a **ClaimsPrincipal** automatikusan a megfelelő értékekkel van beállítva. ASP.NET Core azonban nem biztosít olyan hitelesítési middleware-t, amely App Service felhasználói jogcímekbe integrálódik. Megkerülő megoldásért tekintse meg a következőt: [MaximeRouiller. Azure. AppService. EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Az alkalmazás a `/.auth/me`meghívásával további részleteket is kérhet a hitelesített felhasználóról. Az Mobile Apps Server SDK-k segítő módszereket biztosítanak az adatkezeléshez. További információ: [az azure Mobile apps Node. js SDK használata](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)és [Az Azure-beli .net-háttérrendszer-kiszolgáló SDK-val való együttműködés. Mobile apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

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

Az ügyfél kódjából (például egy mobil alkalmazásból vagy böngészőből származó JavaScriptből) küldjön HTTP-`GET` kérelmet a `/.auth/me`. A visszaadott JSON a szolgáltatóra jellemző jogkivonatokat tartalmaz.

> [!NOTE]
> A hozzáférési tokenek a szolgáltatói erőforrások eléréséhez szükségesek, így csak akkor jelennek meg, ha a szolgáltatót egy ügyfél titkos kulcsával konfigurálja. A frissítési tokenek beszerzésével kapcsolatban lásd: hozzáférési tokenek frissítése.

## <a name="refresh-identity-provider-tokens"></a>Identitás-szolgáltatói tokenek frissítése

Ha a szolgáltató hozzáférési jogkivonata (nem a [munkamenet jogkivonata](#extend-session-token-expiration-grace-period)) lejár, újra hitelesítenie kell a felhasználót, mielőtt újra felhasználja a tokent. Elkerülheti a token lejáratát úgy, hogy `GET` hívást indít az alkalmazás `/.auth/refresh` végpontjának. A híváskor App Service automatikusan frissíti a hitelesített felhasználó hozzáférési jogkivonatait a jogkivonat-tárolóban. Az alkalmazás kódjának a jogkivonatokra vonatkozó későbbi kérelmei megkapják a frissített jogkivonatokat. Ahhoz azonban, hogy a jogkivonat-frissítés működjön, a jogkivonat-tárolónak tartalmaznia kell a szolgáltató [frissítési jogkivonatait](https://auth0.com/learn/refresh-tokens/) . A frissítési tokenek beszerzésének módját az egyes szolgáltatók dokumentálják, de a következő lista egy rövid összefoglalás:

- **Google**: fűzze hozzá `access_type=offline` lekérdezési karakterlánc paramétert a `/.auth/login/google` API-híváshoz. Ha a Mobile Apps SDK-t használja, a paramétert hozzáadhatja az egyik `LogicAsync` túlterheléshez (lásd: [Google frissítési jogkivonatok](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: nem biztosít frissítési jogkivonatokat. A hosszú élettartamú tokenek 60 nap múlva lejárnak (lásd [a Facebook lejáratát és a hozzáférési tokenek kiterjesztését](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: a hozzáférési tokenek nem járnak le (lásd: [Twitter OAuth – gyakori kérdések](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Microsoft-fiók**: a [Microsoft-fiók hitelesítési beállításainak konfigurálásakor](configure-authentication-provider-microsoft.md)válassza ki a `wl.offline_access` hatókört.
- **Azure Active Directory**: a [https://resources.azure.com](https://resources.azure.com)hajtsa végre a következő lépéseket:
    1. Az oldal tetején válassza az **írás/írás**lehetőséget.
    2. A bal oldali böngészőben navigáljon az **előfizetések** >  **_\<előfizetés\_név_**  > **resourceGroups** >  **_\<erőforrás\_csoport\_neve >_**  > **szolgáltatók** > **Microsoft. Web** > **Sites** > \<\_ **_neve >_**  > **config** > **authsettings elemre**. 
    3. Kattintson a **Szerkesztés** gombra.
    4. Módosítsa a következő tulajdonságot. Cserélje le _\<app\_id >t_ az elérni kívánt szolgáltatás Azure Active Directory alkalmazás-azonosítójával.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Kattintson a **put**elemre. 

Miután konfigurálta a szolgáltatót, [megkeresheti a frissítési jogkivonatot és a hozzáférési token lejárati idejét](#retrieve-tokens-in-app-code) a jogkivonat-tárolóban. 

Ha bármikor frissíteni szeretné a hozzáférési jogkivonatot, egyszerűen hívja meg a `/.auth/refresh`t bármilyen nyelven. A következő kódrészlet a jQuery használatával frissíti a hozzáférési jogkivonatait egy JavaScript-ügyfélről.

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

Ha a felhasználó visszavonja az alkalmazás számára biztosított engedélyeket, a `/.auth/me`-hívás sikertelen lehet `403 Forbidden` választ. A hibák diagnosztizálásához az alkalmazás naplófájljaiban tájékozódhat a részletekről.

## <a name="extend-session-token-expiration-grace-period"></a>Munkamenet-jogkivonat lejárati türelmi időszakának kiterjesztése

A hitelesített munkamenet 8 óra elteltével lejár. A hitelesített munkamenet lejárta után a rendszer alapértelmezés szerint 72 órás türelmi időszakot használ. A türelmi időszakon belül lehetősége van a munkamenet-jogkivonat frissítésére App Service a felhasználó ismételt hitelesítése nélkül. Csak akkor hívhat `/.auth/refresh`, ha a munkamenet-jogkivonat érvénytelenné válik, és nem kell nyomon követnie a jogkivonat lejáratát. Ha a 72 órás türelmi időszak lejár, a felhasználónak újra be kell jelentkeznie, hogy érvényes munkamenet-tokent kapjon.

Ha a 72 órán belül nincs elég idő, kiterjesztheti ezt a lejárati időszakot. Ha hosszabb időn keresztül kiterjeszti a lejáratot, jelentős biztonsági következményekkel járhat (például egy hitelesítési jogkivonat kiszivárgásakor vagy ellopásakor). Ezért hagyja meg az alapértelmezett 72 órát, vagy állítsa a kiterjesztési időszakot a legkisebb értékre.

Az alapértelmezett lejárati időszak meghosszabbításához futtassa a következő parancsot a [Cloud Shellban](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> A türelmi időszak csak a App Service hitelesített munkamenetre vonatkozik, nem az Identitáskezelő jogkivonatára. A lejárt szolgáltatói jogkivonatok esetében nincs türelmi időszak. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Bejelentkezési fiókok tartományának korlátozása

A Microsoft-fiók és a Azure Active Directory egyaránt lehetővé teszi több tartományból való bejelentkezést. A Microsoft-fiók például lehetővé teszi a _Outlook.com_, a _live.com_és a _hotmail.com_ fiók használatát. Az Azure AD lehetővé teszi a bejelentkezési fiókok tetszőleges számú egyéni tartományának használatát. Előfordulhat azonban, hogy a felhasználóknak azonnal fel kell gyorsítania a saját márkás Azure AD bejelentkezési oldalát (például `contoso.com`). Ha a bejelentkezési fiókok tartománynevét szeretné javasolni, kövesse az alábbi lépéseket.

A [https://resources.azure.com](https://resources.azure.com)-ben navigáljon az **előfizetések** >  **_\<előfizetés\_neve_**  > **resourceGroups** >  **_\<erőforrás\_csoport\_neve >_**  > **szolgáltatók** > **Microsoft. Web** > **Sites** > \<\_ **_neve >_**  > **config** > **authsettings elemre**. 

Kattintson a **Szerkesztés**gombra, módosítsa a következő tulajdonságot, majd kattintson a **put**elemre. Ne felejtse el lecserélni _\<tartomány\_nevét_ a kívánt tartományra >.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Ezzel a beállítással a rendszer hozzáfűzi a `domain_hint` lekérdezési karakterlánc paramétert a bejelentkezési átirányítás URL-címéhez. 

> [!IMPORTANT]
> Lehetséges, hogy az ügyfél el szeretné távolítani a `domain_hint` paramétert az átirányítási URL-cím fogadása után, majd egy másik tartományba való bejelentkezést. Így amíg ez a függvény kényelmes, nem biztonsági funkció.
>

## <a name="authorize-or-deny-users"></a>Felhasználók engedélyezése vagy megtagadása

Míg App Service gondoskodik a legegyszerűbb engedélyezési esetről (azaz a nem hitelesített kérelmek elutasításáról), az alkalmazás részletesebb engedélyezési viselkedést igényelhet, például csak egy adott felhasználói csoportra korlátozhatja a hozzáférést. Bizonyos esetekben egyéni programkódot kell írnia a bejelentkezett felhasználó hozzáférésének engedélyezéséhez vagy megtagadásához. Más esetekben előfordulhat, hogy a App Service vagy az Ön személyazonosság-szolgáltatója a kód módosítása nélkül tud segítséget nyújtani.

- [Kiszolgáló szintje](#server-level-windows-apps-only)
- [Identitás-szolgáltató szintje](#identity-provider-level)
- [Alkalmazás szintje](#application-level)

### <a name="server-level-windows-apps-only"></a>Kiszolgáló szintje (csak Windows-alkalmazások esetén)

Bármely Windows-alkalmazás esetében megadhatja az IIS-webkiszolgáló engedélyezési viselkedését a *web. config* fájl szerkesztésével. A Linux-alkalmazások nem használják az IIS-t, és nem konfigurálhatók a *web. config*fájlon keresztül.

1. Navigáljon `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. A App Service-fájlok böngésző-tallózójában navigáljon a *hely/wwwroot*elemre. Ha a *web. config* nem létezik, hozza létre **+**  > **új fájl**lehetőség kiválasztásával. 

1. A szerkesztéshez válassza a *web. config* ceruza elemet. Adja hozzá a következő konfigurációs kódot, és kattintson a **Mentés**gombra. Ha a *web. config* fájl már létezik, egyszerűen adja hozzá a `<authorization>` elemet az összeshez. Adja hozzá azokat a fiókokat, amelyeket engedélyezni szeretne a `<allow>` elemben.

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <configuration>
       <system.web>
          <authorization>
            <allow users="user1@contoso.com,user2@contoso.com"/>
            <deny users="*"/>
          </authorization>
       </system.web>
    </configuration>
    ```

### <a name="identity-provider-level"></a>Identitás-szolgáltató szintje

Az identitás-szolgáltató bizonyos kulcsrakész engedélyezést is biztosíthat. Például:

- [Azure app Service](configure-authentication-provider-aad.md)esetében a [vállalati szintű hozzáférés](../active-directory/manage-apps/what-is-access-management.md) közvetlenül az Azure ad-ben is kezelhető. Útmutatásért lásd: [felhasználó hozzáférésének eltávolítása egy alkalmazáshoz](../active-directory/manage-apps/methods-for-removing-user-access.md).
- A [Google](configure-authentication-provider-google.md)-ban a [szervezethez](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) tartozó Google API-projektek konfigurálhatók úgy, hogy csak a szervezet felhasználói számára engedélyezzenek hozzáférést (lásd: a [Google **OAuth 2,0** -támogatás beállítása oldal](https://support.google.com/cloud/answer/6158849?hl=en)).

### <a name="application-level"></a>Alkalmazás szintje

Ha a többi szint valamelyike nem rendelkezik a szükséges engedélyekkel, vagy ha a platform vagy az identitás szolgáltatója nem támogatott, egyéni kódot kell írnia a felhasználók engedélyezéséhez a [felhasználói jogcímek](#access-user-claims)alapján.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: felhasználók hitelesítése és engedélyezése teljes körű (Windows)](app-service-web-tutorial-auth-aad.md)
> [oktatóanyag: felhasználók teljes körű hitelesítése és engedélyezése (Linux)](containers/tutorial-auth-aad.md)
