---
title: Az Azure Active Directory B2C hozzáférési jogkivonatok igénylése |} A Microsoft Docs
description: Ebből a cikkből megtudhatja, és a egy ügyfélalkalmazás beállítása-és hozzáférési jogkivonat beszerzése.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/09/2017
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2043e0fc9fa63903073311856e7e8d31fb34c506
ms.sourcegitcommit: 00dd50f9528ff6a049a3c5f4abb2f691bf0b355a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2018
ms.locfileid: "51015349"
---
# <a name="azure-ad-b2c-requesting-access-tokens"></a>Az Azure AD B2C: Kérő hozzáférési jogkivonatok

Hozzáférési jogkivonat (néven **hozzáférés\_token** a válaszok az Azure AD B2C-ből a) egy biztonsági jogkivonat erőforrások elérésére szolgál egy ügyfél által védett formája egy [az engedélyezési kiszolgáló](active-directory-b2c-reference-protocols.md), például a webes API-t. Hozzáférési jogkivonatok jelentésekként jelennek meg [JWTs](active-directory-b2c-reference-tokens.md) az importálni kívánt erőforrás-kiszolgáló és a megadott engedélyek a kiszolgálóra vonatkozó adatokat tartalmaznak. Hívja meg az erőforrás-kiszolgáló, ha a hozzáférési jogkivonatot a HTTP-kérelem jelen kell lennie.

Ez a cikk bemutatja, miként ügyfélalkalmazás konfigurálása és a webes API-t a beszerzéséhez egy **hozzáférés\_token**.

> [!NOTE]
> **Webes API-láncok (a – meghatalmazásos) nem támogatja az Azure AD B2C-t.**
>
> Számos architektúrában szerepelnek olyan webes API-t, amelyek egy másik alsóbb rétegbeli webes API egyaránt az Azure AD B2C által védett. Ebben a forgatókönyvben szokás vissza célból egy webes API-t, amely ezután meghív egy Microsoft online szolgáltatáshoz, például az Azure AD Graph API rendelkező natív ügyfelek esetében.
>
> Ez a láncolatba fűzött webes API-forgatókönyv az OAuth 2.0 JWT tulajdonosi hitelesítő adatok biztosítása, más néven az On-meghatalmazásos folyamat használatával támogatható. Azonban az On-meghatalmazásos folyamat még nem implementáltuk az Azure AD B2C-ben.

## <a name="register-a-web-api-and-publish-permissions"></a>Webes API regisztrálása és az engedélyek közzététele

Hozzáférési jogkivonatot kér, mielőtt először a webes API regisztrálása, és tegye közzé az ügyfélalkalmazás megadható engedélyeket (hatóköröket).

### <a name="register-a-web-api"></a>Webes API regisztrálása

1. Az Azure Portalon az Azure AD B2C Funkciók menüjében kattintson a **alkalmazások**.
2. Kattintson a **+ Hozzáadás** a menü tetején.
3. Adjon meg az alkalmazáshoz egy olyan **nevet**, amely a felhasználók számára ismerteti az alkalmazást. Például adja meg "Contoso API".
4. Állítsa az **Include web app / web API** (Webappal/webes API-val együtt) kapcsolót **Yes** (Igen) állásba.
5. Adjon meg egy tetszőleges értéket a **válasz URL-címek**. Adja meg például a következőt: `https://localhost:44316/`. Az érték nem számít, mivel az API-k nem kell kapnia a token közvetlenül az Azure AD B2C-t.
6. Adjon meg egy **Alkalmazásazonosító URI-t**. Ez a webes API-hoz használt azonosító. A mezőben adja meg például: "Megjegyzések". A **Alkalmazásazonosító URI-t** lesz `https://{tenantName}.onmicrosoft.com/notes`.
7. Kattintson a **Create** (Létrehozás) gombra az alkalmazás regisztrálásához.
8. Kattintson az imént létrehozott alkalmazásra, és másolja le az alkalmazás globálisan egyedi **ügyfél-azonosítóját**, amelyet később a kódjában fog használni.

