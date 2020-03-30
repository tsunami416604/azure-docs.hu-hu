---
title: Az AuthN/AuthO fejlett használata
description: Ismerje meg, hogyan szabhatja testre a hitelesítési és engedélyezési szolgáltatás az App Service különböző forgatókönyvek, és a felhasználói jogcímek és a különböző jogkivonatok.
ms.topic: article
ms.date: 10/24/2019
ms.custom: seodec18
ms.openlocfilehash: d57b196bf95ebdf31bc459ad4b9d718fd32ca495
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79280832"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>A hitelesítés és az engedélyezés speciális használata az Azure App Service szolgáltatásban

Ez a cikk bemutatja, hogyan szabhatja testre a beépített [hitelesítést és engedélyezést az App Service-ben,](overview-authentication-authorization.md)és hogyan kezelheti az identitást az alkalmazásból. 

A gyors kezdéshez tekintse meg az alábbi útmutatók egyikét:

* [Oktatóanyag: A felhasználók hitelesítése és engedélyezése végpontok között az Azure App Service (Windows) szolgáltatásban](app-service-web-tutorial-auth-aad.md)
* [Oktatóanyag: A felhasználók hitelesítése és engedélyezése végpontok között az Azure App Service for Linux-ban](containers/tutorial-auth-aad.md)
* [Az alkalmazás konfigurálása az Azure Active Directory-bejelentkezés használatára](configure-authentication-provider-aad.md)
* [Az alkalmazás konfigurálása a Facebook-bejelentkezés használatára](configure-authentication-provider-facebook.md)
* [Az alkalmazás konfigurálása a Google-bejelentkezés használatára](configure-authentication-provider-google.md)
* [Az alkalmazás konfigurálása a Microsoft-fiókbejelentkezés használatára](configure-authentication-provider-microsoft.md)
* [Az alkalmazás konfigurálása a Twitter-bejelentkezés használatára](configure-authentication-provider-twitter.md)

## <a name="use-multiple-sign-in-providers"></a>Több bejelentkezési szolgáltató használata

A portál konfigurációja nem kínál kulcsrakész módot arra, hogy több bejelentkezési szolgáltatót mutasson be a felhasználóknak (például a Facebooknak és a Twitternek). Azonban nem nehéz hozzáadni a funkciót az alkalmazáshoz. A lépések a következők:

Először az Azure Portal **hitelesítése / engedélyezése** lapján konfigurálja az engedélyezni kívánt identitásszolgáltatót.

A **Művelet műveletben, ha a kérés nincs hitelesítve,** jelölje be **a Névtelen kérések engedélyezése (nincs művelet)** lehetőséget.

