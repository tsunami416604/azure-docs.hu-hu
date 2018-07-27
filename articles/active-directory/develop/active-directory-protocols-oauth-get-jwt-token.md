---
title: Az Azure AD-hitelesítés és a egy JWT jogkivonat beszerzése az OAuth 2.0 használatával
description: Hitelesítés az Azure Active Directory, az OAuth 2.0 használatával biztonságos webalkalmazásokat és webes API-kat a szervezeten belüli bemutató példa kód.
services: active-directory
author: rloutlaw
manager: angerobe
editor: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/12/2018
ms.author: routlaw
ms.custom: aaddev
ms.openlocfilehash: cbc86eb6d13034fb3154ed8e9d021064f1d15ece
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39265656"
---
# <a name="request-an-access-token-using-oauth-20-to-access-web-apis-and-applications-secured-by-azure-active-directory"></a>OAuth 2.0 használatával hozzáférés webes API-kat és alkalmazásokat az Azure Active Directory által biztosított hozzáférési jogkivonat kérése

Ez a cikk bemutatja, hogyan tehet szert egy JSON webes jogkivonat (JWT) az Azure AD által védett erőforrások eléréséhez. Feltételezi, hogy rendelkezik egy [engedélyezési jogkivonat](/azure/active-directory/develop/active-directory-protocols-oauth-code#request-an-authorization-code) felhasználó kapott engedélyt vagy keresztül egy [szolgáltatásnév](/azure/active-directory/develop/active-directory-application-objects).

## <a name="build-the-request"></a>A kérelem létrehozása

Használja a következő `POST` HTTP-kérelem beolvasása a jwt-t egy adott alkalmazás vagy API Azure AD által védett eléréséhez.

```http
POST https://{tenant}/oauth2/v2.0/token?client_id={client-id}
&scope={scope}
&code=OAAABAAAAiL9Kn2Z27UubvWFPbm0gLWQJVzCTE9UkP3pSx1aXxUjq3n8b2JRLk4OxVXr...
&redirect_uri=http%3A%2F%2Flocalhost%2Fmyapp%2F
&grant_type=authorization_code
&client_secret=JqQX2PNo9bpM0uEihUPzyrh   
```

### <a name="request-headers"></a>Kérelemfejlécek

A következő fejléceket szükség:

|Kérelem fejléce|Leírás|  
|--------------------|-----------------|  
| *Gazdagép:* | https://login.microsoftonline.com |
| *A Content-Type:*| Application/x-www-form-urlencoded |
 

### <a name="uri-parameters"></a>URI-paraméterek

| Paraméter     |                       | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                |
|---------------|-----------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| bérlő        | szükséges              | A `{tenant}` szabályozza, ki az alkalmazás be tud jelentkezni az értéket a kérelem elérési használható. Az engedélyezett értékek a következők `common`, `organizations`, `consumers`, és a bérlői azonosító. További részletekért lásd: [protokoll alapvető](active-directory-v2-protocols.md#endpoints).                                                                                                                                                   |
| client_id     | szükséges              | Az alkalmazás AZONOSÍTÓJÁT, amely a regisztrációs portál ([apps.dev.microsoft.com](https://apps.dev.microsoft.com/?referrer=https://azure.microsoft.com/documentation/articles&deeplink=/appList)) az alkalmazás hozzárendelve.                                                                                                                                                                                                                             |
| grant_type    | szükséges              | Meg kell `authorization_code` az engedélyezési kód folyamata.                                                                                                                                                                                                                                                                                                                                                                            |
| scope         | szükséges              | Hatókörök szóközzel elválasztott listáját. A hatókörök, ez a szakasz a kért kell lennie, egyenértékű a, vagy egy részét a hatókörök hívásában `/authorize`. Ha az ebben a kérelemben megadott hatókörök span több erőforrás-kiszolgáló, akkor a v2.0-végpont vissza az első hatókörében megadott erőforrás-jogkivonat. A hatókörök részletes magyarázatát, tekintse meg [engedélyek, beleegyezése és hatókörök](active-directory-v2-scopes.md). |
| Kód          | szükséges              | A hívás beszerzett authorization_code `/authorize`                                                                                                                                                                                                                                                                                                                                                                |
| redirect_uri  | szükséges              | Az azonos redirect_uri használt értékkel beszerezni a authorization_code.                                                                                                                                                                                                                                                                                                                                                             |
| client_secret | a web apps szükséges | Az alkalmazás titkos, amelyet az alkalmazás az alkalmazás regisztrációs portálon létrehozott. Ne használjon egy natív alkalmazást, mert client_secrets megbízhatóan nem tárolható az eszközökön. Web apps és a webes API-kat, amelynek a titkos ügyfélkódot tárolja biztonságos helyen a kiszolgálói oldalon lehetősége.  Ügyfél titkos kódok kell URL-kódolású elküldése előtt.                                                                                 |
| code_verifier | választható              | Az azonos code_verifier a authorization_code beszerzéséhez használt. Szükséges, ha az engedélyezési kód engedélyezési kérésben PKCE használt. További információkért lásd: a [PKCE RFC](https://tools.ietf.org/html/rfc7636)                                                                                                                                                                                                                                                                                             |
## <a name="handle-the-response"></a>A válasz kezeléséhez

Token sikeres válasz tartalmazni fogja a JWT jogkivonat és fog kinézni:

```json
{
    "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6Ik5HVEZ2ZEstZnl0aEV1Q...",
    "token_type": "Bearer",
    "expires_in": 3599,
    "scope": "https%3A%2F%2Fgraph.microsoft.com%2Fmail.read",
    "refresh_token": "AwABAAAAvPM1KaPlrEqdFSBzjqfTGAMxZGUTdM0t4B4...",
    "id_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJub25lIn0.eyJhdWQiOiIyZDRkMTFhMi1mODE0LTQ2YTctOD...",
}
```
| Paraméter     | Leírás                                                                                                                                                                                                                                                                                                                                                                                                                                              |
|---------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| access_token  | A kért hozzáférési jogkivonatot. Az alkalmazás a jogkivonat használatával hitelesítik magukat a védett erőforráshoz, például a webes API-t.                                                                                                                                                                                                                                                                                                                                    |
| token_type    | Typ tokenu értékét jelöli. Az egyetlen típus, amely támogatja az Azure ad-ben tulajdonosi                                                                                                                                                                                                                                                                                                                                                                           |
| expires_in    | Mennyi ideig a hozzáférési jogkivonat érvénytelen (másodpercben).                                                                                                                                                                                                                                                                                                                                                                                                       |
| scope         | A hatókörök, amely a access_token érvényes.                                                                                                                                                                                                                                                                                                                                                                                                         |
| refresh_token | Az OAuth 2.0-s frissítési jogkivonatot. Az alkalmazás használhatja ezt a jogkivonatot a jelenlegi hozzáférési jogkivonat lejárata után szerzi be a további hozzáférési jogkivonatokat. Refresh_tokens hosszú élettartamú, és az erőforrásokhoz való hozzáférés megőrzése hosszabb ideig is használható. További részletekért tekintse meg a [v2.0 jogkivonat referenciái](active-directory-v2-tokens.md). <br> **Megjegyzés:** csak a megadott if `offline_access` a kért hatókörhöz.                                               |
| id_token      | Az előjel nélküli JSON webes jogkivonat (JWT). Az alkalmazásnak a bejelentkezett felhasználóval kapcsolatos információkat a token a szegmensek is dekódol. Az alkalmazás gyorsítótárazzák az értékeket, és megjelenítheti őket, de azt nem igazolható azokat bármilyen engedélyezési és biztonsági határokat. Id_tokens kapcsolatos további információkért lásd: a [v2.0-végpont jogkivonat referenciái](active-directory-v2-tokens.md). <br> **Megjegyzés:** csak a megadott if `openid` a kért hatókörhöz. |



