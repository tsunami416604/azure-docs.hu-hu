---
title: Az Azure Active Directory v2.0-végpontra vonatkozó alkalmazástípusok |} Microsoft Docs
description: Milyen alkalmazásokat és az Azure Active Directory v2.0-végponttól által támogatott forgatókönyveket.
services: active-directory
documentationcenter: ''
author: hpsin
manager: mtillman
editor: ''
ms.assetid: 494a06b8-0f9b-44e1-a7a2-d728cf2077ae
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/17/2018
ms.author: hpsin
ms.custom: aaddev
ms.openlocfilehash: 291cd1adaf36d7f02aae84f97b7f7b3e6bca2c9c
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="app-types-for-the-azure-active-directory-v20-endpoint"></a>Alkalmazástípusok az Azure Active Directory v2.0 végpont
Az Azure Active Directory (Azure AD) v2.0-végponttól hitelesítést is támogatja a modern alkalmazás-architektúrák esetén az összes szabványos protokollokat számos [OAuth 2.0-s vagy az OpenID Connect](active-directory-v2-protocols.md). A cikkből megtudhatja, milyen típusú alkalmazásokat úgy, hogy az Azure AD v2.0, függetlenül a választott nyelv vagy platform használatával. A cikkben szereplő információkat, amelyekkel jobban megértheti az összetettebb feladatok előtt készült [a kód munka megkezdéséhez](active-directory-appmodel-v2-overview.md#getting-started).

> [!NOTE]
> A v2.0-végpontra nem támogatja, minden Azure Active Directory forgatókönyvek és funkciók. Annak megállapításához, hogy a v2.0-végponttal kell használnia, olvassa el [v2.0 korlátozások](active-directory-v2-limitations.md).
> 
> 

## <a name="the-basics"></a>Az alapok
Regisztrálnia kell az egyes alkalmazások által használt a v2.0-végpontra a [Microsoft alkalmazásregisztrációs portálra](https://apps.dev.microsoft.com). A regisztrációs folyamata gyűjti, és hozzárendeli ezeket az értékeket az alkalmazás:

* Egy **Alkalmazásazonosító** , amely egyedileg azonosítja az alkalmazást
* A **átirányítási URI-** , amely közvetlen válaszokhoz az alkalmazás segítségével
* Néhány más forgatókönyvekre jellemző értékek

További részletek megtudhatja, hogyan [alkalmazások regisztrálásának folyamatával](active-directory-v2-app-registration.md).

Az alkalmazás regisztrálása után az alkalmazás kommunikál az Azure AD kérelmeket küld az Azure AD v2.0-végponttól. Azt adja meg, nyílt forráskódú keretrendszerek és tárak, amelyek kezelik ezeket a kérelmeket részleteit. Lehetősége is van a megvalósítása a hitelesítési logikát saját kérések végpontokkal való létrehozásával:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```
<!-- TODO: Need a page for libraries to link to -->

## <a name="web-apps"></a>Webalkalmazások
A web Apps (.NET, PHP, Java, Ruby, Python, csomópont), amelyhez a felhasználó böngészőn keresztül hozzáfér, használhatja a [OpenID Connect](active-directory-v2-protocols.md) a felhasználói bejelentkezés. Az OpenID Connect a web app egy azonosító jogkivonatot kap. Egy azonosító lexikális elem egy biztonsági jogkivonatot, ellenőrzi a felhasználó személyazonosságát, és a felhasználói jogcímek formájában információkat nyújt:

```
// Partial raw ID token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded ID token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

Minden a jogkivonatok és jogcímek különböző típusairól az alkalmazásban elérhető megismerheti a [v2.0 jogkivonatok hivatkozás](active-directory-v2-tokens.md).

A kiszolgáló webalkalmazásokban bejelentkezés hitelesítési folyamata hajtja végre ezeket a magas szintű lépéseket:

![Webes alkalmazás hitelesítési folyamat](../../media/active-directory-v2-flows/convergence_scenarios_webapp.png)

A felhasználói identitás biztosítható a Azonosítót jogkivonatban érvényesítés a v2.0-végpontra-tól kapott nyilvános aláírókulcs a. A munkamenet cookie-k be van állítva, a felhasználó a következő lapkérések azonosítására használható.

Ebben a forgatókönyvben a művelet megtekintéséhez tekintse meg a webes alkalmazás bejelentkezési Kódminták a 2.0-s verziója [bevezetés](active-directory-appmodel-v2-overview.md#getting-started) szakasz.

Egyszerű bejelentkezhet, valamint egy webalkalmazás-kiszolgáló valószínűleg hozzá kell egy másik webes szolgáltatás, például egy REST API-t. Ebben az esetben a kiszolgáló webalkalmazás kapcsolatba lép kombinált OpenID Connectet és az OAuth 2.0 folyamatában, segítségével a [OAuth 2.0 hitelesítésikód-folyamata](active-directory-v2-protocols.md). Ebben a forgatókönyvben kapcsolatos további információkért olvassa el [Ismerkedés a webalkalmazások és webes API-k](active-directory-v2-devquickstarts-webapp-webapi-dotnet.md).

## <a name="web-apis"></a>Webes API-k
A v2.0-végpontra segítségével webszolgáltatásai, például az alkalmazás RESTful webes API-t. Azonosító-jogkivonatokat és a munkamenet cookie-k helyett egy webes API-t OAuth 2.0 hozzáférési tokent használ, az adatok védelmét és a bejövő kérelmek hitelesítéséhez szükséges. A hívó egy webes API hozzáfűz egy jogkivonatot a HTTP-kérések, például a hitelesítési fejlécéhez:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

A webes API a hozzáférési tokent használ, ellenőrzi az API hívójának identitását, és a hozzáférési jogkivonat kódolt jogcímek információkhoz juthat a hívóról kibontani. Jogkivonatok és jogcímek, az alkalmazások számára elérhető összes típusú kapcsolatos további tudnivalókért lásd: a [v2.0 jogkivonatok hivatkozás](active-directory-v2-tokens.md).

A webes API adhat a felhasználóknak részt vevő vagy tilthatják le az adott funkció vagy adatok engedélyek, más néven jelentkezik, mintha [hatókörök](active-directory-v2-scopes.md). Egy hívó alkalmazás szerezni a hatókör engedéllyel, a felhasználónak bele kell egyeznie a hatókör a folyamat során. A v2.0-végpontra megkérdezi a felhasználót, az engedélyeket, és majd rögzíti a engedélyeket kap a webes API-t minden hozzáférési jogkivonatok. A Web API ellenőrzi a hozzáférési jogkivonatok egyes hívás fogadása és engedélyezési ellenőrzi.

Egy webes API-alkalmazás, illetve a kiszolgáló webalkalmazások, asztali és mobile apps szolgáltatásban, egyoldalas alkalmazások, kiszolgálóoldali démonok, és akár egyéb webes API-k minden típusú hozzáférési jogkivonatok fogadhat. A magas szintű folyamata egy webes API így néz ki:

![Webes API-hitelesítési folyamat](../../media/active-directory-v2-flows/convergence_scenarios_webapi.png)

A webes API biztonságossá OAuth2 hozzáférési jogkivonatok segítségével történő beállításáról tekintse meg a webes API mintakódok a [bevezetés](active-directory-appmodel-v2-overview.md#getting-started) szakasz.

Sok esetben webes API-kat is kell kimenő kérelmek más alsóbb rétegbeli webes API-kat Azure Active Directory által biztosított.  Ehhez az szükséges, webes API-k kihasználhatja az Azure AD **a nevében a** folyamatot, amely lehetővé teszi, hogy a webes API-t a helyszíni Exchange számára egy új hozzáférési jogkivonat kimenő kérelmek használhatók egy bejövő jogkivonatot.  A v2.0-végpontra a folyamat nevében ismertetett [részletesen Itt](active-directory-v2-protocols-oauth-on-behalf-of.md).

## <a name="mobile-and-native-apps"></a>Mobil- és natív alkalmazások
Eszköz telepített alkalmazások, többek között a mobil- és asztali alkalmazások esetében gyakran igényelnek hozzáférést a háttér-szolgáltatásaihoz vagy webes API-k, amelyek adatokat tárolhatnak, és a feladatokat egy felhasználó nevében. Ezek az alkalmazások használatával adhat hozzá bejelentkezési és hitelesítési háttér szolgáltatásaihoz a [OAuth 2.0 hitelesítésikód-folyamata](active-directory-v2-protocols-oauth-code.md).

A folyamatot az alkalmazás fogad egy engedélyezési kód a v2.0-végpontra a felhasználó bejelentkezésekor. Az engedélyezési kód hívására háttér-szolgáltatásaihoz a bejelentkezett felhasználó nevében az alkalmazás engedélyt jelöli. Az alkalmazás továbbíthatja az OAuth 2.0 hozzáférési tokent, és egy frissítési jogkivonat a háttérben engedélyezési kódot. Az alkalmazás a hozzáférési jogkivonat hitelesítéshez használni kívánt webes API-k számára a HTTP-kérelmekre, és a frissítési jogkivonat használatával új hozzáférési jogkivonatok lekérésére, ha régebbi hozzáférési jogkivonatok végén.

![Natív alkalmazás hitelesítési folyamat](../../media/active-directory-v2-flows/convergence_scenarios_native.png)

## <a name="single-page-apps-javascript"></a>Egyoldalas alkalmazások (JavaScript)
Számos modern alkalmazások alkalmazás előtér írt elsősorban a JavaScript rendelkezik. Gyakran írás például AngularJS, az Ember.js vagy Durandal.js keretrendszer használatával. Az Azure AD v2.0-végponttól használatával támogatja ezeket az alkalmazásokat a [OAuth 2.0 típusú implicit engedélyezési folyamat](active-directory-v2-protocols-implicit.md).

A folyamatot, az alkalmazás-jogkivonatokat kap a v2.0-ről a végponthoz, minden kiszolgáló-kiszolgáló cseréje nélkül engedélyezik. Minden hitelesítési logikát és kezelési vesz munkamenet helyezhető el teljes egészében a JavaScript ügyfélprogramokban, de további oldal átirányítja az.

![Implicit hitelesítési folyamat](../../media/active-directory-v2-flows/convergence_scenarios_implicit.png)

Tekintse meg a folyamatot, próbálja meg az alkalmazás mintakódok egyikét a [bevezetés](active-directory-appmodel-v2-overview.md#getting-started) szakasz.

## <a name="daemons-and-server-side-apps"></a>Démonok és kiszolgálóoldali alkalmazások
Az alkalmazások, amelyek hosszú futású folyamatokat, vagy egy felhasználói beavatkozás nélkül is szükség van a védett erőforrások, például webes API-k eléréséhez. Ezek az alkalmazások hitelesítheti és a jogkivonatok lekérésére, az alkalmazás identitásával, nem pedig a felhasználó delegált identitása, az OAuth 2.0 ügyfél hitelesítő adatok folyamata a.

A folyamatot, az alkalmazás kommunikál közvetlenül a `/token` végpont végpontok beszerzése:

![Démon alkalmazás hitelesítési folyamat](../../media/active-directory-v2-flows/convergence_scenarios_daemon.png)

Egy démon alkalmazás elkészítésére, tekintse meg a [ügyfél hitelesítő adatait a dokumentáció](active-directory-v2-protocols-oauth-client-creds.md), vagy próbálja meg egy [.NET mintaalkalmazás](https://github.com/Azure-Samples/active-directory-dotnet-daemon-v2).
