---
title: "Alkalmazástípusok – Azure AD B2C | Microsoft Docs"
description: "A cikk az Azure Active Directory B2C-ben létrehozható alkalmazások típusait ismerteti."
services: active-directory-b2c
documentationcenter: 
author: dstrockis
manager: mbaldwin
editor: 
ms.assetid: bb9d4abe-0db7-4bd9-b0c4-2f43b2c9cf33
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 12/06/2016
ms.author: dastrock
ms.translationtype: HT
ms.sourcegitcommit: 48dfc0fa4c9ad28c4c64c96ae2fc8a16cd63865c
ms.openlocfilehash: 51001feb17ae99d3bd391a9f980d514e07f97099
ms.contentlocale: hu-hu
ms.lasthandoff: 08/30/2017

---
# <a name="azure-active-directory-b2c-types-of-applications"></a>Azure Active Directory B2C: Alkalmazások típusai
Az Azure Active Directory (Azure AD) B2C számos különböző modern alkalmazásarchitektúrához használható hitelesítést tartalmaz. Ezek mindegyike az iparági szabványnak számító [OAuth 2.0](active-directory-b2c-reference-protocols.md) vagy [OpenID Connect](active-directory-b2c-reference-protocols.md) protokollon alapul. Ebben a dokumentumban röviden leírjuk, hogy milyen típusú alkalmazásokat hozhat létre. Mindez független a programozási nyelvtől vagy a használt platformtól. A cikk segíthet az összetettebb feladatok megértésében, ezért érdemes elolvasni, mielőtt nekifog [az alkalmazások létrehozásának](active-directory-b2c-overview.md#get-started).

## <a name="the-basics"></a>Az alapok
Az Azure AD B2C-t használó alkalmazásokat az [Azure Portalon](https://portal.azure.com/) keresztül regisztrálni kell az Ön [B2C-címtárában](active-directory-b2c-get-started.md). Az alkalmazásregisztrációs művelet során a rendszer összegyűjt bizonyos adatokat, majd értékeket rendel az alkalmazáshoz:

* **Application ID** (Alkalmazásazonosító), amely egyedileg azonosítja az alkalmazást.
* **Redirect URI** (Átirányítási URI), amely az alkalmazásnak küldött közvetlen válaszokhoz használható.
* Más, az adott feladathoz tartozó értékek. További részletekért ismerkedjen meg az [alkalmazások regisztrálásának folyamatával](active-directory-b2c-app-registration.md).

Az alkalmazás a regisztrációt követően az Azure AD v2.0-végpontokra küldött kérésekkel kommunikál az Azure AD-vel:

```
https://login.microsoftonline.com/common/oauth2/v2.0/authorize
https://login.microsoftonline.com/common/oauth2/v2.0/token
```

Az Azure AD B2C-nek küldött kérések meghatároznak egy **szabályzatot** is. A szabályzatok vezérlik az Azure AD működését. A végpontok segítségével ezenfelül testre szabható felhasználói élmények is létrehozhatók. Gyakran használt szabályzatok például a következők: regisztráció, bejelentkezés, profilmódosítás és így tovább. Ha nincs tisztában a szabályzatokkal, a folytatás előtt olvasson utána az Azure AD B2C [bővíthető szabályzat-keretrendszernek](active-directory-b2c-reference-policies.md).

Az alkalmazások és a v2.0-végpontok közötti interakció minden esetben hasonló felső szintű mintát követ:

1. Az alkalmazás elirányítja a felhasználót a v2.0-végpontra a [szabályzat](active-directory-b2c-reference-policies.md) végrehajtása érdekében.
2. A felhasználó a szabályzat definíciója szerint teljesíti a szabályzat feltételeit.
3. Az alkalmazás valamilyen biztonsági jogkivonatot kap a v2.0-végponttól.
4. Az alkalmazás a biztonsági jogkivonat segítségével eléri a védett adatokat vagy a védett erőforrásokat.
5. Az erőforrás-kiszolgáló ellenőrzi a biztonsági jogkivonatot, és megállapítja, hogy megadható-e hozzáférés.
6. Az alkalmazás rendszeres időközönként frissíti a biztonsági jogkivonatot.

<!-- TODO: Need a page for libraries to link to -->
Ezek a lépések a létrehozandó alkalmazás típusától függően némileg eltérőek lehetnek. A részleteket nyílt forráskódú kódtárakból is megismerheti.

## <a name="web-apps"></a>Webalkalmazások
A kiszolgálón futtatott és böngészőn keresztül elért webalkalmazások (ideértve a .NET- , a PHP-, a Java-, a Ruby-, a Python- és a Node.js-alapú alkalmazásokat) esetében az Azure AD B2C az összes felhasználói élmény esetében támogatja az [OpenID Connect](active-directory-b2c-reference-protocols.md) protokollt. Ide tartozik a bejelentkezés, a regisztráció és a profilkezelés is. Az OpenID Connect Azure AD B2C-implementációjában a webalkalmazás az Azure AD felé küldött hitelesítési kérések alapján indítja el a különböző felhasználói élményeket. A kérés eredménye egy `id_token`. Ez a biztonsági jogkivonat tartalmazza a felhasználó identitását. Ezenfelül jogcímek formájában információkat nyújt a felhasználóról is:

```
// Partial raw id_token
eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImtyaU1QZG1Cd...

// Partial content of a decoded id_token
{
    "name": "John Smith",
    "email": "john.smith@gmail.com",
    "oid": "d9674823-dffc-4e3f-a6eb-62fe4bd48a58"
    ...
}
```

A [B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md) további információkat tudhat meg az alkalmazásban elérhető jogkivonatok és jogcímek különböző típusairól.

A webalkalmazásban a [szabályzat](active-directory-b2c-reference-policies.md) minden egyes végrehajtása a következő magas szintű lépéseket járja végig:

![Webalkalmazás, sávok](./media/active-directory-b2c-apps/webapp.png)

A felhasználó identitásának ellenőrzéséhez elegendő az Azure AD-tól kapott nyilvános aláírókulcs segítségével ellenőrizni a `id_token`érvényességét. Ezzel egyúttal beállít egy munkamenetcookie-t is, amely a következő lapkérések során azonosítja a felhasználót.

Ha szeretné élőben is megtekinteni a folyamatot, próbálja ki valamelyik webalkalmazás-bejelentkezési kódmintát [a kezdeti lépéseket bemutató cikkből](active-directory-b2c-overview.md#get-started).

A bejelentkezés biztosítása mellett a webkiszolgáló-alkalmazásban szükség lehet webes háttérszolgáltatások elérésére is. Ebben az esetben a webalkalmazást egy némileg különböző [OpenID Connect-folyamat](active-directory-b2c-reference-oidc.md) elvégzésére is be lehet állítani, amelynek keretében hitelesítési kódok és frissítési jogkivonatok segítségével szerzi be a jogkivonatokat. Ezt a folyamatot a következő, [Webes API-k](#web-apis) című fejezet írja le.

<!--, and in our [WebApp-WebAPI Getting started topic](active-directory-b2c-devquickstarts-web-api-dotnet.md).-->

## <a name="web-apis"></a>Webes API-k
Az Azure AD B2C segítségével védelmet állíthat be webszolgáltatásai, például az alkalmazás RESTful webes API-jai számára is. A webes API-k az OAuth 2.0 használatával biztosíthatják az adatok védelmét a bejövő HTTP-kérések jogkivonatokkal történő hitelesítésével. A webes API hívója hozzáfűz egy jogkivonatot a HTTP-kérés hitelesítési fejlécéhez:

```
GET /api/items HTTP/1.1
Host: www.mywebapi.com
Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6...
Accept: application/json
...
```

Így a webes API a jogkivonat segítségével ellenőrizheti az API hívójának identitását, valamint a jogkivonatban kódolt jogcímek segítségével további információkhoz juthat a hívóról. Az [Azure AD B2C-jogkivonatok referenciájából](active-directory-b2c-reference-tokens.md) további információkat tudhat meg az alkalmazásban elérhető jogkivonatok és jogcímek különböző típusairól.

> [!NOTE]
> Az Azure AD B2C jelenleg csak azokat a webes API-kat támogatja, amelyekhez a saját, jól ismert ügyfeleikkel lehet hozzáférni. Az elkészült alkalmazás tartalmazhat például egy iOS-alkalmazást, egy Android-alkalmazást, valamint egy háttérben futó webes API-t. Ez az architektúra már most is teljes támogatást élvez. A rendszer ugyanakkor jelenleg nem támogatja, hogy ugyanezt az API-t egy partnerügyfél (például egy másik iOS-alkalmazás) is elérje. Az elkészült alkalmazás összetevőinek egyazon alkalmazásazonosítót kell használniuk.
>
>

A webes API számos különböző típusú ügyféltől (például webalkalmazásoktól, asztali és mobilalkalmazásoktól, egylapos alkalmazásoktól, kiszolgálóoldali démonoktól vagy más webes API-któl) képes jogkivonatokat fogadni. Az alábbiakban egy webes API-t meghívó webalkalmazás teljes folyamatára láthat példát:

![Webalkalmazás, webes API sávok](./media/active-directory-b2c-apps/webapi.png)

Ha többet szeretne tudni a hitelesítési kódokról, frissítési jogkivonatokról, valamint a jogkivonatok lekérésének lépéseiről, olvasson az [OAuth 2.0-protokollról](active-directory-b2c-reference-oauth-code.md).

Ha szeretné megtanulni, hogyan biztosíthat védelmet a webes API-k számára az Azure AD B2C segítségével, olvassa el a [kezdeti lépéseket bemutató cikk](active-directory-b2c-overview.md#get-started) webes API-kra vonatkozó oktatóanyagát.

## <a name="mobile-and-native-apps"></a>Mobil- és natív alkalmazások
Az eszközökre telepített alkalmazások (többek között a mobil- és asztali alkalmazások) gyakran igényelnek hozzáférést háttérszolgáltatásokhoz vagy webes API-khoz a felhasználók nevében. Az Azure AD B2C és az [OAuth 2.0 hitelesítésikód-folyamata](active-directory-b2c-reference-oauth-code.md) segítségével egyedi identitáskezelési eszközöket adhat a natív alkalmazásokhoz, valamint biztonságosan megoldhatja a háttérszolgáltatások behívását is.  

Ebben a folyamatban az alkalmazás végrehajtja a [szabályzatokat](active-directory-b2c-reference-policies.md), majd, ha a felhasználó teljesítette a szabályzat elvárásait, fogadja az Azure AD által küldött `authorization_code`-ot. Az `authorization_code` azt jelenti, hogy az alkalmazás engedélyt adott a háttérszolgáltatásoknak az aktuálisan bejelentkezett felhasználó nevében történő meghívására. Az alkalmazás ezt követően a háttérben `id_token`-re és `refresh_token`-re cseréli az `authorization_code`-ot.  Az alkalmazás az `id_token` segítségével hitelesíti a háttérben futó webes API-t a HTTP-kérésekben. Az `refresh_token` alkalmas ezenfelül új `id_token` kérésére is, ha a régi lejárna.

> [!NOTE]
> Az Azure AD B2C jelenleg csak azon jogkivonatokat támogatja, amelyek egy alkalmazás saját háttér-webszolgáltatásának az elérésére szolgálnak. Az elkészült alkalmazás tartalmazhat például egy iOS-alkalmazást, egy Android-alkalmazást, valamint egy háttérben futó webes API-t. Ez az architektúra már most is teljes támogatást élvez. A rendszer jelenleg nem támogatja azonban azt, hogy az iOS-alkalmazás OAuth 2.0-hozzáférési jogkivonatok segítségével hozzáférjen a partneri webes API-khoz. Az elkészült alkalmazás összetevőinek egyazon alkalmazásazonosítót kell használniuk.
>
>

![Natív alkalmazás, sávok](./media/active-directory-b2c-apps/native.png)

## <a name="current-limitations"></a>Aktuális korlátozások
Az Azure AD B2C jelenleg nem támogatja a következő típusú alkalmazásokat. A támogatásuk bevezetése a későbbiekben várható. 

### <a name="daemonsserver-side-apps"></a>Démonok/kiszolgálóoldali alkalmazások
A hosszú futású folyamatokat tartalmazó, illetve a felhasználó jelenléte nélkül is működő alkalmazások esetében szükséges lehetőséget adni a biztonságos erőforrások, például webes API-k elérésére. Ezek az alkalmazások saját identitásuk (azaz nem a felhasználó delegált identitása), valamint az OAuth 2.0 ügyfél-hitelesítő adatok folyamata segítségével tudják elvégezni a hitelesítést és lekérni a jogkivonatokat.

Az Azure AD B2C jelenleg nem támogatja a folyamatot. Ezek az alkalmazások csak akkor képesek a jogkivonatok lekérésére, ha már végbement egy interaktív felhasználói folyamat.

### <a name="web-api-chains-on-behalf-of-flow"></a>Webes API-láncok (meghatalmazásos folyamat)
Számos architektúrában szerepelnek olyan webes API-k, amelyek más, alsóbb rétegbeli webes API-kat hívnak meg, és mindkét API biztonságát az Azure AD B2C garantálja. Ez gyakori a webes API-háttérrel rendelkező natív ügyfelek esetében. Ez aztán meghív egy Microsoft online szolgáltatást, például az Azure AD Graph API-t.

Ez a láncolatba fűzött webes API-megoldás az OAuth 2.0 JWT tulajdonosi hitelesítő adatok megadásával (vagy más néven a meghatalmazásos folyamat) segítségével valósítható meg.  A meghatalmazásos folyamatot azonban még nem implementáltuk az Azure AD B2C-be.

