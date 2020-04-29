---
title: Jelentkezzen be az erőforrás-tulajdonos jelszava hitelesítő adataival | Azure
titleSuffix: Microsoft identity platform
description: Támogatja a böngésző nélküli hitelesítési folyamatokat az erőforrás-tulajdonos jelszavas hitelesítő adatai (ROPC) megadása esetén.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: hirsin
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: a282264ed3e9539bcc96babfc41376d2c6c35628
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81868651"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Microsoft Identity platform és OAuth 2,0 erőforrás-tulajdonosi jelszó hitelesítő adatai

A Microsoft Identity platform támogatja a [OAuth 2,0 erőforrás-tulajdonosi jelszó hitelesítő adatait (ROPC)](https://tools.ietf.org/html/rfc6749#section-4.3), amely lehetővé teszi, hogy az alkalmazás közvetlenül a jelszavának kezelésével jelentkezzen be a felhasználóba.  Ez a cikk azt ismerteti, hogyan lehet programozni közvetlenül az alkalmazás protokollját.  Ha lehetséges, javasoljuk, hogy a támogatott Microsoft hitelesítési kódtárakat (MSAL) használja a [jogkivonatok beszerzése és a biztonságos webes API-k hívása](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)helyett.  Tekintse meg az MSAL-t [használó példákat](sample-v2-code.md)is.

> [!WARNING]
> A Microsoft azt javasolja, hogy _ne használja a_ ROPC folyamatot. A legtöbb esetben biztonságosabb alternatívák érhetők el és ajánlottak. Ennek a folyamatnak nagyon nagyfokú megbízhatóságra van szüksége az alkalmazásban, és olyan kockázatokat hordoz, amelyek nem jelennek meg más folyamatokban. Ezt a folyamatot csak akkor használja, ha a többi biztonságosabb folyamat nem használható.

> [!IMPORTANT]
>
> * A Microsoft Identity platform végpontja csak az Azure AD-bérlők ROPC támogatja, a személyes fiókokat nem. Ez azt jelenti, hogy a bérlő-specifikus végpontot (`https://login.microsoftonline.com/{TenantId_or_Name}`) vagy a `organizations` végpontot kell használnia.
> * Az Azure AD-bérlők számára meghívott személyes fiókok nem használhatják a ROPC.
> * Azok a fiókok, amelyeknek nincsenek jelszavai, nem jelentkezhetnek be a ROPC-on keresztül. Ennél a forgatókönyvnél azt javasoljuk, hogy ehelyett használjon egy másik folyamatot az alkalmazáshoz.
> * Ha a felhasználóknak többtényezős hitelesítést (MFA) kell használniuk az alkalmazásba való bejelentkezéshez, a rendszer letiltja a helyet.
> * A ROPC nem támogatott [hibrid identitás-összevonási](/azure/active-directory/hybrid/whatis-fed) forgatókönyvekben (például az Azure ad és a helyszíni fiókok hitelesítéséhez használt ADFS). Ha a felhasználók teljes körűen átirányítják a helyszíni identitás-szolgáltatókat, az Azure AD nem tudja tesztelni a felhasználónevet és a jelszót az identitás-szolgáltatón. Az [átmenő hitelesítés](/azure/active-directory/hybrid/how-to-connect-pta) a ROPC esetében is támogatott.

## <a name="protocol-diagram"></a>Protokoll diagramja

Az alábbi ábrán a ROPC folyamat látható.

![Az erőforrás-tulajdonos jelszava hitelesítő folyamatát ábrázoló diagram](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Engedélyezési kérelem

A ROPC folyamat egyetlen kérelem: elküldi az ügyfél-azonosítót és a felhasználó hitelesítő adatait a IDENTITÁSSZOLGÁLTATÓ, majd visszaküldi a jogkivonatokat. Az ügyfélnek meg kell adnia a felhasználó e-mail-címét (UPN) és a jelszót. A sikeres kérelem után az ügyfélnek biztonságosan fel kell szabadítania a felhasználó hitelesítő adatait a memóriából. Soha nem kell mentenie őket.

> [!TIP]
> Próbálja meg végrehajtani a kérelmet postán!
> [![Próbálja meg futtatni ezt a kérelmet postán](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```HTTP
// Line breaks and spaces are for legibility only.  This is a public client, so no secret is required.

POST {tenant}/oauth2/v2.0/token
Host: login.microsoftonline.com
Content-Type: application/x-www-form-urlencoded

client_id=6731de76-14a6-49ae-97bc-6eba6914391e
&scope=user.read%20openid%20profile%20offline_access
&username=MyUsername@myTenant.com
&password=SuperS3cret
&grant_type=password
```

| Paraméter | Állapot | Leírás |
| --- | --- | --- |
| `tenant` | Kötelező | Az a címtár-bérlő, amelybe be szeretné jelentkezni a felhasználót. Ez lehet a GUID vagy a felhasználóbarát név formátuma. Ez a paraméter nem állítható be `common` vagy `consumers`értékre, de beállítható `organizations`a következőre:. |
| `client_id` | Kötelező | Az alkalmazáshoz hozzárendelt [Azure Portal-Alkalmazásregisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) oldal alkalmazás-(ügyfél-) azonosítója. |
| `grant_type` | Kötelező | Értékre kell állítani `password`. |
| `username` | Kötelező | A felhasználó e-mail címe. |
| `password` | Kötelező | A felhasználó jelszava. |
| `scope` | Ajánlott | Az alkalmazás által igényelt [hatókörök](v2-permissions-and-consent.md)vagy engedélyek szóközzel tagolt listája. Egy interaktív folyamat során a rendszergazdának vagy a felhasználónak meg kell adnia a hatókörét az idő előtt. |
| `client_secret`| Néha szükséges | Ha az alkalmazás nyilvános ügyfél, akkor a vagy `client_secret` `client_assertion` a nem vehető fel.  Ha az alkalmazás bizalmas ügyfél, akkor azt is tartalmaznia kell. |
| `client_assertion` | Néha szükséges | Egy másik formája `client_secret`, amely egy tanúsítvány használatával lett létrehozva.  További részletekért tekintse meg a [tanúsítvány hitelesítő adatait](active-directory-certificate-credentials.md) . |

### <a name="successful-authentication-response"></a>Sikeres hitelesítési válasz

A következő példa egy sikeres jogkivonat-választ mutat be:

```json
{
    "token_type": "Bearer",
    "scope": "User.Read profile openid email",
    "expires_in": 3599,
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD..."
}
```

| Paraméter | Formátum | Leírás |
| --------- | ------ | ----------- |
| `token_type` | Sztring | Mindig állítsa be `Bearer`a következőt:. |
| `scope` | Szóközzel tagolt karakterláncok | Ha egy hozzáférési jogkivonatot adott vissza, akkor ez a paraméter felsorolja azokat a hatóköröket, amelyekhez a hozzáférési jogkivonat érvényes. |
| `expires_in`| int | Azon másodpercek száma, ameddig a tartalmazott hozzáférési jogkivonat érvényes. |
| `access_token`| Átlátszatlan karakterlánc | A kért [hatókörökhöz](v2-permissions-and-consent.md) lett kiállítva. |
| `id_token` | JWT | Kiadva, ha `scope` az eredeti paraméter `openid` tartalmazza a hatókört. |
| `refresh_token` | Átlátszatlan karakterlánc | Kiadva, ha `scope` az eredeti `offline_access`paraméter szerepel. |

A frissítési jogkivonattal új hozzáférési jogkivonatok szerezhetők be, és a tokenek frissítése a [OAuth-programkód dokumentációjában](v2-oauth2-auth-code-flow.md#refresh-the-access-token)ismertetett folyamattal megegyező folyamat használatával végezhető el.

### <a name="error-response"></a>Hiba válasza

Ha a felhasználó nem adta meg a helyes felhasználónevet vagy jelszót, vagy az ügyfél nem kapta meg a kért beleegyezett, a hitelesítés sikertelen lesz.

| Hiba | Leírás | Ügyfél művelete |
|------ | ----------- | -------------|
| `invalid_grant` | A hitelesítés sikertelen | A hitelesítő adatok helytelenek voltak, vagy az ügyfél nem rendelkezik beleegyezik a kért hatókörökkel. Ha a hatókörök nem lettek megadva `consent_required` , a rendszer hibaüzenetet küld. Ha ez történik, az ügyfélnek egy interaktív üzenetbe kell küldenie a felhasználót egy webnézet vagy egy böngésző használatával. |
| `invalid_request` | A kérés nem megfelelően lett kiépítve | A támogatás típusa nem támogatott a vagy `/common` `/consumers` a hitelesítési környezetekben.  Ehelyett `/organizations` használjon vagy egy bérlői azonosítót. |

## <a name="learn-more"></a>Részletek

* Próbálja ki a ROPC a [minta konzol alkalmazás](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)használatával.
* Annak megállapításához, hogy a 2.0-s végpontot kell-e használni, olvassa el a [Microsoft Identity platform korlátozásait](active-directory-v2-limitations.md)ismertetőt.
