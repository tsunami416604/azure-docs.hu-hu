---
title: Testre szabhatja a hitelesítési és engedélyezési az Azure App Service szolgáltatásban |} Microsoft Docs
description: Testre szabhatja a hitelesítési és engedélyezési az App Service-ben, és a felhasználói jogcímek és a különböző jogkivonatok ismerteti.
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
ms.openlocfilehash: 10decbd5291e2054e373bfef266b64eae36ea1cf
ms.sourcegitcommit: 5b2ac9e6d8539c11ab0891b686b8afa12441a8f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="customize-authentication-and-authorization-in-azure-app-service"></a>Hitelesítési és engedélyezési az Azure App Service testreszabása

Ez a cikk bemutatja, hogyan szabhatja testre [hitelesítési és engedélyezési az App Service](app-service-authentication-overview.md), és az identitás kezelése az alkalmazásból. 

Gyorsan használatba, tekintse meg az alábbi oktatóanyagok egyikét:

* [Oktatóanyag: Helyszerepkörre, és felhasználók-végpont az Azure App Service-ben](app-service-web-tutorial-auth-aad.md)
* [Az alkalmazás konfigurálása az Azure Active Directory-bejelentkezés használatára](app-service-mobile-how-to-configure-active-directory-authentication.md)
* [Az alkalmazás konfigurálása a Facebook-bejelentkezés használatára](app-service-mobile-how-to-configure-facebook-authentication.md)
* [Az alkalmazás konfigurálása a Google-bejelentkezés használatára](app-service-mobile-how-to-configure-google-authentication.md)
* [Az alkalmazás konfigurálása a Microsoft-fiókbejelentkezés használatára](app-service-mobile-how-to-configure-microsoft-authentication.md)
* [Az alkalmazás konfigurálása a Twitter-bejelentkezés használatára](app-service-mobile-how-to-configure-twitter-authentication.md)

## <a name="configure-multiple-sign-in-options"></a>Több bejelentkezési beállítások konfigurálása

A portál beállításai nem kínál kulcsrakész úgy több bejelentkezési beállítás elérhető, hogy a felhasználók (például a Facebookhoz és a Twitterhez). Azonban nem nehéz a Funkciók hozzáadása a webes alkalmazást. A lépéseket a következőképpen vázolt:

Az első a **hitelesítési / engedélyezési** oldalra az Azure portálon, minden engedélyezni szeretné az identitásszolgáltató be.

A **hitelesítetlen kérés esetén elvégzendő művelet**, jelölje be **engedélyezése névtelen kérésekkel (intézkedés)**.

A bejelentkezési oldal, vagy a navigációs sávon, vagy bármely más helyre, webalkalmazás, a bejelentkezési hivatkozás hozzáadása az engedélyezett szolgáltatók mindegyikének (`/.auth/login/<provider>`). Példa:

```HTML
<a href="/.auth/login/aad">Log in with Azure AD</a>
<a href="/.auth/login/microsoft">Log in with Microsoft Account</a> 
<a href="/.auth/login/facebook">Log in with Facebook</a>
<a href="/.auth/login/google">Log in with Google</a>
<a href="/.auth/login/twitter">Log in with Twitter</a>
```

Amikor a felhasználó a hivatkozásra kattint, megnyílik a megfelelő bejelentkezési oldal jelentkezzen be a felhasználó.

## <a name="access-user-claims"></a>Hozzáférés felhasználói jogcímek

App Service speciális fejlécek használatával továbbítja a felhasználói jogcímeket, hogy az alkalmazást. Külső kérelmek nem engedélyezett ezek a fejlécek beállítása, így ezek meg adva csak akkor, ha az App Service által beállított. Néhány példa fejlécek a következők:

* X-MS-CLIENT-PRINCIPAL-NAME
* X-MS-CLIENT-PRINCIPAL-ID

Bármilyen nyelven vagy keretrendszerben írt kódot az ezek a fejlécek kérheti le a szükséges információkat. Az ASP.NET 4.6-alkalmazások esetén a **ClaimsPrincipal** automatikusan állítsa be a megfelelő értékekkel.

