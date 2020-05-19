---
title: Útvonalak az Azure statikus Web Apps
description: Tudnivalók a háttérbeli útválasztási szabályokról és az útvonalak szerepkörökkel való biztonságossá tételéről.
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: cshoe
ms.openlocfilehash: 4a9639343827ebc5bb17a6d62d9b65d0b561e932
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597743"
---
# <a name="routes-in-azure-static-web-apps-preview"></a>Útvonalak az Azure statikus Web Apps előzetes verziójában

Az Azure statikus Web Apps útválasztása meghatározza a háttér-útválasztási szabályokat és a statikus tartalom és API-k engedélyezési viselkedését. A szabályok a _Routes. JSON_ fájlban szereplő szabályok tömbje vannak meghatározva.

- Az _Routes. JSON_ fájlnak léteznie kell az alkalmazás Build-összetevő mappájának gyökerében.
- A szabályok a tömbben megjelenő sorrendben lesznek végrehajtva `routes` .
- A szabály kiértékelése az első egyeztetéskor leáll. Az útválasztási szabályok nincsenek összefűzve.
- A szerepkörök az _Routes. JSON_ fájlban vannak meghatározva, és a felhasználók a [meghívón](authentication-authorization.md)keresztül vannak társítva a szerepkörökhöz.
- Teljes hozzáférése van a szerepkörök neveihez.

Az Útválasztás témaköre jelentősen átfedésben van a hitelesítési és engedélyezési fogalmakkal. Ügyeljen rá, hogy olvassa el a [hitelesítési és engedélyezési](authentication-authorization.md) útmutatót a jelen cikk mellett.

## <a name="location"></a>Hely

Az _Routes. JSON_ fájlnak léteznie kell az alkalmazás Build-összetevő mappájának gyökerében. Ha a webalkalmazás olyan Build lépést tartalmaz, amely egy adott mappából a Build-összetevő mappájába másolt létrehozott fájlokat, akkor a _Routes. JSON_ fájlnak léteznie kell az adott mappában.

A következő táblázat felsorolja a megfelelő helyet, hogy a _Routes. JSON_ fájlt több előtér-JavaScript-keretrendszerhez és-könyvtárhoz helyezze.

|Keretrendszer/könyvtár | Hely  |
|---------|----------|
| Angular | _eszközök_   |
| Reagálni   | _nyilvános_  |
| Karcsú  | _nyilvános_   |
| Vue     | _nyilvános_ |

## <a name="defining-routes"></a>Útvonalak meghatározása