### <a name="publishing-permissions"></a>Közzétételi engedélyek

Hatókörök, amelyek megfelel az engedélyeket, szükség, ha az alkalmazás egy API-t hívja. Néhány példa a hatókörök "olvasási" vagy "write". Tegyük fel, hogy webes vagy natív alkalmazás "olvasási" API-k. Az alkalmazás Azure AD B2C-t hívja és a hatókör "olvasási" hozzáférést biztosító hozzáférési jogkivonat kérése. Ahhoz, hogy az Azure AD B2C hozzáférési jogkivonat kibocsátható az alkalmazás kell jogosítania a "olvasási" az adott API-ból. Ehhez az API-t először meg kell közzétenni a "olvasási" hatókör.

1. Az Azure AD B2C belül **alkalmazások** menüben nyissa meg a webes API-alkalmazás ("Contoso API").
2. Kattintson a **Published scopes** (Közzétett hatókörök) elemre. Itt határozza meg a más alkalmazásoknak megadható engedélyeket (hatóköröket).
3. Adjon hozzá **hatókör értékeinek** szükség szerint (például "olvassa el"). Alapértelmezés szerint a „user_impersonation” hatókör van meghatározva. Figyelmen kívül hagyhatja ezt, ha szeretné. Adjon meg egy leírást a hatókör az **hatókör neve** oszlop.
4. Kattintson a **Save** (Mentés) gombra.

> [!IMPORTANT]
> A **hatókör neve** leírása, a **hatókör értéke**. A hatókör használata esetén mindenképp használja a **hatókör értéke**.

## <a name="grant-a-native-or-web-app-permissions-to-a-web-api"></a>Adja meg egy natív vagy webes Alkalmazásengedélyek egy webes API-hoz

Miután egy API közzé hatókörök van konfigurálva, az ügyfélalkalmazás kell adni ezeket a hatókörök, az Azure Portalon keresztül.

1. Keresse meg a **alkalmazások** menü az Azure AD B2C Funkciók menüben.
2. Regisztráljon egy ügyfélalkalmazás ([webalkalmazás](active-directory-b2c-app-registration.md) vagy [natív ügyfél](active-directory-b2c-app-registration.md)) Ha már nincs ilyen. Ha ez az útmutató a kiindulási pontként követi, szüksége lesz egy ügyfélalkalmazás regisztrálására.
3. Kattintson a **API-hozzáférés**.
4. Kattintson a **Hozzáadás** gombra.
5. Válassza ki a webes API és a hatóköröket (engedélyeket), amelyet szeretne megadni.
6. Kattintson az **OK** gombra.

> [!NOTE]
> Az Azure AD B2C nem kér az ügyfél az alkalmazáshasználók hozzájárulásukat. Ehelyett minden hozzájárulása a fent leírt alkalmazás közötti konfigurált engedélyek alapján, a rendszergazda által biztosított. Ha egy alkalmazás engedélymegadásának visszavonja, minden felhasználó, aki korábban sikerült lekérni az engedélyt már nem tudja megtenni.

## <a name="requesting-a-token"></a>A jogkivonat kérése

Amikor hozzáférési jogkivonatot kér az ügyfélalkalmazás kell adja meg a kívánt engedélyeket a **hatókör** paraméter a kérelem. Adja meg például a **hatókör értéke** "olvasási" az API-hoz, amely rendelkezik a **Alkalmazásazonosító URI-t** , `https://contoso.onmicrosoft.com/notes`, a hatókör lenne `https://contoso.onmicrosoft.com/notes/read`. Alul látható egy példa egy engedélyezési kód kérést a `/authorize` végpont.

> [!NOTE]
> Egyéni tartományok jelenleg nem támogatottak és hozzáférési jogkivonatokat. A kérelem URL-CÍMÉT az tenantName.onmicrosoft.com tartományához kell használnia.