Az alkalmazás további részleteket a hitelesített felhasználó is beszerezhető meghívásával `/.auth/me`. A Mobile Apps server SDK-k az adatok segédmódszereket biztosítanak. További információkért lásd: [használata az Azure Mobile Apps Node.js SDK](../app-service-mobile/app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-tables-getidentity), és [használható a .NET-háttérrendszer server SDK az Azure Mobile Apps a](../app-service-mobile/app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#user-info).

## <a name="retrieve-tokens-in-app-code"></a>Az alkalmazáskódot jogkivonatok beolvasása

A kiszolgálóoldali kódját a szolgáltatói jogkivonatok vannak be a nézetmodellbe, a kérelem fejlécében, egyszerűen elérhet. Az alábbi táblázat a lehetséges token nevek:

| | |
|-|-|
| Azure Active Directory | `X-MS-TOKEN-AAD-ID-TOKEN` <br/> `X-MS-TOKEN-AAD-ACCESS-TOKEN` <br/> `X-MS-TOKEN-AAD-EXPIRES-ON`  <br/> `X-MS-TOKEN-AAD-REFRESH-TOKEN` |
| Facebook-jogkivonat | `X-MS-TOKEN-FACEBOOK-ACCESS-TOKEN` <br/> `X-MS-TOKEN-FACEBOOK-EXPIRES-ON` |
| Google | `X-MS-TOKEN-GOOGLE-ID-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-ACCESS-TOKEN` <br/> `X-MS-TOKEN-GOOGLE-EXPIRES-ON` <br/> `X-MS-TOKEN-GOOGLE-REFRESH-TOKEN` |
| Microsoft-fiók | `X-MS-TOKEN-MICROSOFTACCOUNT-ACCESS-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-EXPIRES-ON` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-AUTHENTICATION-TOKEN` <br/> `X-MS-TOKEN-MICROSOFTACCOUNT-REFRESH-TOKEN` |
| Twitter | `X-MS-TOKEN-TWITTER-ACCESS-TOKEN` <br/> `X-MS-TOKEN-TWITTER-ACCESS-TOKEN-SECRET` |
|||

Az Ügyfélkód (például a mobilalkalmazás vagy a böngésző JavaScript) küld HTTP `GET` elküldeni a kérelmet `/.auth/me`. A visszaadott JSON a szolgáltatói jogkivonatok rendelkezik.

> [!NOTE]
> Hozzáférési jogkivonatok eléréséhez szolgáltató erőforrásai, így ezek meg adva, csak akkor, ha a szolgáltató beállítása úgy, az ügyfél titkos kulcs. Hogyan kérhet a frissítési jogkivonatokat, olvassa el [frissítési hozzáférési jogkivonatok](#refresh-access-tokens).

## <a name="refresh-access-tokens"></a>Hozzáférési jogkivonatok frissítése

Amikor a szolgáltató hozzáférési jogkivonat lejár, a felhasználó hitelesítését kell. Elkerülheti a jogkivonat lejáratáról azáltal, hogy egy `GET` hívása a `/.auth/refresh` végpont az alkalmazás. Meghívásakor, az App Service automatikusan frissíti a hozzáférési jogkivonatok lévő a jogkivonat-tároló a hitelesített felhasználók. A frissített jogkivonatok lekérésére, kérelmeknél jogkivonatokat által az alkalmazás kódját. Azonban a token frissítéshez működjön, a jogkivonat-tároló tartalmaznia kell [frissítési jogkivonatok](https://auth0.com/learn/refresh-tokens/) a szolgáltatóhoz. A frissítési jogkivonatokat módja minden szolgáltató szerepelnek, de az alábbi listában röviden összefoglaljuk:

- **Google**: hozzáfűzése egy `access_type=offline` lekérdezési karakterlánc paramétert a `/.auth/login/google` API-hívás. Ha a Mobile Apps SDK használatával adhat hozzá a paraméter egyikét a `LogicAsync` túlterhelések (lásd: [Google frissítési jogkivonatok](https://developers.google.com/identity/protocols/OpenIDConnect#refresh-tokens)).
- **Facebook**: nem biztosít a frissítési jogkivonatokat. Hosszú élettartamú jogkivonatok 60 nap múlva lejár (lásd: [Facebook lejárati és a hozzáférési jogkivonatok bővítmény](https://developers.facebook.com/docs/facebook-login/access-tokens/expiration-and-extension)).
- **Twitter**: hozzáférési jogkivonatok nem jár le (lásd: [Twitter OAuth – gyakori kérdések](https://developer.twitter.com/docs/basics/authentication/guides/oauth-faq)).
- **Microsoft Account**: Ha [konfigurálása a Microsoft-fiók hitelesítési beállításai](app-service-mobile-how-to-configure-microsoft-authentication.md), jelölje be a `wl.offline_access` hatókör.
- **Az Azure Active Directory**: A [ https://resources.azure.com ](https://resources.azure.com), hajtsa végre a következő lépéseket:
    1. A lap tetején jelölje ki a **olvasási/írási**.
    1. A bal oldali böngészőben navigáljon **előfizetések** > **_\<előfizetés\_neve_**   >  **resourceGroups** > _**\<erőforrás\_csoport\_neve >**_   >  **szolgáltatók** > **Microsoft.Web** > **helyek** > _**\<alkalmazás \_neve >**_ > **config** > **authsettings**. 
    1. Kattintson a **Szerkesztés** gombra.
    1. A következő tulajdonság módosítható. Cserélje le  _\<app\_azonosítója >_ az elérni kívánt szolgáltatás az Azure Active Directory alkalmazás azonosítójával.

        ```json
        "additionalLoginParams": ["response_type=code id_token", "resource=<app_id>"]
        ```

    1. Kattintson a **Put**. 

Miután beállította a szolgáltató, láthatja, ha a frissítési jogkivonatokat szerepelnek a jogkivonat-tároló meghívásával `/.auth/me`. 

A hozzáférési token frissítése bármikor, csak hívja `/.auth/refresh` bármilyen nyelven. Az alábbi kódrészletben jQuery használatával frissítse a jogkivonatot a JavaScript-ügyfélről.

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

Ha egy felhasználó visszavonja az alkalmazáshoz, a hívás jogosultságaitól `/.auth/me` sikertelen lehet és egy `403 Forbidden` választ. Hibák diagnosztizálása, ellenőrizze a részleteket.

## <a name="extend-session-expiration-grace-period"></a>Kiterjeszti a munkamenet lejárata türelmi időszak

Egy hitelesített munkamenet lejárata után nincs 72 óra türelmi alapértelmezés szerint. A türelmi időszak alatt belül, hogy jogosult a munkamenetcookie-t vagy az App Service a munkamenet jogkivonatának frissítéséhez anélkül, hogy a felhasználó ismételt hitelesítése. Csak hívása `/.auth/refresh` amikor a munkameneti cookie-ból vagy a munkamenet jogkivonatának érvénytelenné válik, és nem kell nyomon követnie jogkivonat lejáratáról, saját magának. Ha a 72 óra türelmi időszak telik, a felhasználó kell jelentkezzen be újra egy érvényes cookie-t vagy a munkamenet jogkivonatának beolvasása.

Ha 72 óra nem elegendő időt, kiterjesztheti a lejárati időszak. A lejárati hosszú ideig kiterjesztése sikerült jelentős biztonsági következményekkel (például amikor egy hitelesítési jogkivonatot szivárgását vagy lopás esetén). Így kell hagyja az alapértelmezett 72 óra, vagy állítsa a bővítményt a legkisebb értéket.

Az alapértelmezett lejárati időszak kiterjesztéséhez futtassa a következő parancsot a [felhő rendszerhéj](../cloud-shell/overview.md).

```azurecli-interactive
az webapp auth update --resource-group <group_name> --name <app_name> --token-refresh-extension-hours <hours>
```

> [!NOTE]
> A türelmi időszak csak az App Service hitelesített munkamenet, nem az identitás-szolgáltatóktól származó jogkivonatokat vonatkozik. Nincs a lejárt szolgáltató jogkivonatok nem türelmi időszak. 
>

## <a name="limit-the-domain-of-sign-in-accounts"></a>A tartományi bejelentkezési fiókok korlátozása

Microsoft Account, mind az Azure Active Directory lehetővé teszi több tartományt a bejelentkezéshez. Például lehetővé teszi a Microsoft Account _Outlook.com-os_, _live.com_, és _hotmail.com_ fiókok. Az Azure Active Directory a bejelentkezési fiókok lehetővé teszi tetszőleges számú egyéni tartományokat. Lehet, hogy ez a viselkedés nemkívánatos belső alkalmazás, amely nem szeretné, hogy bárki, aki egy _Outlook.com-os_ hozzáférési fiókot. A tartománynév, a bejelentkezési fiókok korlátozása érdekében kövesse az alábbi lépéseket.

A [ https://resources.azure.com ](https://resources.azure.com), navigáljon a **előfizetések** > **_\<előfizetés\_neve_**   >  **resourceGroups** > _**\<erőforrás\_csoport\_neve >**_   >  **szolgáltatók** > **Microsoft.Web** > **helyek**  >    _**\<app\_neve >**_ > **config** > **authsettings**. 

Kattintson a **szerkesztése**, módosítsa a következő tulajdonság, majd **Put**. Ügyeljen arra, hogy a csere  _\<tartomány\_neve >_ kívánt tartomány.

```json
"additionalLoginParams": ["domain_hint=<domain_name>"]
```
## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Oktatóanyag: Helyszerepkörre, és felhasználók-végpontok](app-service-web-tutorial-auth-aad.md)
