---
title: Hitelesítés és engedélyezés az Azure App Service testreszabása |} A Microsoft Docs
description: Bemutatja, hogyan testre szabhatja a hitelesítés és engedélyezés az App Service-ben, és felhasználói jogcímek és különböző jogkivonatokat.
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
ms.date: 03/14/2018
ms.author: cephalin
ms.openlocfilehash: 629a76ab5610625e14780d7b5c57d3979c2224c9
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43344170"
---
# <a name="customize-authentication-and-authorization-in-azure-app-service"></a>Hitelesítés és engedélyezés az Azure App Service testreszabása

Ez a cikk bemutatja, hogyan szabhatja testre [hitelesítés és engedélyezés az App Service-ben](app-service-authentication-overview.md), és az identitások kezelésére az alkalmazásból. 

Gyorsan használatba, tekintse meg az alábbi oktatóanyagok egyikét:

* [Oktatóanyag: Hitelesítése és engedélyezése a felhasználók teljes körű az Azure App Service-ben (Windows)](app-service-web-tutorial-auth-aad.md)
* [Oktatóanyag: Hitelesítése és engedélyezése a felhasználók teljes körű az Azure App Service Linux rendszeren](containers/tutorial-auth-aad.md)
* [Az alkalmazás konfigurálása az Azure Active Directory-bejelentkezés használatára](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Az alkalmazás konfigurálása a Facebook-bejelentkezés használatára](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Az alkalmazás konfigurálása a Google-bejelentkezés használatára](app-service-mobile-how-to-configure-google-authentication.md)
* [Az alkalmazás konfigurálása a Microsoft-fiókbejelentkezés használatára](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Az alkalmazás konfigurálása a Twitter-bejelentkezés használatára](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="use-multiple-sign-in-providers"></a>Több bejelentkezési szolgáltató használata

A portál konfigurációja nem található több bejelentkezési szolgáltatók (például Facebook és Twitter) a felhasználók számára, kulcsrakész lehetőséget biztosít. Azonban nem nehéz a Funkciók hozzáadása a webalkalmazáshoz. A lépéseket a következő eljárásokat:

Az első a **hitelesítési / engedélyezési** lapon az Azure Portalon, az engedélyezni kívánt identitásszolgáltató konfigurálja.

A **elvégzendő művelet, ha a kérés nincs hitelesítve**válassza **engedélyezése névtelen kérések (nincs művelet)**.

A bejelentkezési oldal vagy a navigációs sávon, vagy bármely más helyen található, a bejelentkezési hivatkozás hozzáadása az engedélyezett szolgáltatók mindegyike (`/.auth/login/<provider>`). Példa:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoftaccount">Log in with Microsoft Account</a>
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Amikor a felhasználó a hivatkozásra kattint, a megfelelő bejelentkezési oldal megnyílik a felhasználó bejelentkezni.

Átirányítja a felhasználót követő-regisztrálási be egy egyéni URL-címet, használja a `post_login_redirect_url` lekérdezési karakterlánc paraméterének (nem tévesztendő össze az átirányítási URI az az identitás-szolgáltató konfigurálása). Nyissa meg a felhasználót, hogy a példában `/Home/Index` után jelentkezzen be, használja az alábbi HTML-kódot:

```HTML
<a href="/.auth/login/<provider>?post_login_redirect_url=/Home/Index">Log in</a>
```

## <a name="sign-out-of-a-session"></a>Jelentkezzen ki a munkamenet

Kezdeményezheti, hogy a felhasználók számára a kijelentkezési küldésével egy `GET` az alkalmazás kérelem `/.auth/logout` végpont. A `GET` kérelmet a következőket:

- Hitelesítési cookie-k az aktuális munkamenet törlése.
- Törli a jelenlegi felhasználói jogkivonatok a jogkivonat-tároló.
- Az identitásszolgáltató a kijelentkezési kiszolgálóoldali végez az Azure Active Directory és a Google.

Itt van egy weblap egyszerű Kijelentkezés hivatkozásra:

```HTML
<a href="/.auth/logout">Sign out</a>
```

Egy sikeres alapértelmezés szerint kijelentkezési átirányítja az ügyfelet az URL-cím `/.auth/logout/done`. Adja hozzá a post-sign-out átirányítási oldalon módosíthatja a `post_logout_redirect_uri` lekérdezési paraméter. Példa:

```
GET /.auth/logout?post_logout_redirect_uri=/index.html
```

Azt javasoljuk, hogy Ön [kódolása](https://wikipedia.org/wiki/Percent-encoding) értékét `post_logout_redirect_uri`.

Teljes URL-címek használata esetén az URL-címet kell lennie az ugyanabban a tartományban lévő üzemeltetett vagy egy engedélyezett külső átirányítási URL-címet az alkalmazás konfigurálva. A következő példában átirányítása `https://myexternalurl.com` , amely nem ugyanabban a tartományban lévő üzemeltetett:

```
GET /.auth/logout?post_logout_redirect_uri=https%3A%2F%2Fmyexternalurl.com
```

A következő parancsot futtatnia kell a [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp auth update --name <app_name> --resource-group <group_name> --allowed-external-redirect-urls "https://myexternalurl.com"
```

## <a name="preserve-url-fragments"></a>URL-cím töredékkel megőrizni

Miután a felhasználók bejelentkeznek az alkalmazásba, általában szeretne átirányítja őket ugyanabban a szegmensben, ugyanazon az oldalon, mint például `/wiki/Main_Page#SectionZ`. Azonban mivel [URL-cím töredék](https://wikipedia.org/wiki/Fragment_identifier) (például `#SectionZ`) soha nem továbbítódnak a kiszolgálóra, ezek nem maradnak meg alapértelmezés szerint az OAuth-bejelentkezés befejezése, és átirányítja a alkalmazását követően. Felhasználók majd egy optimálisnál tapasztalatokat szerezhet, amikor szükségük van, keresse meg a kívánt jegyzetobjektum újra. Ez a korlátozás vonatkozik minden kiszolgálóoldali hitelesítési megoldás.

App Service-hitelesítés URL-cím szilánkok között az OAuth-bejelentkezés tudja őrizni. Ehhez állítsa be az alkalmazás nevű beállítása `WEBSITE_AUTH_PRESERVE_URL_FRAGMENT` való `true`. Ezt megteheti a [az Azure portal](https://portal.azure.com), vagy egyszerűen futtassa a következő parancsot a [Azure Cloud Shell](../cloud-shell/quickstart.md):

```azurecli-interactive
az webapp config appsettings set --name <app_name> --resource-group <group_name> --settings WEBSITE_AUTH_PRESERVE_URL_FRAGMENT="true"
```

## <a name="access-user-claims"></a>Hozzáférési felhasználói jogcímek

App Service speciális fejlécek használatával adja át a felhasználói jogcímek az alkalmazáshoz. Külső kérelmek nem engedélyezett, állítsa be ezeket a fejléceket, így ezek meg adva, csak akkor, ha az App Service által beállított. Néhány példa fejléc a következők:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

A bármilyen nyelven vagy keretrendszerben írt kód az ezeket a fejléceket kérheti a szükséges információkat. ASP.NET 4.6-alkalmazások esetén a **ClaimsPrincipal** automatikusan a megfelelő értékekre van beállítva.

Az alkalmazás további részleteket a hitelesített felhasználó is beszerezhető meghívásával `/.auth/me`. A Mobile Apps server SDK-k az adatokkal való munka segédmódszereket biztosítanak. További információkért lásd: [használata az Azure Mobile Apps Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity), és [használható a .NET háttérkiszolgáló-SDK az Azure Mobile Apps a](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Alkalmazáskódban lévő jogkivonatok lekéréséről

A kiszolgálói kódból a szolgáltatóhoz tartozó jogkivonatok vannak elhelyezte a kérelem fejlécét, így könnyen elérheti őket. Az alábbi táblázat a lehetséges token fejléc neve:

| | |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook-jogkivonat | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft-fiók | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Az Ügyfélkód (például egy mobilalkalmazás vagy a böngészőben JavaScript), a küldjön egy HTTP `GET` kérelmet `/.auth/me`. A visszaadott JSON-FÁJLBAN szerepel a szolgáltatóhoz tartozó jogkivonatok.

> [!NOTE]
> Hozzáférési jogkivonatok olyan eléréséhez a felhőszolgáltatók erőforrásainak, így ezek meg adva, csak akkor, ha a egy ügyfélkulcsot a szolgáltató konfigurálhat. Frissítési biztonsági jogkivonat beszerzése megtekintéséhez lásd: [hozzáférési jogkivonatok Frissítésérőt](#refresh-access-tokens).

## <a name="refresh-access-tokens"></a>Hozzáférési jogkivonatok frissítése

Amikor a szolgáltató hozzáférési jogkivonat lejár, hitelesítse magát újra a felhasználónak kell. Elkerülheti a jogkivonat lejárati azáltal, hogy egy `GET` hívása a `/.auth/refresh` végpont az alkalmazás. Meghívni, az App Service automatikusan frissül a jogkivonat-tároló a hitelesített felhasználó számára a hozzáférési jogkivonatokat. A kód által jogkivonatokat későbbi kérelmeket tokenekhez frissülnek. Azonban a jogkivonat-frissítések esetében működik, a jogkivonat-tároló tartalmaznia kell [frissítési jogkivonatok](https://auth0.com/learn/refresh-tokens/) a szolgáltató. A frissítési tokenekhez módszer egyes szolgáltatók szerint van dokumentálva, de az alábbi lista röviden összefoglalva:

- **Google**: hozzáfűzése egy `access_type=offline` lekérdezési karakterlánc paraméter, a `/.auth/login/google` API-hívás. A Mobile Apps SDK használata esetén is hozzáadhat a paraméter az egyik a `LogicAsync` túlterheléssel (lásd: [Google frissítési jogkivonatok](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: frissítési biztonsági jogkivonat nem biztosít. Hosszú élettartamú jogkivonatok 60 nap múlva megszűnik (lásd: [Facebook lejárati és hozzáférési jogkivonatok bővítmény](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter-**: hozzáférési jogkivonatok nem jár le (lásd: [OAuth – gyakori kérdések Twitter](https://developer.twitter.com/en/docs/basics/authentication/guides/oauth-faq)).
- **A Microsoft Account**: amikor [konfigurálása a Microsoft-fiók hitelesítési beállításai](app-service-mobile-how-to-configure-microsoft-authentication.md), jelölje be a `wl.offline_access` hatókör.
- **Az Azure Active Directory**: A [ https://resources.azure.com ](https://resources.azure.com), tegye a következőket:
    1. A lap tetején válassza **olvasási/írási**.
    1. A bal oldali böngészőben navigáljon **előfizetések** > **_\<előfizetés\_neve_**   >  **resourceGroups** > _**\<erőforrás\_csoport\_neve >**_   >  **szolgáltatók** > **Microsoft.Web** > **helyek** > _**\<alkalmazás \_neve >**_ > **config** > **authsettings**. 
    1. Kattintson a **Szerkesztés** gombra.
    1. A következő tulajdonság módosításával. Cserélje le  _\<alkalmazás\_azonosítója >_ az Azure Active Directory-alkalmazás azonosítójával az elérni kívánt szolgáltatás.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    1. Kattintson a **Put**. 

Miután konfigurálta, a szolgáltató is [keresse meg a frissítési jogkivonatot, és a hozzáférési jogkivonat lejárati idejének](#retrieve-tokens-in-app-code) a jogkivonat-tároló a. 

A hozzáférési jogkivonat frissítéséhez bármikor, csak hívja `/.auth/refresh` bármilyen nyelven. A következő kódrészlet jQuery a hozzáférési tokenek egy JavaScript-ügyfél frissítéséhez.

```JavaScript
function refreshTokens() {
  var refreshUrl = "/.auth/refresh";
  $.ajax(refreshUrl) .done(function() {
    console.log("Token refresh completed successfully.");
  }) .fail(function() {
    console.log("Token refresh failed. See application logs for details.");
  });
}
```

Ha a felhasználó visszavonja az alkalmazáshoz, a hívás jogosultságaitól `/.auth/me` sikertelen lehet a `403 Forbidden` választ. Diagnosztizálhatja a hibákat, ellenőrizze a részleteket az alkalmazásnaplókat.

## <a name="extend-session-expiration-grace-period"></a>Kiterjesztheti a munkamenet lejárata türelmi időszak

Egy hitelesített munkamenet lejárata után nincs 72 órás türelmi időszak alapértelmezés szerint. A türelmi időszakon belül akkor jogosult-e a munkamenetcookie-t vagy az App Service-szel tokenu relace frissítése anélkül, hogy a felhasználó ismételt hitelesítése. Csak hívása `/.auth/refresh` amikor a munkamenetcookie-t vagy a munkamenet-jogkivonat érvénytelenné válik, és nem kell saját kezűleg jogkivonat lejárati nyomon követéséhez. Le nem telik a 72 órás türelmi időszak után a felhasználó jelentkezzen be újra egy érvényes munkamenetcookie-t vagy a munkamenet-jogkivonat beszerzése.

72 óra nem elegendő időt, ha a lejárati időszak bővítheti. Kiterjeszti a hosszabb időn keresztül a lejárati lehet (például amikor egy hitelesítési tokent kiszivárgott vagy ellopják) jelentős biztonsági szempontokat. Ezért érdemes meghagyhatja az alapértelmezett, 72 óra vagy a legkisebb érték a kiterjesztés idő beállítása.

Az alapértelmezett lejárati időszak kiterjesztéséhez futtassa a következő parancsot a [Cloud Shell](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> A türelmi időszak csak az App Service-ben hitelesített munkamenet, nem az identitás-szolgáltatóktól származó jogkivonatokat vonatkozik. A lejárt szolgáltató jogkivonatokat nincs türelmi időszak van. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>Bejelentkezési fiók tartományának korlátozása

A Microsoft Account és az Azure Active Directory lehetővé teszi több tartományból jelentkezzen be. Például lehetővé teszi a Microsoft Account _Outlook.com-os_, _live.com_, és _hotmail.com_ fiókokat. Az Azure Active Directory lehetővé teszi, hogy minden olyan egyéni tartományok száma a bejelentkezési fiókok. Lehet, hogy ez a viselkedés a belső alkalmazás, amely nem szeretné, hogy bárki nemkívánatos egy _Outlook.com-os_ hozzáférési fiókot. Szeretné korlátozni a bejelentkezési fiók tartománynevét, kövesse az alábbi lépéseket.

A [ https://resources.azure.com ](https://resources.azure.com), navigáljon a **előfizetések** > **_\<előfizetés\_neve_**   >  **resourceGroups** > _**\<erőforrás\_csoport\_neve >**_   >  **szolgáltatók** > **Microsoft.Web** > **helyek**  >    _**\<alkalmazás\_neve >**_ > **config** > **authsettings**. 

Kattintson a **szerkesztése**, a következő tulajdonság módosításával, és kattintson **Put**. Ne felejtse el  _\<tartomány\_neve >_ kívánt tartományával.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Hitelesítése és engedélyezése a felhasználók teljes körű (Windows)](app-service-web-tutorial-auth-aad.md)
> [oktatóanyag: hitelesítése és engedélyezése a felhasználók teljes körű (Linux)](containers/tutorial-auth-aad.md)