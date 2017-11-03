---
title: "A kért hozzáférési jogkivonatok – az Azure AD B2C |} Microsoft Docs"
description: "Ez a cikk bemutatja, hogyan kell beállítania egy ügyfélalkalmazást, és egy hozzáférési jogkivonat."
services: active-directory-b2c
documentationcenter: android
author: parakhj
manager: krassk
editor: 
ms.assetid: 1c75f17f-5ec5-493a-b906-f543b3b1ea66
ms.service: active-directory-b2c
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/09/2017
ms.author: parakhj
ms.openlocfilehash: 7202be4e0e9b8b28b5ec1443d6d248c1738da6fb
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Az Azure AD B2C: A kért hozzáférési jogkivonatok

Olyan hozzáférési jogkivonatot (jelölése **hozzáférés\_token** az Azure AD B2C válaszát) a biztonsági jogkivonat ügyfél használhatja a erőforrások eléréséhez által védett egy formája, amely egy [engedélyezési server](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-protocols#the-basics), például egy webes API-t. Hozzáférési jogkivonatok helyettesítik [JWTs](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-reference-tokens#types-of-tokens) és a kívánt erőforrás-kiszolgáló és az engedélyek a kiszolgáló adatait tartalmazzák. Az erőforrás-kiszolgáló meghívásakor a hozzáférési jogkivonat szerepelnie kell a HTTP-kérelmek.

Ez a cikk ismerteti a ügyfélalkalmazás konfigurálása és a webes API-t ahhoz, hogy az beszerzése egy **hozzáférés\_token**.

> [!NOTE]
> **Webes API-láncok (a-nevében-a) nem támogatja az Azure AD B2C.**
>
> Számos architektúrában szerepelnek olyan webes API, amely egy másik alsóbb rétegbeli webes API-t, mind az Azure AD B2C által védett hívnak. Ez a forgatókönyv esetében gyakori, a hátsó záró egy webes API-t, amely meghívja a egy Microsoft Online Services szolgáltatással, például az Azure AD Graph API rendelkező natív ügyfelek.
>
> Ez a láncolatba fűzött webes API-forgatókönyv támogatja az OAuth 2.0 JWT tulajdonosi hitelesítő adatok megadása, más néven az On-meghatalmazásos folyamat használatával. Azonban az On-meghatalmazásos folyamat jelenleg nincs megvalósítva az Azure AD B2C.

## <a name="register-a-web-api-and-publish-permissions"></a>Webes API-k regisztrálja, és tegye közzé az engedélyek

Mielőtt kérelmezi a hozzáférési tokent, először webes API-k regisztrálását, és akkor adhatók engedélyek (hatókör) közzététele az ügyfélalkalmazást.

### <a name="register-a-web-api"></a>Webes API regisztrálása

1. Az Azure portálon az Azure AD B2C funkciók menüsorban kattintson **alkalmazások**.
1. Kattintson a **+ Hozzáadás** a menü tetején.
1. Adjon meg az alkalmazáshoz egy olyan **nevet**, amely a felhasználók számára ismerteti az alkalmazást. Például adja meg "Contoso API".
1. Állítsa az **Include web app / web API** (Webappal/webes API-val együtt) kapcsolót **Yes** (Igen) állásba.
1. Adjon meg egy tetszőleges értéke a **válasz URL-címek**. Adja meg például a következőt: `https://localhost:44316/`. Az érték nem számít, mivel az API-k kell nem kapnia a jogkivonatot az Azure AD B2C-ről.
1. Adjon meg egy **Alkalmazásazonosító URI-t**. Ez a webes API-hoz használt azonosító. A mezőbe írja be például, "Megjegyzések". A **App ID URI** lesz `https://{tenantName}.onmicrosoft.com/notes`.
1. Kattintson a **Create** (Létrehozás) gombra az alkalmazás regisztrálásához.
1. Kattintson az imént létrehozott alkalmazásra, és másolja le az alkalmazás globálisan egyedi **ügyfél-azonosítóját**, amelyet később a kódjában fog használni.

### <a name="publishing-permissions"></a>Közzétételi engedélyek

Hatókörök, amely megfelel az engedélyeket, szükség, ha az alkalmazás hívja az API-t. Néhány példa a hatókörök "olvasása" vagy "write". Tegyük fel, hogy a webhelyen vagy a natív alkalmazással az API-k "olvasható". Az alkalmazás ehhez hívja meg az Azure AD B2C és egy hozzáférési jogkivonatot, amely hozzáférést biztosít a hatókör "read" kérése. Ahhoz, hogy az Azure AD B2C hozható létre olyan hozzáférési jogkivonatot engedéllyel "olvasni" az adott API-t kell az alkalmazást. Ehhez az API-t először meg kell közzétenni a "olvasása" hatókörben.

1. Az Azure AD B2C belül **alkalmazások** menüben nyissa meg a webes API-alkalmazás ("Contoso API").
1. Kattintson a **Published scopes** (Közzétett hatókörök) elemre. Itt határozza meg a más alkalmazásoknak megadható engedélyeket (hatóköröket).
1. Adja hozzá **hatókörök értékeinek** szükség szerint "(például" olvasása). Alapértelmezés szerint a „user_impersonation” hatókör van meghatározva. Ha ez kihagyhatja. Adja meg a hatókör leírását a **hatókör neve** oszlop.
1. Kattintson a **Save** (Mentés) gombra.

> [!IMPORTANT]
> A **hatókör neve** leírása a **hatókör értéke**. A hatókör használata esetén ügyeljen arra, hogy használja a **hatókör értéke**.

