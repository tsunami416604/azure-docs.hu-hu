---
title: A AuthN/AuthZ speciális használata
description: Megtudhatja, hogyan szabhatja testre a hitelesítési és engedélyezési funkciót App Service különböző forgatókönyvek esetén, valamint felhasználói jogcímeket és különböző jogkivonatokat kaphat.
ms.topic: article
ms.date: 07/08/2020
ms.custom: seodec18
ms.openlocfilehash: 52213999ae0ec9f6891c8ec10ab65471926e87d2
ms.sourcegitcommit: 4913da04fd0f3cf7710ec08d0c1867b62c2effe7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2020
ms.locfileid: "88208029"
---
# <a name="advanced-usage-of-authentication-and-authorization-in-azure-app-service"></a>A hitelesítés és az engedélyezés speciális használata Azure App Service

Ez a cikk bemutatja, hogyan szabhatja testre a [app Service beépített hitelesítését és engedélyezését](overview-authentication-authorization.md), és hogyan kezelheti az alkalmazás identitását. 

A gyors kezdéshez tekintse meg a következő oktatóanyagok egyikét:

* [Oktatóanyag: Felhasználók hitelesítése és engedélyezése végpontok között az Azure App Service-ben](tutorial-auth-aad.md)
* [Az alkalmazás konfigurálása az Azure Active Directory-bejelentkezés használatára](configure-authentication-provider-aad.md)
* [Az alkalmazás konfigurálása a Facebook-bejelentkezés használatára](configure-authentication-provider-facebook.md)
* [Az alkalmazás konfigurálása a Google-bejelentkezés használatára](configure-authentication-provider-google.md)
* [Az alkalmazás konfigurálása a Microsoft-fiókbejelentkezés használatára](configure-authentication-provider-microsoft.md)
* [Az alkalmazás konfigurálása a Twitter-bejelentkezés használatára](configure-authentication-provider-twitter.md)
* [Alkalmazás konfigurálása OpenID Connect-szolgáltató használatával történő bejelentkezéshez (előzetes verzió)](configure-authentication-provider-openid-connect.md)

## <a name="use-multiple-sign-in-providers"></a>Több bejelentkezési szolgáltató használata

A portál konfigurációja nem biztosít kulcsrakész módot arra, hogy több bejelentkezési szolgáltatót nyújtson be a felhasználók számára (például Facebook és Twitter). Azonban nem nehéz felvenni a funkciót az alkalmazáshoz. A lépéseket a következőképpen vázoljuk fel:

Először a Azure Portal **hitelesítés/engedélyezés** lapján konfigurálja az összes engedélyezni kívánt identitás-szolgáltatót.

**Ha a kérelem nem hitelesítve van**, válassza a **Névtelen kérelmek engedélyezése (nincs művelet)** lehetőséget.

A bejelentkezési oldalon vagy a navigációs sávon vagy az alkalmazás bármely más helyén vegyen fel egy bejelentkezési hivatkozást az összes engedélyezett szolgáltatóhoz ( `/.auth/login/<provider>` ). Például:

```html
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Amikor a felhasználó rákattint az egyik hivatkozásra, megnyílik a megfelelő bejelentkezési oldal, amely bejelentkezik a felhasználóba.

Ha át szeretné irányítani a felhasználói bejelentkezést egy egyéni URL-címre, használja a `post_login_redirect_url` lekérdezési karakterlánc paramétert (a rendszer nem tévesztendő össze az identitás-szolgáltatói konfigurációban található átirányítási URI azonosítóval). Ha például a felhasználót a bejelentkezés után szeretné navigálni `/Home/Index` , használja a következő HTML-kódot:

```html
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="validate-tokens-from-providers"></a>Jogkivonatok érvényesítése a szolgáltatóktól