```
https://<tenantName>.b2clogin.com/tfp/<tenantName>.onmicrosoft.com/<yourPolicyId>/oauth2/v2.0/authorize?client_id=<appID_of_your_client_application>&nonce=anyRandomValue&redirect_uri=<redirect_uri_of_your_client_application>&scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread&response_type=code 
```

A kérésben több engedély beszerzéséhez, adhat hozzá több bejegyzés az egyetlen **hatókör** paramétert, szóközzel elválasztva. Példa:

Dekódolni URL-címe:

```
scope=https://contoso.onmicrosoft.com/notes/read openid offline_access
```

URL-kódolású:

```
scope=https%3A%2F%2Fcontoso.onmicrosoft.com%2Fnotes%2Fread%20openid%20offline_access
```

További hatóköröket (engedélyeket) kérheti egy erőforrás-nál mit kap az ügyfélalkalmazás számára. Ez a helyzet, ha a hívás akkor sikeres, ha legalább egy engedélyt kapnak. A létrejövő **hozzáférés\_token** fog rendelkezni a "" hatókörjogcímet csak a szükséges engedélyeket, amelyek sikeresen engedélyezve lettek feltöltve.

> [!NOTE] 
> Két különböző webes erőforrásokon kér engedélyeket a kérésben nem támogatott. Az ilyen típusú kérelem sikertelen lesz.

### <a name="special-cases"></a>Bizonyos esetekben

Az OpenID Connect standard több speciális "hatókör" értéket adja meg. A következő különleges hatókörök "érhető el a felhasználói profil" engedélyt az mutatják be:

* **openid**: Ez a kérelmek egy azonosító jogkivonat
* **kapcsolat nélküli\_hozzáférés**: Ez a frissítési jogkivonatot kér (használatával [hitelesítési kód folyamatok](active-directory-b2c-reference-oauth-code.md)).

Ha a `response_type` paramétert egy `/authorize` kérelmet tartalmaz `token`, a `scope` paraméternek tartalmaznia kell legalább egy erőforrás hatókör (nem `openid` és `offline_access`), amely megkapja. Ellenkező esetben a `/authorize` kérelem hibával leáll.

## <a name="the-returned-token"></a>A visszaadott jogkivonat

A sikeres minted **hozzáférés\_token** (vagy a `/authorize` vagy `/token` végpont), a következő jogcímek lesznek jelen:

| Name (Név) | Igénylés | Leírás |
| --- | --- | --- |
|Közönség |`aud` |A **Alkalmazásazonosító** az egyetlen erőforrás, amely a token engedélyt biztosít. |
|Hatókör |`scp` |Az erőforráshoz rendelt engedélyeket. Megadott engedélyek több helyet fogja elválasztani. |
|Jogosult fél |`azp` |A **Alkalmazásazonosító** az ügyfélalkalmazás által kezdeményezett a kérelmet. |

Ha az API-t kap a **hozzáférés\_token**, azt kell [a jogkivonat érvényesítéséhez](active-directory-b2c-reference-tokens.md) igazolnia, hogy a jogkivonat hiteles, és rendelkezik a megfelelő jogcímeket.

Mindig tudjuk nyissa meg a vélemények és tanácsok! Ha ez a témakör a nehézségek rendelkezik, látogasson el az Stack Overflow címke használatával ["azure-ad-b2c"](https://stackoverflow.com/questions/tagged/azure-ad-b2c). A szolgáltatással kapcsolatos kéréseit, hozzáadhatja őket a [UserVoice](https://feedback.azure.com/forums/169401-azure-active-directory/category/160596-b2c).

## <a name="next-steps"></a>További lépések

* Egy webes API használatával [.NET Core](https://github.com/Azure-Samples/active-directory-b2c-dotnetcore-webapi)
* Egy webes API használatával [Node.JS](https://github.com/Azure-Samples/active-directory-b2c-javascript-nodejs-webapi)
