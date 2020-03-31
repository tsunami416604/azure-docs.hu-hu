---
title: Python ADAL az MSAL áttelepítési útmutató | Azure
description: Ismerje meg, hogyan telepítheti át az Azure Active Directory hitelesítési könyvtár (ADAL) Python-alkalmazást a Microsoft Authentication Library (MSAL) for Python alkalmazásba.
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
ms.openlocfilehash: fe9dc6c04fe033fd518218d1b5ea971e573405fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76696557"
---
# <a name="adal-to-msal-migration-guide-for-python"></a>ADAL a MSAL áttelepítési útmutató python

Ez a cikk kiemeli azokat a módosításokat, amelyeket az Azure Active Directory hitelesítési könyvtár (ADAL) a Microsoft authentication library (MSAL) használatához használó alkalmazás áttelepítéséhez szükséges módosításokat kell végrehajtania.

## <a name="difference-highlights"></a>Különbség kiemeli

Az ADAL az Azure Active Directory (Azure AD) 1.0-s végponttal működik. A Microsoft authentication library (MSAL) együttműködik a Microsoft identitás platform - korábbi nevén az Azure Active Directory v2.0 végpont. A Microsoft-identitásplatform annyiban különbözik az Azure AD 1.0-s verziójától:

Támogatja:
  - Munkahelyi és iskolai fiókok (Azure AD-vel kiépített fiókok)
  - Személyes fiókok (például Outlook.com vagy Hotmail.com)
  - Azok az ügyfelek, akik saját e-mail-címüket vagy közösségi identitásukat (például LinkedIn, Facebook, Google) hozzák az Azure AD B2C ajánlaton keresztül

- A szabványok kompatibilisek-e a következőkkel:
  - OAuth v2.0
  - OpenID Connect (OIDC)

További részletekért olvassa el [a Microsoft identity platform (2.0-s verzió) végpontját.](https://docs.microsoft.com/azure/active-directory/develop/azure-ad-endpoint-comparison)

### <a name="scopes-not-resources"></a>Hatókörök nem erőforrások

Az ADAL Python jogkivonatokat szerez be az erőforrásokhoz, de az MSAL Python beszerzi a hatókörök tokenjeit. Az MSAL Python API-felülete már nem rendelkezik erőforrás-paraméterrel. Meg kell adnia a hatóköröket a kívánt engedélyeket és erőforrásokat deklaráló karakterláncok listájaként. A hatókörök néhány példáját a [Microsoft Graph hatóköreinek című témakörben láthatja.](https://docs.microsoft.com/graph/permissions-reference)

### <a name="error-handling"></a>Hibakezelés

Az Azure Active Directory hitelesítési könyvtár (ADAL) a Python használja a kivételt `AdalError` annak jelzésére, hogy volt egy probléma. A Python msal-ja általában hibakódokat használ. További információ: [MSAL for Python error handling](https://docs.microsoft.com/azure/active-directory/develop/msal-handling-exceptions?tabs=python).

### <a name="api-changes"></a>API-módosítások

Az alábbi táblázat egy API-t sorol fel az ADAL for Pythonban, és azt, amelyet az MSAL for Python ban kell használni:

| ADAL Python API-hoz  | MSAL Python API-hoz |
| ------------------- | ---------------------------------- |
| [AuthenticationContext (Hitelesítési környezet)](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext)  | [PublicClientApplication vagy ConfidentialClientApplication](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.__init__)  |
| N/A  | [get_authorization_request_url()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.get_authorization_request_url)  |
| [acquire_token_with_authorization_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_authorization_code) | [acquire_token_by_authorization_code()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_by_authorization_code) |
| [acquire_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token) | [acquire_token_silent()](https://msal-python.readthedocs.io/en/latest/#msal.ClientApplication.acquire_token_silent) |
| [acquire_token_with_refresh_token()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_refresh_token) | N/A |
| [acquire_user_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_user_code) | [initiate_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.initiate_device_flow) |
| [acquire_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_device_code) és [cancel_request_to_get_token_with_device_code()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.cancel_request_to_get_token_with_device_code) | [acquire_token_by_device_flow()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_device_flow) |
| [acquire_token_with_username_password()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_username_password) | [acquire_token_by_username_password()](https://msal-python.readthedocs.io/en/latest/#msal.PublicClientApplication.acquire_token_by_username_password) |
| [acquire_token_with_client_credentials()](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_credentials) és [acquire_token_with_client_certificate](https://adal-python.readthedocs.io/en/latest/#adal.AuthenticationContext.acquire_token_with_client_certificate) | [acquire_token_for_client()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_for_client) |
| N/A | [acquire_token_on_behalf_of()](https://msal-python.readthedocs.io/en/latest/#msal.ConfidentialClientApplication.acquire_token_on_behalf_of) |
| [TokenGyorsítótár()](https://adal-python.readthedocs.io/en/latest/#adal.TokenCache) | [SerializableTokenCache()](https://msal-python.readthedocs.io/en/latest/#msal.SerializableTokenCache) |
| N/A | Az [MSAL-bővítményekből](https://github.com/marstr/original-microsoft-authentication-extensions-for-python) elérhető adatmegőrzéssel rendelkező gyorsítótár |

## <a name="migrate-existing-refresh-tokens-for-msal-python"></a>Meglévő frissítési jogkivonatok áttelepítése az MSAL Python számára

A Microsoft hitelesítési könyvtár (MSAL) kivonatolja a frissítési jogkivonatok fogalmát. Az MSAL Python alapértelmezés szerint egy memórián belüli jogkivonat-gyorsítótárat biztosít, így nem kell tárolnia, keresnie vagy frissítenie a frissítési jogkivonatokat. A felhasználók kevesebb bejelentkezési kérdést is látni fognak, mivel a frissítési jogkivonatok általában felhasználói beavatkozás nélkül frissíthetők. A jogkivonat-gyorsítótárról további információt az [Egyéni tokengyorsítótár-szerializálás az MSAL for Python ban című témakörben talál.](msal-python-token-cache-serialization.md)

A következő kód segít áttelepíteni a frissítési jogkivonatokat egy másik OAuth2 könyvtár által felügyelt (beleértve, de nem kizárólagosan az ADAL Python) által kezelt MSAL python. A frissítési jogkivonatok áttelepítésének egyik oka annak, hogy megakadályozza a meglévő felhasználók bejelentkezését, amikor áttelepíti az alkalmazást az MSAL for Python alkalmazásba.

A frissítési jogkivonat áttelepítésének módja az MSAL használata a Python számára egy új hozzáférési jogkivonat beszerzéséhez az előző frissítési jogkivonat használatával. Az új frissítési jogkivonat visszaadásakor az MSAL python-hoz tárolja azt a gyorsítótárban. Íme egy példa, hogyan kell csinálni:

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

További információ: [1.0 és 2.0-s összehasonlítás.](active-directory-v2-compare.md)
