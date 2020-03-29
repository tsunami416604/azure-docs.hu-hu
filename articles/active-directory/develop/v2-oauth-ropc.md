---
title: Bejelentkezés az erőforrás-tulajdonosi jelszó hitelesítő adataival támogatás | Azure
titleSuffix: Microsoft identity platform
description: Támogatja a böngésző nélküli hitelesítési folyamatokat az erőforrás-tulajdonosi jelszó hitelesítő adatok (ROPC) támogatás használatával.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/19/2019
ms.author: ryanwi
ms.reviewer: hirsin
ms.custom: aaddev
ms.openlocfilehash: b935ad2491ca486a3bc6878f0332e5390600b1bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76700685"
---
# <a name="microsoft-identity-platform-and-oauth-20-resource-owner-password-credentials"></a>Microsoft identity platform és OAuth 2.0 Erőforrás-tulajdonos jelszóhitelesítő adatai

A Microsoft identity platform támogatja az [OAuth 2.0 Erőforrás-tulajdonos i jelszóhitelesítő adatok (ROPC) támogatást,](https://tools.ietf.org/html/rfc6749#section-4.3)amely lehetővé teszi az alkalmazás számára, hogy közvetlenül kezelje a jelszót.  Ez a cikk azt ismerteti, hogy miként programozhat közvetlenül az alkalmazásban lévő protokoll ellen.  Ha lehetséges, azt javasoljuk, hogy a támogatott Microsoft Authentication Libraries (MSAL) helyett [a jogkivonatok beszerzéséhez és a biztonságos webes API-k hívásához](authentication-flows-app-scenarios.md#scenarios-and-supported-authentication-flows)használja.  Is vessen egy pillantást a [minta alkalmazások at MSAL](sample-v2-code.md).

> [!WARNING]
> A Microsoft azt javasolja, hogy _ne_ használja a ROPC-folyamatot. A legtöbb esetben biztonságosabb alternatívák állnak rendelkezésre és ajánlottak. Ez az áramlás nagyon nagy fokú bizalmat igényel az alkalmazásiránt, és olyan kockázatokat hordoz, amelyek nincsenek jelen más folyamatokban. Ezt a folyamatot csak akkor használja, ha más biztonságosabb folyamatok nem használhatók.

> [!IMPORTANT]
>
> * A Microsoft identity platform végpont csak az Azure AD-bérlők ROPC-jét támogatja, a személyes fiókokat nem. Ez azt jelenti, hogy bérlő-specifikus`https://login.microsoftonline.com/{TenantId_or_Name}`végpontot `organizations` ( ) vagy a végpontot kell használnia.
> * Az Azure AD-bérlőbe meghívott személyes fiókok nem használhatják a ROPC-t.
> * A jelszóval nem rendelkező fiókok nem tudnak bejelentkezni a ROPC-n keresztül. Ebben az esetben azt javasoljuk, hogy egy másik folyamatot használjon az alkalmazáshoz.
> * Ha a felhasználóknak többtényezős hitelesítést (MFA) kell használniuk az alkalmazásba való bejelentkezéshez, akkor a rendszer le tiltja őket.
> * A ROPC nem támogatott [a hibrid identitás-összevonási](/azure/active-directory/hybrid/whatis-fed) forgatókönyvekben (például a helyszíni fiókok hitelesítéséhez használt Azure AD és ADFS). Ha a felhasználók teljes oldalas átirányítása egy helyszíni identitásszolgáltatók, az Azure AD nem tudja tesztelni a felhasználónevet és a jelszót az adott identitásszolgáltatóval szemben. [Az átmenő hitelesítést](/azure/active-directory/hybrid/how-to-connect-pta) azonban a ROPC támogatja.

## <a name="protocol-diagram"></a>Protokolldiagram

Az alábbi ábra a ROPC-folyamatot mutatja be.

![Az erőforrás-tulajdonos jelszóhitelesítő adatfolyamát bemutató diagram](./media/v2-oauth2-ropc/v2-oauth-ropc.svg)

## <a name="authorization-request"></a>Engedélyezési kérelem

A ROPC-folyamat egyetlen kérelem: elküldi az ügyfélazonosítót és a felhasználó hitelesítő adatait az IDP-nek, majd jogkivonatokat kap cserébe. Az ügyfélnek ehhez meg kell kérnie a felhasználó e-mail címét (UPN) és jelszavát. Közvetlenül a sikeres kérés után az ügyfélnek biztonságosan fel kell szabadítania a felhasználó hitelesítő adatait a memóriából. Soha nem mentheti meg őket.

> [!TIP]
> Próbálja meg végrehajtani ezt a kérést a Postman!
> [![Próbálja meg futtatni ezt a kérést a Postman ben](./media/v2-oauth2-auth-code-flow/runInPostman.png)](https://app.getpostman.com/run-collection/f77994d794bab767596d)


```
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
| `tenant` | Kötelező | Az a címtár-bérlő, amelybe a felhasználót be szeretné jelentkezni. Ez lehet GUID vagy rövid név formátumban. Ez a paraméter nem `common` állítható `consumers`be a vagy `organizations`a ikonra, de lehet. |
| `client_id` | Kötelező | Az alkalmazás (ügyfél) azonosítója, amely az [Azure Portal – App regisztrációk](https://go.microsoft.com/fwlink/?linkid=2083908) lap az alkalmazáshoz rendelt. | 
| `grant_type` | Kötelező | A beállításnak `password`a beállítására kell beállítható. |
| `username` | Kötelező | A felhasználó e-mail címe. |
| `password` | Kötelező | A felhasználó jelszava. |
| `scope` | Ajánlott | Az alkalmazás által [igényelt, térelválasztott hatókörök](v2-permissions-and-consent.md)vagy engedélyek listája. Egy interaktív folyamat, a rendszergazda vagy a felhasználó nak hozzá kell járulnia, hogy ezeket a hatóköröket előre. |
| `client_secret`| Néha szükséges | Ha az alkalmazás nyilvános ügyfél, `client_secret` `client_assertion` akkor a vagy nem vehető fel.  Ha az alkalmazás bizalmas ügyfél, akkor azt is tartalmaznia kell. | 
| `client_assertion` | Néha szükséges | A tanúsítvány `client_secret`segítségével létrehozott, a alkalmazás egy másik formája.  További részleteket a [tanúsítványhitelesítő](active-directory-certificate-credentials.md) adatok ban talál. | 

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
| `token_type` | Sztring | Mindig a `Bearer`beállítás . |
| `scope` | Szóköz elválasztott karakterláncok | Ha egy hozzáférési jogkivonatot adott vissza, ez a paraméter felsorolja azokat a hatóköröket, amelyekre a hozzáférési jogkivonat érvényes. |
| `expires_in`| int | Azon másodpercek száma, amerre a mellékelt hozzáférési jogkivonat érvényes. |
| `access_token`| Átlátszatlan karakterlánc | A kért [hatókörökhöz](v2-permissions-and-consent.md) kiállítva. |
| `id_token` | Jwt | Akkor van `scope` kiadva, `openid` ha az eredeti paraméter tartalmazza a hatókört. |
| `refresh_token` | Átlátszatlan karakterlánc | Kiadva, `scope` ha `offline_access`az eredeti paraméter is benne van. |

A frissítési jogkivonat segítségével új hozzáférési jogkivonatokat és frissítési jogkivonatokat szerezhet az [OAuth-kód folyamatdokumentációjában](v2-oauth2-auth-code-flow.md#refresh-the-access-token)leírt ugyanazon folyamat használatával.

### <a name="error-response"></a>Hibaválasz

Ha a felhasználó nem adta meg a megfelelő felhasználónevet vagy jelszót, vagy az ügyfél nem kapta meg a kért hozzájárulást, a hitelesítés sikertelen lesz.

| Hiba | Leírás | Ügyfélművelet |
|------ | ----------- | -------------|
| `invalid_grant` | A hitelesítés nem sikerült | A hitelesítő adatok helytelenek voltak, vagy az ügyfél nem rendelkezik a kért hatókörök höz. Ha a hatókörök nem adhatók meg, a `consent_required` rendszer hibát ad vissza. Ebben az esetben az ügyfélnek egy interaktív üzenetre kell küldenie a felhasználót egy webnézet vagy böngésző használatával. |
| `invalid_request` | A kérelmet nem megfelelően építették | A támogatás típusa nem támogatott `/common` `/consumers` a vagy a hitelesítési környezetben.  Használja, `/organizations` vagy egy bérlői azonosító helyett. |

## <a name="learn-more"></a>Részletek

* Próbálja ki a ROPC-t saját maga a [mintakonzol alkalmazássegítségével.](https://github.com/azure-samples/active-directory-dotnetcore-console-up-v2)
* Annak megállapításához, hogy a 2.0-s verziós végpontot kell-e használnia, olvassa el a [Microsoft identity platform korlátait.](active-directory-v2-limitations.md)