A Routes _. JSON_ fájlban az útvonalakat a tulajdonsághoz tartozó útvonal-szabályok tömbje határozza meg. `routes` Minden szabály egy útvonal mintából áll, valamint egy vagy több választható szabály-tulajdonsággal. A használati példákat lásd a [példaként szolgáló útválasztási fájlban](#example-route-file) .

| Szabály tulajdonsága  | Kötelező | Alapértelmezett érték | Megjegyzés                                                      |
| -------------- | -------- | ------------- | ------------------------------------------------------------ |
| `route`        | Igen      | n.a.          | A hívó által kért útvonal-minta.<ul><li>A [helyettesítő karakterek](#wildcards) az útvonal-elérési utak végén támogatottak. Például az útvonal _rendszergazdája/ \* _ a _rendszergazdai_ elérési úton található bármely útvonalra illeszkedik.<li>Az útvonal alapértelmezett fájlja az _index. html_.</ul>|
| `serve`        | Nem       | n.a.          | Meghatározza a kérelemből visszaadott fájlt vagy elérési utat. A fájl elérési útja és neve eltérő lehet a kért elérési útról. Ha `serve` meg van adva egy érték, a rendszer a kért elérési utat használja. |
| `allowedRoles` | Nem       | névtelen     | A szerepkörök neveinek tömbje. <ul><li>Érvényes karakterek a következők:,, `a-z` `A-Z` `0-9` és `_` .<li>A beépített szerepkör minden nem `anonymous` hitelesített felhasználóra érvényes.<li>A beépített szerepkör `authenticated` minden bejelentkezett felhasználóra érvényes.<li>A felhasználóknak legalább egy szerepkörhöz kell tartoznia.<li>A szerepköröket _vagy_ azok alapján kell egyeztetni. Ha egy felhasználó a felsorolt szerepkörök valamelyikében szerepel, akkor a rendszer hozzáférést biztosít.<li>Az egyes felhasználók a szerepkörökhöz a [meghívások](authentication-authorization.md)útján vannak társítva.</ul> |
| `statusCode`   | Nem       | 200           | A kérelem [http-állapotkód](https://wikipedia.org/wiki/List_of_HTTP_status_codes) -válasza. |

## <a name="securing-routes-with-roles"></a>Útvonalak biztonságossá tétele szerepkörökkel

Az útvonalak egy vagy több szerepkörnek a szabály tömbbe való felvételével biztonságosak `allowedRoles` . A használati példákat lásd a [példaként szolgáló útválasztási fájlban](#example-route-file) .

Alapértelmezés szerint minden felhasználó a beépített `anonymous` szerepkörhöz tartozik, és az összes bejelentkezett felhasználó tagja a `authenticated` szerepkörnek. Ha például csak a hitelesített felhasználókra szeretné korlátozni az útvonalat, adja hozzá a beépített `authenticated` szerepkört a `allowedRoles` tömbhöz.

```json
{
  "route": "/profile",
  "allowedRoles": ["authenticated"]
}
```

Szükség szerint új szerepköröket hozhat létre a `allowedRoles` tömbben. Az útvonalak csak rendszergazdákra való korlátozásához definiálhat egy `administrator` szerepkört a `allowedRoles` tömbben.

```json
{
  "route": "/admin",
  "allowedRoles": ["administrator"]
}
```

- Teljes hozzáférése van a szerepkörök neveihez; nincsenek főlista, amelyhez a szerepköröknek be kell tartaniuk.
- Az egyes felhasználók a szerepkörökhöz vannak társítva a [meghívásokon](authentication-authorization.md)keresztül.

## <a name="wildcards"></a>Helyettesítő karakterek

A helyettesítő karakteres szabályok az adott útvonal mintájában lévő összes kérelemnek megfelelnek. Ha meghatároz egy `serve` értéket a szabályban, a megnevezett fájl vagy elérési út válaszként szolgál.

A naptári alkalmazások útvonalának megvalósításához például leképezheti a _naptári_ útvonal alá tartozó összes URL-címet egyetlen fájl kiszolgálásához.

```json
{
  "route": "/calendar/*",
  "serve": "/calendar.html"
}
```

A _Calendar. html_ fájl ezután használhatja az ügyféloldali útválasztást az URL-változatok (például `/calendar/january/1` , `/calendar/2020` , és) különböző nézetének kiszolgálására `/calendar/overview` .

Az útvonalakat helyettesítő karakterekkel is biztonságossá teheti. A következő _példában a rendszergazdai elérési_ úton kért összes fájlhoz egy hitelesített felhasználónak kell tartoznia, aki tagja a _rendszergazda_ szerepkörnek.

```json
{
  "route": "/admin/*",
  "allowedRoles": ["administrator"]
}
```

> [!NOTE]
> A kiszolgált fájl által hivatkozott fájlokra vonatkozó kérelmeket a rendszer csak a hitelesítési ellenőrzésekhez értékeli. A helyettesítő karakteres elérési úton lévő CSS-fájlokra irányuló kérések például a CSS-fájl, nem pedig a fájl definíciója `serve` . A CSS-fájl mindaddig rendelkezésre áll, amíg a felhasználó megfelel a szükséges hitelesítési követelményeknek.

## <a name="fallback-routes"></a>Tartalék útvonalak

Az előtér-JavaScript-keretrendszerek vagy-kódtárak gyakran használják az ügyféloldali útválasztást a webalkalmazások navigációs felületén. Ezek az ügyféloldali útválasztási szabályok frissítik a böngésző ablakának helyét anélkül, hogy kéréseket kellene visszaküldeni a kiszolgálónak. Ha frissíti az oldalt, vagy közvetlenül az ügyféloldali útválasztási szabályok által létrehozott helyekre navigál, a megfelelő HTML-oldal kiszolgálásához kiszolgálóoldali tartalék útvonal szükséges.

A következő példában egy közös tartalék útvonal látható:

```json
{
  "routes": [
    {
      "route": "/*",
      "serve": "/index.html",
      "statusCode": 200
    }
  ]
}
```

Az útválasztási szabályokban a tartalék útvonalnak szerepelnie kell az utolsóként, mivel a korábban meghatározott szabályok által nem észlelt összes kérést kigyűjti.

## <a name="redirects"></a>Átirányítja

Az [301](https://en.wikipedia.org/wiki/HTTP_301) -es és a [302](https://en.wikipedia.org/wiki/HTTP_302) -es HTTP-állapotkódok használatával átirányíthatja a kérelmeket az egyik útvonalról a másikra.

A következő szabály például létrehoz egy 301-es átirányítást a _Old-Page. html_ fájlból a _New-Page. html_fájlba.

```json
{
  "route": "/old-page.html",
  "serve": "/new-page.html",
  "statusCode": 301
}
```

Az átirányítások olyan elérési utakkal is működnek, amelyek nem határoznak meg külön fájlokat.

```json
{
  "route": "/about-us",
  "serve": "/about",
  "statusCode": 301
}
```

## <a name="custom-error-pages"></a>Egyéni hibalapok

Előfordulhat, hogy a felhasználók számos különböző szituációba ütköznek, ami hibát okozhat. A tömb használatával egyéni felhasználói élményt adhat meg a `platformErrorOverrides` hibákra reagálva. Az Routes _. JSON_ fájlban tekintse át a [példaként szolgáló útválasztási fájlt](#example-route-file) a tömb elhelyezéséhez.

A következő táblázat az elérhető platform-hibák felülbírálásait sorolja fel:

| Hiba típusa  | HTTP-állapotkód | Leírás |
|---------|---------|---------|
| `NotFound` | 404  | Nem található lap a kiszolgálón. |
| `Unauthenticated` | 401 | A felhasználó nincs bejelentkezve egy [hitelesítési szolgáltatóval](authentication-authorization.md). |
| `Unauthorized_InsufficientUserInformation` | 401 | A felhasználó fiókja a hitelesítési szolgáltatón nincs konfigurálva a szükséges adatvédelemre. Ez a hiba olyan helyzetekben fordulhat elő, mint amikor az alkalmazás megkéri a hitelesítő szolgáltatót a felhasználó e-mail-címére, de a felhasználó úgy döntött, hogy korlátozza a hozzáférést az e-mail-címre. |
| `Unauthorized_InvalidInvitationLink` | 401 | Egy meghívás lejárt, vagy a felhasználó egy másik címzett számára létrehozott meghívó hivatkozást követett.  |
| `Unauthorized_MissingRoles` | 401 | A felhasználó nem tagja egy szükséges szerepkörnek. |
| `Unauthorized_TooManyUsers` | 401 | A hely elérte a felhasználók maximális számát, és a kiszolgáló korlátozza a további kiegészítéseket. Ez a hiba az ügyfél számára érhető el, mert nincs korlátozva a létrehozható [meghívások](authentication-authorization.md) száma, és egyes felhasználók soha nem fogadják el a meghívót.|
| `Unauthorized_Unknown` | 401 | Ismeretlen hiba történt a felhasználó hitelesítése közben. Ennek a hibának az egyik oka az lehet, hogy a felhasználó nem ismerhető fel, mert nem engedélyezte az alkalmazáshoz való hozzájárulásukat.|

## <a name="example-route-file"></a>Példa az útválasztási fájlra

Az alábbi példa bemutatja, hogyan hozhat létre útválasztási szabályokat statikus tartalomhoz és API-khoz egy _Routes. JSON_ fájlban. Egyes útvonalak a [ _/.auth_ rendszermappáját](authentication-authorization.md) használják, amely hozzáfér a hitelesítéssel kapcsolatos végpontokhoz.

```json
{
  "routes": [
    {
      "route": "/profile",
      "allowedRoles": ["authenticated"]
    },
    {
      "route": "/admin/*",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/api/admin",
      "allowedRoles": ["administrator"]
    },
    {
      "route": "/customers/contoso",
      "allowedRoles": ["administrator", "customers_contoso"]
    },
    {
      "route": "/login",
      "serve": "/.auth/login/github"
    },
    {
      "route": "/.auth/login/twitter",
      "statusCode": "404"
    },
    {
      "route": "/logout",
      "serve": "/.auth/logout"
    },
    {
      "route": "/calendar/*",
      "serve": "/calendar.html"
    },
    {
      "route": "/specials",
      "serve": "/deals",
      "statusCode": 301
    }
  ],
  "platformErrorOverrides": [
    {
      "errorType": "NotFound",
      "serve": "/custom-404.html"
    },
    {
      "errorType": "Unauthenticated",
      "serve": "/login"
    }
  ]
}
```

Az alábbi példák azt írják le, mi történik, ha egy kérelem megfelel egy szabálynak.

|Kérelmek...  | Eredmény... |
|---------|---------|---------|
| _/Profile_ | A hitelesített felhasználók kiszolgálják a _/Profile/index.html_ fájlt. Nem hitelesített felhasználók átirányítva a _belépési_értékre. |
| _/admin/reports_ | A _rendszergazdák_ szerepkörben lévő hitelesített felhasználók a _/Admin/Reports/index.html_ fájlt kézbesítik. A _rendszergazdák_ szerepkörben nem szereplő hitelesített felhasználók az<sup>1</sup>. 401 hibát szolgálják fel. Nem hitelesített felhasználók átirányítva a _belépési_értékre. |
| _/api/admin_ | A _rendszergazdák_ szerepkörbe tartozó hitelesített felhasználóktól érkező kéréseket a rendszer ELKÜLDI az API-nak. A _rendszergazdák_ szerepkörbe nem tartozó hitelesített felhasználók és a nem hitelesített felhasználók 401-es hibát szolgáltatnak. |
| _/customers/contoso_ | A _rendszergazdákhoz_ vagy az _ügyfelekhez tartozó \_ contoso_ -szerepkörökhöz tartozó hitelesített felhasználók kiszolgálják az<sup>1</sup>. _/Customers/contoso/index.html_ fájlt. A _rendszergazdák_ vagy az _ügyfelek \_ contoso_ szerepköreiben nem szereplő hitelesített felhasználók 401-es hibát szolgáltatnak. Nem hitelesített felhasználók átirányítva a _belépési_értékre. |
| _/Belépés View_     | A nem hitelesített felhasználók a GitHub használatával hitelesíthetők. |
| _/.auth/login/twitter_     | A Twitter-engedélyezés le van tiltva. A kiszolgáló 404-as hibával válaszol. |
| _/logout_     | A felhasználók ki vannak jelentkezve bármely hitelesítési szolgáltatóból. |
| _/calendar/2020/01_ | A böngésző kiszolgálja a _/Calendar.html_ fájlt. |
| _/specials_ | A böngésző átirányítja a _/Deals_. |
| _/unknown-folder_     | A _/Custom-404.html_ -fájl kézbesítve. |

<sup>1</sup> egyéni hibaüzenetet adhat meg egy szabály definiálásával `Unauthorized_MissingRoles` a `platformErrorOverrides` tömbben.

## <a name="restrictions"></a>Korlátozások

- Az _Routes. JSON_ fájl nem lehet nagyobb, mint 100 kb
- Az _Routes. JSON_ fájl legfeljebb 50 különböző szerepkört támogat

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Hitelesítés és engedélyezés beállítása](authentication-authorization.md)