A bejelentkezési lapon, a navigációs sávon vagy az alkalmazás bármely más helyén adjon meg egy bejelentkezési hivatkozást az engedélyezett szolgáltatókhoz (`/.auth/login/<provider>`. Példa:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Amikor a felhasználó rákattint az egyik hivatkozásra, megnyílik a megfelelő bejelentkezési lap a felhasználó bejelentkezéséhez.

Ha a bejelentkezés után a felhasználót át szeretné `post_login_redirect_url` irányítani egy egyéni URL-címre, használja a lekérdezési karakterlánc paramétert (nem tévesztendő össze az átirányításuri-val az identitásszolgáltató konfigurációjában). Ha például a bejelentkezés `/Home/Index` után a felhasználóhoz szeretne navigálni, használja a következő HTML-kódot:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Szolgáltatók jogkivonatjainak érvényesítése

Az ügyfél által irányított bejelentkezésben az alkalmazás manuálisan jelentkezik be a felhasználóba a szolgáltatónak, majd elküldi a hitelesítési jogkivonatot az App Service-nek érvényesítésre (lásd: [Hitelesítési folyamat).](overview-authentication-authorization.md#authentication-flow) Ez az ellenőrzés önmagában valójában nem biztosít hozzáférést a kívánt alkalmazás-erőforrásokhoz, de a sikeres érvényesítés egy munkamenet-jogkivonatot biztosít, amely segítségével hozzáférhet az alkalmazás erőforrásaihoz. 

A szolgáltatói jogkivonat érvényesítéséhez az App Service alkalmazást először a kívánt szolgáltatóval kell konfigurálni. Futásidőben, miután lekérte a hitelesítési jogkivonatot `/.auth/login/<provider>` a szolgáltatótól, tegye közzé a jogkivonatot érvényesítéscéljából. Példa: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

A token formátuma a szolgáltatótól függően kis mértékben változik. A részleteket lásd az alábbi táblázatban:

| Szolgáltató értéke | A kérelemtörzsben kötelező | Megjegyzések |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | A `expires_in` szálláshely nem kötelező. <br/>Amikor a jogkivonatot az Élő `wl.basic` szolgáltatásoktól kéri, mindig kérje a hatókört. |
| `google` | `{"id_token":"<id_token>"}` | A `authorization_code` szálláshely nem kötelező. Ha meg van adva, akkor `redirect_uri` a szálláshely is mellékelhető. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Használj érvényes [felhasználói hozzáférési tokent](https://developers.facebook.com/docs/facebook-login/access-tokens) a Facebookról. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Ha a szolgáltató jogkivonata sikeresen érvényesítve van, az API-t a `authenticationToken` választörzsben adja vissza, amely a munkamenet-jogkivonat. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Miután rendelkezik ezzel a munkamenet-jogkivonattal, `X-ZUMO-AUTH` a http-kérelmek fejlécének hozzáadásával hozzáférhet a védett alkalmazás-erőforrásokhoz. Példa: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Kijelentkezés a munkamenetből

A felhasználók az alkalmazás végpontjára `GET` `/.auth/logout` küldött kérelemmel kezdeményezhetnek kijelentkezést. A `GET` kérelem a következőket teszi:

- Törli a hitelesítési cookie-kat az aktuális munkamenetből.
- Törli az aktuális felhasználó jogkivonatait a jogkivonat-tárolóból.
- Az Azure Active Directory és a Google egy kiszolgálóoldali kijelentkezést hajt végre az identitásszolgáltatón.

A következő egy egyszerű kijelentkezési hivatkozás egy webhelyen:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Alapértelmezés szerint a sikeres kijelentkezés átirányítja `/.auth/logout/done`az ügyfelet az URL-címre. A kijelentkezés utáni átirányítási lapot a `post_logout_redirect_uri` lekérdezési paraméter hozzáadásával módosíthatja. Példa:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Javasoljuk, hogy [kódolja](https://wikipedia.org/wiki/Percent-encoding) a értékét. `post_logout_redirect_uri`

A teljesen minősített URL-címek használatakor az URL-címet vagy ugyanabban a tartományban kell üzemeltetni, vagy az alkalmazás engedélyezett külső átirányítási URL-címeként kell konfigurálni. A következő példában, hogy `https://myexternalurl.com` átirányítsa, hogy nem található ugyanabban a tartományban:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Futtassa a következő parancsot az [Azure Cloud Shellben:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>URL-töredékek megőrzése

Miután a felhasználók bejelentkeztek az alkalmazásba, általában ugyanannak az oldalnak a `/wiki/Main_Page#SectionZ`szakaszára szeretnének átirányítani őket, például . Mivel azonban [az URL-töredékek](https://wikipedia.org/wiki/Fragment_identifier) (például `#SectionZ`) soha nem kerülnek elküldésre a kiszolgálóra, alapértelmezés szerint nem őrződnek meg, miután az OAuth bejelentkezés befejeződik, és visszairányítja őket az alkalmazásba. A felhasználók ezután az optimálistól elmaradó élményt kapnak, amikor újra a kívánt horgonyhoz kell navigálniuk. Ez a korlátozás minden kiszolgálóoldali hitelesítési megoldásra vonatkozik.

Az App Service-hitelesítésben megőrizheti az URL-töredékeket az OAuth bejelentkezésben. Ehhez állítson be egy `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` alkalmazásbeállítást a következőre. `true` Ezt megteheti az [Azure Portalon,](https://portal.azure.com)vagy egyszerűen futtassa a következő parancsot az [Azure Cloud Shellben:](../cloud-shell/quickstart.md)

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Felhasználói jogcímek elérése

Az App Service speciális fejlécek használatával továbbítja a felhasználói jogcímeket az alkalmazásnak. A külső kérelmek nem állíthatók be ezeket a fejléceket, így csak akkor vannak jelen, ha az App Service állítja be. Néhány példa fejlécek közé:

* X-MS-ÜGYFÉL-MEGBÍZÓ NEVE
* X-MS-CLIENT-PRINCIPAL-ID

A bármely nyelven vagy keretrendszerben írt kód a szükséges információkat letudja venni ezekből a fejlécekből. A ASP.NET 4.6-os alkalmazások esetén a **ClaimsPrincipal** automatikusan be van állítva a megfelelő értékekkel. ASP.NET Core azonban nem biztosít olyan hitelesítési köztes szoftvert, amely integrálható az App Service felhasználói jogcímekkel. A kerülő megoldásról a [MaximeRouiller.Azure.AppService.EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth)címen nyújt elő megoldást.

Az alkalmazás további részleteket is megkaphat `/.auth/me`a hitelesített felhasználóról a hívással. A Mobile Apps server SDK-k segítő módszereket biztosítanak az adatok kal való kapcsolathoz. További információ: [Az Azure Mobile Apps Node.js SDK használata](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity)és [a .NET háttérkiszolgáló SDK használata az Azure Mobile Apps alkalmazáshoz](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info)című témakörben talál.

## <a name="retrieve-tokens-in-app-code"></a>Tokenek lekérése az alkalmazáskódban

A kiszolgálókódból a szolgáltatóspecifikus jogkivonatok at a kérelem fejlécébe injektálják, így könnyen elérheti őket. Az alábbi táblázat a lehetséges tokenfejlécneveket mutatja be:

| Szolgáltató | Fejlécnevek |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook Token | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft-fiók | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Az ügyfélkódból (például egy mobilalkalmazásból vagy a `GET` böngészőben `/.auth/me`található JavaScriptből) HTTP-kérést kell küldeni a rendszernek. A visszaadott JSON rendelkezik a szolgáltató-specifikus jogkivonatok.

> [!NOTE]
> A hozzáférési jogkivonatok a szolgáltató erőforrásainak elérésére szolgálnak, így csak akkor vannak jelen, ha a szolgáltatót ügyféltokkal konfigurálja. A frissítési jogkivonatok bekérésáról a Hozzáférési jogkivonatok frissítése.

## <a name="refresh-identity-provider-tokens"></a>Identitásszolgáltató-tokenek frissítése

Amikor a szolgáltató hozzáférési jogkivonata (nem a [munkamenet-jogkivonat)](#extend-session-token-expiration-grace-period)lejár, újra kell hitelesítenie a felhasználót, mielőtt újra használná a jogkivonatot. Elkerülheti a jogkivonat `GET` lejárati `/.auth/refresh` azáltal, hogy az alkalmazás végpontjára hívást kezdeményez. Híváskor az App Service automatikusan frissíti a jogkivonat-tárolóban a hitelesített felhasználó hozzáférési jogkivonatait. További kérelmek jogkivonatok az alkalmazáskód a frissített jogkivonatok. Azonban a token frissítési dolgozni, a jogkivonat-tároló tartalmaznia kell [a szolgáltató frissítési jogkivonatokat.](https://auth0.com/learn/refresh-tokens/) A frissítési tokenek beszerzésének módját az egyes szolgáltatók dokumentálják, de az alábbi lista rövid összefoglaló:

- **Google**: Egy `access_type=offline` lekérdezési karakterlánc-paraméter hozzáfűzése az `/.auth/login/google` API-híváshoz. Ha a Mobile Apps SDK-t használja, hozzáadhatja a paramétert az `LogicAsync` egyik túlterheléshez (lásd: Google [Tokenek frissítése).](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)
- **Facebook**: Nem biztosít frissítési jogkivonatokat. A hosszú élettartamú tokenek 60 napon belül lejárnak (lásd: [A Facebook lejárata és a hozzáférési jogkitok kiterjesztése).](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)
- **Twitter**: A hozzáférési tokenek nem járnak le [(lásd: Twitter OAuth GYIK](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Microsoft-fiók**: A [Microsoft-fiók hitelesítési beállításainak konfigurálásakor](configure-authentication-provider-microsoft.md)válassza ki a `wl.offline_access` hatókört.
- **Azure Active**Directory [https://resources.azure.com](https://resources.azure.com): A alkalmazásban hajtsa végre a következő lépéseket:
    1. A lap tetején válassza az **Olvasás/írás**lehetőséget.
    2. A bal oldali böngészőben keresse meg az **előfizetések** > **_\<előfizetési\_neve_** > **_\<\_ _** > **resourceGroups** > **_\<\_erőforráscsoport\_nevét,>_**  > a >  > **Microsoft.Web** > **sites**alkalmazás nevét **providers**> > a **konfigurációs****beállításokat.** 
    3. Kattintson a **Szerkesztés** gombra.
    4. Módosítsa a következő tulajdonságot. Cserélje le _ \<az alkalmazásazonosító\_>_ az Azure Active Directory alkalmazásazonosítóját az elérni kívánt szolgáltatás.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Kattintson **az Elhelyezés gombra.** 

Miután a szolgáltató konfigurálva van, [megtalálhatja a frissítési jogkivonatot és a hozzáférési jogkivonat lejárati idejét](#retrieve-tokens-in-app-code) a jogkivonat-tárolóban. 

A hozzáférési jogkivonat bármikor frissítéséhez `/.auth/refresh` hívjon bármilyen nyelven. A következő kódrészlet a jQuery segítségével frissíti a hozzáférési jogkivonatokat egy JavaScript-ügyfélről.

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

Ha egy felhasználó visszavonja az alkalmazásnak adott `/.auth/me` engedélyeket, `403 Forbidden` előfordulhat, hogy a hívás sikertelen lesz egy válasszal. A hibák diagnosztizálásához ellenőrizze az alkalmazásnaplókat a részletekért.

## <a name="extend-session-token-expiration-grace-period"></a>Munkamenet-jogkivonat lejárati türelmi időszakának meghosszabbítása

A hitelesített munkamenet 8 óra elteltével lejár. A hitelesített munkamenet lejárta után alapértelmezés szerint 72 órás türelmi idő áll fenn. Ebben a türelmi időszakban a munkamenet-jogkivonat frissítése az App Service-lel a felhasználó újrahitelesítése nélkül frissítheti. Csak akkor `/.auth/refresh` hívhatja meg, ha a munkamenet-jogkivonat érvénytelenné válik, és nem kell nyomon követnie a jogkivonat lejárati saját magát. A 72 órás türelmi időszak lejárta után a felhasználónak újra be kell jelentkeznie egy érvényes munkamenet-jogkivonat lekéréséhez.

Ha a 72 óra nem elegendő idő az Ön számára, meghosszabbíthatja ezt a lejárati időszakot. A lejárat hosszú időszakra történő meghosszabbítása jelentős biztonsági következményekkel járhat (például ha egy hitelesítési jogkivonat kiszivárgott vagy ellopták). Ezért hagyja az alapértelmezett 72 órában, vagy állítsa be a meghosszabbítási időszakot a legkisebb értékre.

Az alapértelmezett lejárati időszak meghosszabbításához futtassa a következő parancsot a [Felhőrendszerhéjban](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> A türelmi időszak csak az App Service hitelesített munkamenetére vonatkozik, az identitásszolgáltatók jogkivonataira nem. A lejárt szolgáltatójogkivonatokhoz nincs türelmi idő. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>A bejelentkezési fiókok tartományának korlátozása

A Microsoft-fiók és az Azure Active Directory segítségével több tartományból is bejelentkezhetsz. A Microsoft-fiók például _engedélyezi a outlook.com_, _live.com_és _hotmail.com_ fiókokat. Az Azure AD lehetővé teszi tetszőleges számú egyéni tartományok a bejelentkezési fiókok. Előfordulhat azonban, hogy a felhasználókat közvetlenül a saját márkás Azure AD `contoso.com`bejelentkezési oldalára (például) szeretné felgyorsítani. A bejelentkezési fiókok tartománynevének javaslatához kövesse az alábbi lépéseket.

A [https://resources.azure.com](https://resources.azure.com)alkalmazásban keresse meg az **előfizetési** > **_\<előfizetések\_neve_** > **resourceGroups** > **_\<\_erőforráscsoport\_nevét>_**  > szolgáltatók**microsoft.webwebhelyek** > **sites** > **_\<\_ _** **providers** > alkalmazás nevét  > >**config** > **authsettings**. 

Kattintson a **Szerkesztés**gombra, módosítsa a következő tulajdonságot, majd kattintson az **Elhelyezés**gombra. Ügyeljen arra, hogy a _ \<tartománynevet\_>_ a kívánt tartományra cserélje.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```

Ez a beállítás `domain_hint` hozzáfűzi a lekérdezési karakterlánc paramétert a bejelentkezési átirányítási URL-címhez. 

> [!IMPORTANT]
> Az ügyfél eltávolíthatja a paramétert az `domain_hint` átirányítási URL-cím kézhezvétele után, majd egy másik tartománnyal jelentkezhet be. Tehát rövid idő ez függvény van megfelelő, -a' nem egy biztonság vonás.
>

## <a name="authorize-or-deny-users"></a>Felhasználók engedélyezése vagy megtagadása

Bár az App Service a legegyszerűbb engedélyezési esetet (azaz a nem hitelesített kérelmeket utasítja el), az alkalmazás részletesebb engedélyezési viselkedést igényelhet, például a hozzáférést csak a felhasználók egy adott csoportjára korlátozza. Bizonyos esetekben egyéni alkalmazáskódot kell írnia a bejelentkezett felhasználó elérésének engedélyezéséhez vagy megtagadásához. Más esetekben előfordulhat, hogy az App Service vagy az identitásszolgáltató kódmódosítás nélkül is tud segíteni.

- [Kiszolgálói szint](#server-level-windows-apps-only)
- [Identitásszolgáltató szintje](#identity-provider-level)
- [Alkalmazási szint](#application-level)

### <a name="server-level-windows-apps-only"></a>Kiszolgálói szint (csak Windows-alkalmazások)

Bármely Windows-alkalmazáshoz meghatározhatja az IIS webkiszolgáló engedélyezési viselkedését a *Web.config* fájl szerkesztésével. A Linux-alkalmazások nem használják az IIS-t, és nem konfigurálhatók a *Web.config fájlon*keresztül.

1. Navigálás a`https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. Az App Service-fájlok böngészőkezelőjében keresse meg a *webhelyet/wwwroot*webhelyet. Ha a *Web.config* nem létezik, **+**  > hozza létre az **Új fájl**lehetőséget választva. 

1. A szerkesztéshez jelölje ki a *Web.config* ceruzáját. Adja hozzá a következő konfigurációs kódot, és kattintson a **Mentés gombra.** Ha *a Web.config* már `<authorization>` létezik, csak adja hozzá az elemet mindennel. Adja hozzá az `<allow>` elembe engedélyezni kívánt fiókokat.

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

### <a name="identity-provider-level"></a>Identitásszolgáltató szintje

Az identitásszolgáltató bizonyos kulcsrakész engedélyezést adhat meg. Példa:

- Az [Azure App Service](configure-authentication-provider-aad.md)esetén közvetlenül az Azure AD-ben kezelheti a [nagyvállalati szintű hozzáférést.](../active-directory/manage-apps/what-is-access-management.md) További információt a [Felhasználó alkalmazáshoz való hozzáférésének eltávolítása.](../active-directory/manage-apps/methods-for-removing-user-access.md)
- A [Google](configure-authentication-provider-google.md)esetében a [szervezethez](https://cloud.google.com/resource-manager/docs/cloud-platform-resource-hierarchy#organizations) tartozó Google API-projektek beállíthatók úgy, hogy csak a szervezet felhasználói számára engedélyezzeak hozzáférést (lásd: [A Google **OAuth 2.0 támogatási oldalának beállítása).** ](https://support.google.com/cloud/answer/6158849?hl=en)

### <a name="application-level"></a>Alkalmazási szint

Ha a többi szint valamelyike nem adja meg a szükséges jogosultságot, vagy ha a platform vagy az identitásszolgáltató nem támogatott, egyéni kódot kell írnia, amely engedélyezi a felhasználóknak a [felhasználói jogcímek](#access-user-claims)alapján.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: A felhasználók hitelesítése és engedélyezése végpontok között (Windows)](app-service-web-tutorial-auth-aad.md)
> [Oktatóanyag: A felhasználók hitelesítése és engedélyezése végpontok között (Linux)](containers/tutorial-auth-aad.md)
