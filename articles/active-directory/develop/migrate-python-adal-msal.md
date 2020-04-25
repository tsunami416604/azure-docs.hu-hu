---
title: Python-ADAL a MSAL áttelepítési útmutatóhoz | Azure
description: Ismerje meg, hogyan telepítheti át a Azure Active Directory Authentication Library (ADAL) Python-alkalmazást a Pythonhoz készült Microsoft Authentication Library (MSAL) szolgáltatásba.
services: active-directory
titleSuffix: Microsoft identity platform
author: rayluo
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.tgt_pltfrm: Python
ms.workload: identity
ms.date: 11/11/2019
ms.author: rayluo
ms.reviewer: rayluo, nacanuma, twhitney
ms.custom: aaddev
ms.openlocfilehash: a3f95383979fd47b3baaec946f724533461729b8
ms.sourcegitcommit: edccc241bc40b8b08f009baf29a5580bf53e220c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/24/2020
ms.locfileid: "82128050"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>ADAL a MSAL áttelepítési útmutatója a Pythonhoz

Ez a cikk azokat a módosításokat mutatja be, amelyeket a Azure Active Directory hitelesítési függvénytárat (ADAL) használó alkalmazások áttelepíteni kell a Microsoft Authentication Library (MSAL) használatára.

## <a name="difference-highlights"></a>Különbségi csúcsfények

A ADAL együttműködik a Azure Active Directory (Azure AD) 1.0-s verziójának végpontján. A Microsoft Authentication Library (MSAL) a Microsoft Identity platformmal működik – korábbi nevén Azure Active Directory v 2.0-végpont. A Microsoft Identity platform különbözik az Azure AD 1.0-s verziótól:

Támogatja
  - Munkahelyi és iskolai fiókok (Azure AD-kiépített fiókok)
  - Személyes fiókok (például Outlook.com vagy Hotmail.com)
  - A saját e-mail-címét vagy közösségi identitását (például LinkedIn, Facebook, Google) használó ügyfelei az Azure AD B2C ajánlaton keresztül

- A szabványokkal kompatibilis szabványok:
  - OAuth 2.0-s verzió
  - OpenID Connect (OIDC)

További részletekért lásd: [Mi a különbség a Microsoft Identity platform (v 2.0) végpontján?](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison) .

### <a name="scopes-not-resources"></a>Hatókörök nem erőforrásai

A ADAL Python jogkivonatokat vásárol az erőforrásokhoz, de a MSAL Python jogkivonatokat vásárol a hatókörökhöz. A MSAL Python API-felülete már nem rendelkezik erőforrás-paraméterrel. A hatóköröket olyan karakterláncok listájában kell megadnia, amelyek deklarálják a kívánt engedélyeket és a kért erőforrásokat. A hatókörökkel kapcsolatos néhány példa: [Microsoft Graph hatókörei](https://docs.microsoft.com/graph/permissions-reference).

A `/.default` hatókör utótagját hozzáadhatja az erőforráshoz, hogy az alkalmazásokat a 1.0-s verziójú végpontról (ADAL) a Microsoft Identity platform-végpontra (MSAL) telepítse. A (z) erőforrás értékének `https://graph.microsoft.com`megfelelő hatókör értéke például a következő: `https://graph.microsoft.com/.default`.  Ha az erőforrás nem szerepel az URL-címben, de az űrlap `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX`erőforrás-azonosítója, továbbra is használhatja a hatókör értékét. `XXXXXXXX-XXXX-XXXX-XXXXXXXXXXXX/.default`

A különböző típusú hatókörökkel kapcsolatos további részletekért tekintse [meg a Microsoft Identity platform engedélyeit és](https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent) a hozzájuk tartozó jogosultságokat, valamint a webes API-k 1.0-s verzióinak [elfogadására vonatkozó](https://docs.microsoft.com/azure/active-directory/develop/msal-v1-app-scopes) cikkeket.

### <a name="error-handling"></a>Hibakezelés

A Pythonhoz készült Azure Active Directory Authentication Library (ADAL) a `AdalError` kivétel alapján jelzi, hogy probléma merült fel. A MSAL for Python jellemzően hibakódokat használ. További információ: [MSAL for Python hibakezelés](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python).

### <a name="api-changes"></a>API-változások

A következő táblázat a Pythonhoz készült ADAL API-t, a MSAL for Python esetében pedig a helyét tartalmazza.

| ADAL Python API-hoz  | MSAL Python API-hoz |
| ------------------- | ---------------------------------- |
| [AuthenticationContext](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication vagy ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| N/A  | [get_authorization_request_url ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent ()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | N/A |
| [acquire_user_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) és [cancel_request_to_get_token_with_device_code ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password ()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) és [acquire_token_with_client_certificate ()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| N/A | [acquire_token_on_behalf_of ()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenCache ()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| N/A | Gyorsítótár az adatmegőrzéssel, elérhető a [MSAL-bővítmények](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) közül |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Meglévő frissítési tokenek migrálása a MSAL Pythonhoz

A Microsoft Authentication Library (MSAL) elvonta a frissítési tokenek fogalmát. A MSAL Python alapértelmezés szerint a memóriában tárolt jogkivonat-gyorsítótárat biztosít, így nincs szükség a frissítési tokenek tárolására, keresésére vagy frissítésére. A felhasználók kevesebb bejelentkezési kérést is láthatnak, mivel a frissítési tokenek általában felhasználói beavatkozás nélkül frissíthetők. További információ a jogkivonat-gyorsítótárról: [Egyéni jogkivonat-gyorsítótár szerializálása a MSAL-ben a Pythonhoz](msal-python-token-cache-serialization.md).

A következő kód segít áttelepíteni a más OAuth2-függvénytár által kezelt frissítési tokeneket (beleértve a ADAL Pythont is), amelyet a MSAL for Python felügyel. A frissítési tokenek áttelepítésének egyik oka annak megakadályozása, hogy a meglévő felhasználók újra be kelljen jelentkezniük az alkalmazás MSAL for Python-ba való áttelepítésekor.

A frissítési token áttelepítésének módszere a MSAL for Python használata az előző frissítési token használatával új hozzáférési jogkivonat beszerzéséhez. Ha az új frissítési tokent adja vissza, a Python MSAL a gyorsítótárban fogja tárolni. Íme egy példa arra, hogyan teheti meg:

```python
from msal import PublicClientApplication

def get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    raise NotImplementedError("You will need to implement this by yourself")

app = PublicClientApplication(..., token_cache=...)

for old_rt, old_scope in get_preexisting_rt_and_their_scopes_from_elsewhere(...):
    # Assuming the old scope could be a space-delimited string.
    # MSAL expects a list, like ["scope1", "scope2"].
    scopes = old_scope.split()
        # If your old refresh token came from ADAL for Python, which uses a resource rather than a scope,
        # you need to convert your v1 resource into v2 scopes
        # See https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison#scopes-not-resources
        # You may be able to append "/.default" to your v1 resource to form a scope
        # See https://docs.microsoft.com/azure/active-directory/develop/v2-permissions-and-consent#the-default-scope

    result = app.client.obtain_token_by_refresh_token(old_rt, scope=scopes)
    # When this call returns the new token(s), a new refresh token is issued by the Microsoft identity platform and MSAL for Python
    # stores it in the token cache.
```

## <a name="next-steps"></a>További lépések

További információkért tekintse meg a [1.0-s és a 2.0-s verzió összehasonlítását](active-directory-v2-compare.md)ismertető témakört.