## <a name="grant-a-native-or-web-app-permissions-to-a-web-api"></a>Adja meg egy natív vagy webalkalmazás-Alkalmazásengedélyek egy webes API-hoz

Miután az API-k hatókörök közzétételére van konfigurálva, az ügyfélalkalmazás kell adni ezeket hatókörök az Azure-portálon.

1. Keresse meg a **alkalmazások** menü az Azure AD B2C Funkciók menüjében.
1. Egy ügyfélalkalmazás regisztrálni ([webalkalmazás](active-directory-b2c-app-registration.md#register-a-web-app) vagy [natív ügyfél](active-directory-b2c-app-registration.md#register-a-mobile-or-native-app)) Ha már nincs ilyen. Ha ez az útmutató következő a kiindulási pontként, szüksége lesz egy ügyfélalkalmazás regisztrálni.
1. Kattintson a **API-hozzáférés**.
1. Kattintson a **Hozzáadás** gombra.
1. Válassza ki a webes API-t és a hatókörök (engedélyek), amelyhez hozzá szeretné adni.
1. Kattintson az **OK** gombra.

> [!NOTE]
> Az Azure AD B2C nem kéri az ügyfél az alkalmazás felhasználóinak a hozzájárulásukat. Ehelyett minden beleegyezést biztosítja a rendszergazda, a fent leírt alkalmazás közötti konfigurált jogosultságok alapján. Ha az alkalmazás engedélyt támogatás visszavonja, minden felhasználó, aki korábban sikerült megszerezni az engedélyt már nem lesz erre képes.

## <a name="requesting-a-token"></a>Jogkivonat kérésével

Ha kérelmezi a hozzáférési tokent, az ügyfélalkalmazás kell meghatároznia a kívánt engedélyeket a **hatókör** paraméter a kérelem. Ahhoz például, hogy adja meg a **hatókör értéke** "Olvasás", az API-hoz, amely rendelkezik a **App ID URI** a `https://contoso.onmicrosoft.com/notes`, a hatókör lenne `https://contoso.onmicrosoft.com/notes/read`. Az alábbiakban látható egy példa egy engedélyezési kód kérést a `/authorize` végpont.

> [!NOTE]
> Egyéni tartományok jelenleg nem támogatottak hozzáférési jogkivonatok együtt. A kérelem URL-CÍMÉT a tenantName.onmicrosoft.com tartományt kell használnia.

```
https://login.microsoftonline.com/<tenantName>.onmicrosoft.com/oauth2/v2.0/authorize?p=<yourPolicyId>&client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

A kérésben több engedélyeket szerezni, adhat hozzá több bejegyzés egyszeres **hatókör** paraméter választják el egymástól. Példa:

Dekódolt URL-címe:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

Kódolt URL-címe:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

További hatókörök (engedélyek) mint mi az ügyfélalkalmazás számára adott erőforrás kérheti. Ez a helyzet, amikor a hívás sikeres lesz, ha legalább egy engedélyt. A létrejövő **hozzáférés\_token** lesz feltöltve csak azokat az engedélyeket, melyeket sikeres rendelkeznek "scp" kérelmét.

> [!NOTE] 
> A kérésben két különböző webes erőforrásokon kérelmező engedélyek nem támogatott. Az ilyen kérelem sikertelen lesz.

### <a name="special-cases"></a>Bizonyos esetekben

Az OpenID Connect szabvány határozza meg, hogy több különleges "hatókör" értéket. A következő különleges hatókörök a hozzáférése "profil" mutatják be:

* **openid**: Ez az azonosító tokent kérelmek
* **kapcsolat nélküli\_hozzáférés**: ezt kéri, hogy a frissítési jogkivonat (használatával [viszonylatában Auth kód](active-directory-b2c-reference-oauth-code.md)).

Ha a `response_type` paraméterének egy `/authorize` kérelemben `token`, a `scope` paraméternek tartalmaznia kell legalább egy erőforrás hatókör (eltérő `openid` és `offline_access`) fog kapni. Ellenkező esetben a `/authorize` kérelem hibával leáll.

## <a name="the-returned-token"></a>A visszaküldött jogkivonat

A sikeresen minted **hozzáférés\_token** (vagy a a `/authorize` vagy `/token` végpont), a következő jogcímeket is jelen lesz:

| Név | Jogcím | Leírás |
| --- | --- | --- |
|Célközönség |`aud` |A **Alkalmazásazonosító** a token biztosít hozzáférést egyetlen erőforrás. |
|Hatókör |`scp` |Az erőforráshoz rendelt engedélyek. Több engedélyeket terület lesz elválasztva. |
|Jogosult felek |`azp` |A **Alkalmazásazonosító** az ügyfélalkalmazás kezdeményező a kérelmet. |

Ha az API-t kap a **hozzáférés\_token**, azt kell [ellenőrzése a jogkivonat](active-directory-b2c-reference-tokens.md) igazolnia, hogy a jogkivonat hiteles, és rendelkezik a megfelelő jogcímeket.

Azt mindig nyitva a visszajelzések és tanácsok! Ha bármilyen nehézségbe ütközik az ebben a témakörben, tegye a Stack Overflow címkével ["azure-ad-b2c"](https://stackoverflow.com/questions/tagged/azure-ad-b2c). A szolgáltatás kéréseket, hozzáadhatja őket a [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

## <a name="next-steps"></a>Következő lépések

* Egy webes API használatával Build [.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)
* Egy webes API használatával Build [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)