Az ügyfél által irányított bejelentkezés során az alkalmazás manuálisan bejelentkezik a felhasználónak a szolgáltatóba, majd elküldi a hitelesítési jogkivonatot az ellenőrzéshez App Service (lásd: [hitelesítési folyamat](overview-authentication-authorization.md#authentication-flow)). Ez az érvényesítés önmagában nem biztosít hozzáférést a kívánt alkalmazás-erőforrásokhoz, de sikeres ellenőrzés esetén egy munkamenet-tokent ad meg, amely az alkalmazás erőforrásainak elérésére használható. 

A szolgáltatói jogkivonat érvényesítéséhez a App Service alkalmazást először konfigurálni kell a kívánt szolgáltatóval. Futásidőben a szolgáltatótól kapott hitelesítési jogkivonat lekérése után a tokent az `/.auth/login/<provider>` érvényesítéshez tegye közzé. Például: 

```
POST https://<appname>.azurewebsites.net/.auth/login/aad HTTP/1.1
Content-Type: application/json

{"id_token":"<token>","access_token":"<token>"}
```

A jogkivonat formátuma a szolgáltatótól függően némileg eltér. A részletekért tekintse meg az alábbi táblázatot:

| Szolgáltató értéke | A kérelem törzsében kötelező | Megjegyzések |
|-|-|-|
| `aad` | `{"access_token":"<access_token>"}` | |
| `microsoftaccount` | `{"access_token":"<token>"}` | A `expires_in` tulajdonság megadása nem kötelező. <br/>Ha az élő szolgáltatásokból kér jogkivonatot, mindig kérje a `wl.basic` hatókört. |
| `google` | `{"id_token":"<id_token>"}` | A `authorization_code` tulajdonság megadása nem kötelező. Ha meg van adva, azt is elvégezheti a `redirect_uri` tulajdonsággal együtt. |
| `facebook`| `{"access_token":"<user_access_token>"}` | Érvényes [felhasználói hozzáférési tokent](https://developers.facebook.com/docs/facebook-login/access-tokens) használhat a Facebook-ból. |
| `twitter` | `{"access_token":"<access_token>", "access_token_secret":"<acces_token_secret>"}` | |
| | | |

Ha a szolgáltatói jogkivonat ellenőrzése sikeresen megtörtént, az API a `authenticationToken` Válasz törzsében, azaz a munkamenet-jogkivonatban tér vissza. 

```json
{
    "authenticationToken": "...",
    "user": {
        "userId": "sid:..."
    }
}
```

Ha ezzel a munkamenet-jogkivonattal rendelkezik, a védett alkalmazás erőforrásaihoz a `X-ZUMO-AUTH` fejlécet a http-kérelmekhez hozzáadva érheti el. Például: 

```
GET https://<appname>.azurewebsites.net/api/products/1
X-ZUMO-AUTH: <authenticationToken_value>
```

## <a name="sign-out-of-a-session"></a>Kijelentkezés a munkamenetből

A felhasználók elindíthatnak egy kijelentkezést `GET` az alkalmazás végpontjának küldött kérelem elküldésével `/.auth/logout` . A `GET` kérelem a következő műveleteket végzi el:

- Törli a hitelesítési cookie-kat az aktuális munkamenetből.
- Törli az aktuális felhasználó jogkivonatait a jogkivonat-tárolóból.
- A Azure Active Directory és a Google esetében a kiszolgálóoldali kijelentkezést hajt végre az identitás-szolgáltatón.

A következő egy egyszerű kijelentkezési hivatkozás egy webhelyen:

```html
<a href="/.auth/logout">Sign out</a>
```

Alapértelmezés szerint a sikeres kijelentkezés az URL-címre irányítja át az ügyfelet `/.auth/logout/done` . A kijelentkezési átirányítási lapot a lekérdezési paraméter hozzáadásával módosíthatja `post_logout_redirect_uri` . Például:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Javasoljuk, hogy [kódolja](https://wikipedia.org/wiki/Percent-encoding) a értékét `post_logout_redirect_uri` .

Teljes URL-címek használatakor az URL-címnek vagy ugyanabban a tartományban kell lennie, vagy az alkalmazás számára engedélyezett külső átirányítási URL-címnek kell lennie. A következő példában a `https://myexternalurl.com` nem ugyanabban a tartományban üzemeltetett átirányítás:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

Futtassa a következő parancsot a [Azure Cloud Shellban](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>URL-töredékek megőrzése

Miután a felhasználók bejelentkeznek az alkalmazásba, általában ugyanarra a lapra szeretnének átirányítani, például: `/wiki/Main_Page#SectionZ` . Mivel azonban a rendszer soha nem küldi el a kiszolgálónak az [URL-töredékeket](https://wikipedia.org/wiki/Fragment_identifier) (például `#SectionZ` ), a rendszer alapértelmezés szerint nem őrzi meg őket a OAuth bejelentkezés befejezése után, és visszairányítja az alkalmazásba. A felhasználók ezt követően optimális élményt kapnak, amikor újra kell navigálni a kívánt horgonyhoz. Ez a korlátozás az összes kiszolgálóoldali hitelesítési megoldásra vonatkozik.

App Service hitelesítésnél megőrizheti az URL-töredékeket a OAuth-bejelentkezés során. Ehhez állítson be egy nevű alkalmazást `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` `true` . Ezt megteheti a [Azure Portalban](https://portal.azure.com), vagy egyszerűen futtathatja a következő parancsot a [Azure Cloud Shellban](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Felhasználói jogcímek elérése

A App Service speciális fejlécek használatával átadja az alkalmazásnak a felhasználói jogcímeket. A külső kérelmek nem állíthatják be ezeket a fejléceket, ezért csak akkor jelennek meg, ha App Service. A fejlécek például a következők:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

A bármely nyelven vagy keretrendszerben írt kód a fenti fejlécek által igényelt információkat kérheti le. A ASP.NET 4,6 alkalmazások esetében a **ClaimsPrincipal** automatikusan a megfelelő értékekkel van beállítva. ASP.NET Core azonban nem biztosít olyan hitelesítési middleware-t, amely App Service felhasználói jogcímekbe integrálódik. Megkerülő megoldásért tekintse meg a következőt: [MaximeRouiller. Azure. AppService. EasyAuth](https://github.com/MaximRouiller/MaximeRouiller.Azure.AppService.EasyAuth).

Az alkalmazás a meghívásával további részleteket is kérhet a hitelesített felhasználóról `/.auth/me` . Az Mobile Apps Server SDK-k segítő módszereket biztosítanak az adatkezeléshez. További információ: [az azure Mobile Apps Node.js SDK használata](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity), valamint az Azure-hoz [készült .net backend Server SDK-val való együttműködés. Mobile apps](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

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

Küldjön HTTP-kérést az ügyfél kódjából (például egy mobil alkalmazásból vagy egy böngészőből származó JavaScriptből) `GET` `/.auth/me` . A visszaadott JSON a szolgáltatóra jellemző jogkivonatokat tartalmaz.

> [!NOTE]
> A hozzáférési tokenek a szolgáltatói erőforrások eléréséhez szükségesek, így csak akkor jelennek meg, ha a szolgáltatót egy ügyfél titkos kulcsával konfigurálja. A frissítési tokenek beszerzésével kapcsolatban lásd: hozzáférési tokenek frissítése.

## <a name="refresh-identity-provider-tokens"></a>Identitás-szolgáltatói tokenek frissítése

Ha a szolgáltató hozzáférési jogkivonata (nem a [munkamenet jogkivonata](#extend-session-token-expiration-grace-period)) lejár, újra hitelesítenie kell a felhasználót, mielőtt újra felhasználja a tokent. A jogkivonat lejáratának elkerüléséhez `GET` hívja meg az `/.auth/refresh` alkalmazás végpontját. A híváskor App Service automatikusan frissíti a hitelesített felhasználó hozzáférési jogkivonatait a jogkivonat-tárolóban. Az alkalmazás kódjának a jogkivonatokra vonatkozó későbbi kérelmei megkapják a frissített jogkivonatokat. Ahhoz azonban, hogy a jogkivonat-frissítés működjön, a jogkivonat-tárolónak tartalmaznia kell a szolgáltató [frissítési jogkivonatait](https://auth0.com/learn/refresh-tokens/) . A frissítési tokenek beszerzésének módját az egyes szolgáltatók dokumentálják, de a következő lista egy rövid összefoglalás:

- **Google**: fűzze hozzá a `access_type=offline` lekérdezési karakterlánc paramétert az `/.auth/login/google` API-híváshoz. Ha a Mobile Apps SDK-t használja, hozzáadhatja a paramétert a `LogicAsync` túlterhelések egyikéhez (lásd: [Google frissítési jogkivonatok](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: nem biztosít frissítési jogkivonatokat. A hosszú élettartamú tokenek 60 nap múlva lejárnak (lásd [a Facebook lejáratát és a hozzáférési tokenek kiterjesztését](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: a hozzáférési tokenek nem járnak le (lásd: [Twitter OAuth – gyakori kérdések](https://developer.twitter.com/en/docs/basics/authentication/FAQ)).
- **Microsoft-fiók**: a [Microsoft-fiók hitelesítési beállításainak konfigurálásakor](configure-authentication-provider-microsoft.md)válassza ki a `wl.offline_access` hatókört.
- **Azure Active Directory**: a-ben [https://resources.azure.com](https://resources.azure.com) végezze el a következő lépéseket:
    1. Az oldal tetején válassza az **írás/írás**lehetőséget.
    2. A bal oldali böngészőben navigáljon az **előfizetések** > * *_ \<subscription\_name_** > **resourceGroups** > * *_ \<resource\_group\_name> _* * > **szolgáltatók**  >  **Microsoft. Web**  >  **Sites** > * *_ \<app\_name> _ * * > **config**  >  **authsettings elemre**. 
    3. Kattintson a **Szerkesztés** gombra.
    4. Módosítsa a következő tulajdonságot. Cserélje le az helyére az _\<app\_id>_ elérni kívánt szolgáltatás Azure Active Directory alkalmazás-azonosítóját.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    5. Kattintson a **put**elemre. 

Miután konfigurálta a szolgáltatót, [megkeresheti a frissítési jogkivonatot és a hozzáférési token lejárati idejét](#retrieve-tokens-in-app-code) a jogkivonat-tárolóban. 

Ha bármikor frissíteni szeretné a hozzáférési jogkivonatot, csak `/.auth/refresh` tetszőleges nyelven hívhat meg. A következő kódrészlet a jQuery használatával frissíti a hozzáférési jogkivonatait egy JavaScript-ügyfélről.

```javascript
function refreshTokens() {
  let refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Ha a felhasználó visszavonja az alkalmazás számára biztosított engedélyeket, a hívása `/.auth/me` sikertelen lehet `403 Forbidden` . A hibák diagnosztizálásához az alkalmazás naplófájljaiban tájékozódhat a részletekről.

## <a name="extend-session-token-expiration-grace-period"></a>Munkamenet-jogkivonat lejárati türelmi időszakának kiterjesztése

A hitelesített munkamenet 8 óra elteltével lejár. A hitelesített munkamenet lejárta után a rendszer alapértelmezés szerint 72 órás türelmi időszakot használ. A türelmi időszakon belül lehetősége van a munkamenet-jogkivonat frissítésére App Service a felhasználó ismételt hitelesítése nélkül. Csak akkor hívható meg `/.auth/refresh` , ha a munkamenet-jogkivonat érvénytelenné válik, és nem kell nyomon követnie a jogkivonat lejáratát. Ha a 72 órás türelmi időszak lejár, a felhasználónak újra be kell jelentkeznie, hogy érvényes munkamenet-tokent kapjon.

Ha a 72 órán belül nincs elég idő, kiterjesztheti ezt a lejárati időszakot. Ha hosszabb időn keresztül kiterjeszti a lejáratot, jelentős biztonsági következményekkel járhat (például egy hitelesítési jogkivonat kiszivárgásakor vagy ellopásakor). Ezért hagyja meg az alapértelmezett 72 órát, vagy állítsa a kiterjesztési időszakot a legkisebb értékre.

Az alapértelmezett lejárati időszak meghosszabbításához futtassa a következő parancsot a [Cloud Shellban](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> A türelmi időszak csak a App Service hitelesített munkamenetre vonatkozik, nem az Identitáskezelő jogkivonatára. A lejárt szolgáltatói jogkivonatok esetében nincs türelmi időszak. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Bejelentkezési fiókok tartományának korlátozása

A Microsoft-fiók és a Azure Active Directory egyaránt lehetővé teszi több tartományból való bejelentkezést. A Microsoft-fiók például lehetővé teszi a _Outlook.com_, a _live.com_és a _hotmail.com_ fiók használatát. Az Azure AD lehetővé teszi a bejelentkezési fiókok tetszőleges számú egyéni tartományának használatát. Előfordulhat azonban, hogy a felhasználóknak azonnal fel kell gyorsítania a saját márkás Azure AD bejelentkezési oldalát (például `contoso.com` ). Ha a bejelentkezési fiókok tartománynevét szeretné javasolni, kövesse az alábbi lépéseket.

A alkalmazásban [https://resources.azure.com](https://resources.azure.com) navigáljon az **előfizetések** > * *_ \<subscription\_name_** > **resourceGroups** > * *_ \<resource\_group\_name> _* * > **szolgáltatók**  >  **Microsoft. Web**  >  **Sites** > * *_ \<app\_name> _ * * > **config**  >  **authsettings elemre**. 

Kattintson a **Szerkesztés**gombra, módosítsa a következő tulajdonságot, majd kattintson a **put**elemre. Ne felejtse el lecserélni _\<domain\_name>_ a kívánt tartományra.

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

Bármely Windows-alkalmazás esetében a *Web.config* fájl szerkesztésével megadhatja az IIS-webkiszolgáló engedélyezési viselkedését. A Linux-alkalmazások nem használják az IIS-t, és nem konfigurálhatók *Web.configon *keresztül.

1. Navigáljon ide: `https://<app-name>.scm.azurewebsites.net/DebugConsole`

1. A App Service-fájlok böngésző-tallózójában navigáljon a *hely/wwwroot*elemre. Ha a *Web.config* nem létezik, hozza létre az **+**  >  **új fájl**kiválasztásával. 

1. Válassza ki a ceruzát *Web.config* a szerkesztéshez. Adja hozzá a következő konfigurációs kódot, és kattintson a **Mentés**gombra. Ha *Web.config* már létezik, egyszerűen adja hozzá a `<authorization>` elemet a benne található összes elemhez. Adja hozzá azokat a fiókokat, amelyeket engedélyezni szeretne a `<allow>` elemben.

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

## <a name="configure-using-a-file-preview"></a><a name="config-file"> </a>Konfigurálás fájl használatával (előzetes verzió)

Az Auth beállításai opcionálisan konfigurálhatók az üzemelő példány által biztosított fájlon keresztül is. Erre szükség lehet a App Service hitelesítés/engedélyezés bizonyos előzetes verziójú képességeitől.

> [!IMPORTANT]
> Ne feledje, hogy az alkalmazás hasznos, ezért ez a fájl a környezetek között mozog, a [tárolóhelyek](./deploy-staging-slots.md)esetében. Valószínű, hogy egy másik, az egyes tárolóhelyekre rögzített alkalmazás-regisztrációt szeretne használni, és ezekben az esetekben a konfigurációs fájl használata helyett továbbra is a szabványos konfigurációs módszert kell használnia.

### <a name="enabling-file-based-configuration"></a>A fájl alapú konfiguráció engedélyezése

> [!CAUTION]
> Az előzetes verzióban a fájl alapú konfiguráció engedélyezésével letilthatja az alkalmazás App Service hitelesítési/engedélyezési funkciójának kezelését bizonyos ügyfeleken, például a Azure Portal, az Azure CLI és a Azure PowerShell használatával.

1. Hozzon létre egy új JSON-fájlt a konfigurációhoz a projekt gyökérkönyvtárában (a web/Function alkalmazás D:\home\site\wwwroot üzembe helyezése). Adja meg a kívánt konfigurációt a [fájl alapú konfigurációs hivatkozás](#configuration-file-reference)alapján. Meglévő Azure Resource Manager konfigurációjának módosításakor ügyeljen arra, hogy a gyűjteményben rögzített tulajdonságokat a `authsettings` konfigurációs fájlba fordítsa.

2. Módosítsa a meglévő konfigurációt, amely a [Azure Resource Manager](../azure-resource-manager/management/overview.md) API-ban van rögzítve `Microsoft.Web/sites/<siteName>/config/authsettings` . Ennek módosításához használhat [Azure Resource Manager sablont](../azure-resource-manager/templates/overview.md) vagy eszközt, például [Azure erőforrás-kezelőt](https://resources.azure.com/). A authsettings elemre gyűjteményben három tulajdonságot kell megadnia (és el is távolíthatja másokat):

    1.  Beállítás értéke `enabled` "true"
    2.  Beállítás értéke `isAuthFromFile` "true"
    3.  Állítsa a `authFilePath` fájl nevére (például "auth.json")

> [!NOTE]
> A `authFilePath` platformok közötti változó formátuma. Windows rendszeren a relatív és az abszolút elérési út is támogatott. A relatív érték használata javasolt. A Linux esetében jelenleg csak az abszolút elérési utak támogatottak, így a beállítás értéke "/Home/site/wwwroot/auth.json" vagy hasonló lehet.

Ha elvégezte ezt a konfigurációs frissítést, a rendszer a fájl tartalmát fogja használni App Service hitelesítés/engedélyezés viselkedésének meghatározásához az adott helyen. Ha bármikor vissza szeretne térni Azure Resource Manager konfigurációhoz, a `isAuthFromFile` "false" (hamis) értékre állíthatja vissza.

### <a name="configuration-file-reference"></a>Konfigurációs fájl leírása

A konfigurációs fájlra hivatkozó összes titkot [alkalmazási beállításokként](./configure-common.md#configure-app-settings)kell tárolni. Megadhatja a beállításokat, amelyeket szeretne. Győződjön meg arról, hogy a konfigurációs fájl hivatkozásai ugyanazokat a kulcsokat használják.

Az alábbi kimeríti a fájl lehetséges konfigurációs beállításait:

```json
{
    "platform": {
        "enabled": <true|false>
    },
    "globalValidation": {
        "requireAuthentication": <true|false>,
        "unauthenticatedClientAction": "RedirectToLoginPage|AllowAnonymous|Return401|Return403",
        "redirectToProvider": "<default provider alias>",
        "excludedPaths": [
            "/path1",
            "/path2"
        ]
    },
    "identityProviders": {
        "azureActiveDirectory": {
            "enabled": <true|false>,
            "registration": {
                "openIdIssuer": "<issuer url>",
                "clientId": "<app id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_AAD_SECRET",
            },
            "login": {
                "loginParameters": [
                    "paramName1=value1",
                    "paramName2=value2"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "facebook": {
            "enabled": <true|false>,
            "registration": {
                "appId": "<app id>",
                "appSecretSettingName": "APP_SETTING_CONTAINING_FACEBOOK_SECRET"
            },
            "graphApiVersion": "v3.3",
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
        },
        "gitHub": {
            "enabled": <true|false>,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GITHUB_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            }
        },
        "google": {
            "enabled": true,
            "registration": {
                "clientId": "<client id>",
                "clientSecretSettingName": "APP_SETTING_CONTAINING_GOOGLE_SECRET"
            },
            "login": {
                "scopes": [
                    "profile",
                    "email"
                ]
            },
            "validation": {
                "allowedAudiences": [
                    "audience1",
                    "audience2"
                ]
            }
        },
        "twitter": {
            "enabled": <true|false>,
            "registration": {
                "consumerKey": "<consumer key>",
                "consumerSecretSettingName": "APP_SETTING_CONTAINING TWITTER_CONSUMER_SECRET"
            }
        },
        "openIdConnectProviders": {
            "provider name": {
                "enabled": <true|false>,
                "registration": {
                    "clientId": "<client id>",
                    "clientCredential": {
                        "secretSettingName": "<name of app setting containing client secret>"
                    },
                    "openIdConnectConfiguration": {
                        "authorizationEndpoint": "<url specifying authorization endpoint>",
                        "tokenEndpoint": "<url specifying token endpoint>",
                        "issuer": "<url specifying issuer>",
                        "certificationUri": "<url specifying jwks endpoint>",
                        "wellKnownOpenIdConfiguration": "<url specifying .well-known/open-id-configuration endpoint - if this property is set, the other properties of this object are ignored, and authorizationEndpoint, tokenEndpoint, issuer, and certificationUri are set to the corresponding values listed at this endpoint>"
                    }
                },
                "login": {
                    "nameClaimType": "<name of claim containing name>",
                    "scope": [
                        "openid",
                        "profile",
                        "email"
                    ],
                    "loginParameterNames": [
                        "paramName1=value1",
                        "paramName2=value2"
                    ],
                }
            },
            //...
        },
        "login": {
            "routes": {
                "logoutEndpoint": "<logout endpoint>"
            },
            "tokenStore": {
                "enabled": <true|false>,
                "tokenRefreshExtensionHours": "<double>",
                "fileSystem": {
                    "directory": "<directory to store the tokens in if using a file system token store (default)>"
                },
                "azureBlobStorage": {
                    "sasUrlSettingName": "<app setting name containing the sas url for the Azure Blob Storage if opting to use that for a token store>"
                }
            },
            "preserveUrlFragmentsForLogins": <true|false>,
            "allowedExternalRedirectUrls": [
                "https://uri1.azurewebsites.net/",
                "https://uri2.azurewebsites.net/"
            ],
            "cookieExpiration": {
                "convention": "FixedTime|IdentityProviderDerived",
                "timeToExpiration": "<timespan>"
            },
            "nonce": {
                "validateNonce": <true|false>,
                "nonceExpirationInterval": "<timespan>"
            }
        },
        "httpSettings": {
            "requireHttps": <true|false>,
            "routes": {
                "apiPrefix": "<api prefix>"
            },
            "forwardProxy": {
                "convention": "NoProxy|Standard|Custom",
                "customHostHeaderName": "<host header value>",
                "customProtoHeaderName": "<proto header value>"
            }
        }
    }
}
```

## <a name="pin-your-app-to-a-specific-authentication-runtime-version"></a>Alkalmazás rögzítése egy adott hitelesítési futtatókörnyezet-verzióra

A hitelesítés/engedélyezés engedélyezésekor a platform middleware-t a [szolgáltatás áttekintése](overview-authentication-authorization.md#how-it-works)című témakörben leírtak szerint fecskendezik be a http-kérési folyamatba. Ezt a platformot rendszeresen frissítik a platform legújabb frissítéseinek részeként új funkciókkal és újdonságokkal. Alapértelmezés szerint a web vagy Function alkalmazás a platform middleware legújabb verziójában fog futni. Ezek az automatikus frissítések mindig visszafelé kompatibilisek. Azonban abban a ritka esetben, ha ez az automatikus frissítés futásidejű problémát jelent a web vagy Function alkalmazás számára, átmenetileg visszaállíthatja az előző middleware-verziót. Ez a cikk azt ismerteti, hogyan lehet ideiglenesen PIN-kódot rögzíteni az alkalmazások számára a hitelesítési middleware egy adott verziójához.

### <a name="automatic-and-manual-version-updates"></a>Az automatikus és a manuális verzió frissítései 

Az alkalmazást az alkalmazás egy beállításának beállításával rögzítheti a platform middleware egy adott verziójához `runtimeVersion` . Az alkalmazás mindig a legújabb verzión fut, kivéve, ha úgy dönt, hogy kifejezetten egy adott verzióra szeretné rögzíteni. Egyszerre csak néhány támogatott verzió érhető el. Ha egy érvénytelen, már nem támogatott verzióra rögzít, az alkalmazás a legújabb verziót fogja használni. A legújabb verzió mindig futtatásához állítsa a következőt: `runtimeVersion` ~ 1. 

### <a name="view-and-update-the-current-runtime-version"></a>Az aktuális futtatókörnyezet verziójának megtekintése és frissítése

Módosíthatja az alkalmazás által használt futtatókörnyezet verzióját. Az új futtatókörnyezet verziója az alkalmazás újraindítása után lép érvénybe. 

#### <a name="view-the-current-runtime-version"></a>Az aktuális futtatókörnyezet verziójának megtekintése

Megtekintheti a platform-hitelesítés middleware aktuális verzióját az Azure CLI használatával vagy az alkalmazás egyik built0 HTTP-végpontján keresztül.

##### <a name="from-the-azure-cli"></a>Az Azure CLI-ből

Az Azure CLI használatával tekintse meg az aktuális middleware-verziót az az [WebApp Auth show](https://docs.microsoft.com/cli/azure/webapp/auth?view=azure-cli-latest#az-webapp-auth-show) paranccsal.

```azurecli-interactive
az webapp auth show --name <my_app_name> \
--resource-group <my_resource_group>
```

Ebben a kódban cserélje le az `<my_app_name>` alkalmazást az alkalmazás nevére. Az `<my_resource_group>` alkalmazáshoz tartozó erőforráscsoport nevét is cserélje le.

Ekkor megjelenik a `runtimeVersion` CLI kimenetben lévő mező. A következő példához hasonló kimenetet fog hasonlítani, amelyet az egyértelműség érdekében csonkolt: 
```output
{
  "additionalLoginParams": null,
  "allowedAudiences": null,
    ...
  "runtimeVersion": "1.3.2",
    ...
}
```

##### <a name="from-the-version-endpoint"></a>A verzió végpontján

Egy alkalmazás/.auth/Version-végpontját is elérheti, ha meg szeretné tekinteni az alkalmazás által futó aktuális middleware-verziót. A következő példához hasonló kimenetet fog kinézni:
```output
{
"version": "1.3.2"
}
```

#### <a name="update-the-current-runtime-version"></a>Az aktuális futtatókörnyezet verziójának frissítése

Az Azure CLI használatával az az `runtimeVersion` [WebApp Auth Update](https://docs.microsoft.com/cli/azure/webapp/auth?view=azure-cli-latest#az-webapp-auth-update) paranccsal frissítheti az alkalmazás beállításait.

```azurecli-interactive
az webapp auth update --name <my_app_name> \
--resource-group <my_resource_group> \
--runtime-version <version>
```

A helyére írja `<my_app_name>` be az alkalmazás nevét. Az `<my_resource_group>` alkalmazáshoz tartozó erőforráscsoport nevét is cserélje le. Továbbá cserélje le az értékét az `<version>` 1. x futtatókörnyezet érvényes verziójára vagy `~1` a legújabb verzióra. A kibocsátási megjegyzéseket a különböző futtatókörnyezet-verziókban találja ([ide]) (a https://github.com/Azure/app-service-announcements) rögzítéshez használt verzió meghatározásához.

Ezt a parancsot a [Azure Cloud Shell](../cloud-shell/overview.md) futtathatja, ha az előző kódrészletben a **kipróbálás** lehetőséget választja. Az [Azure CLI helyi](https://docs.microsoft.com/cli/azure/install-azure-cli) használatával is végrehajthatja ezt a parancsot az [az login (bejelentkezés](https://docs.microsoft.com/cli/azure/reference-index#az-login) ) parancs végrehajtása után.

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Felhasználók teljes körű hitelesítése és engedélyezése](tutorial-auth-aad.md)
