---
title: Támogatott Microsoft Graph műveletek
titleSuffix: Azure AD B2C
description: Az Azure AD B2C erőforrások, például a felhasználók, a felhasználói folyamatok, az identitás-szolgáltatók, az egyéni házirendek, a szabályzati kulcsok és egyéb házirendek kezeléséhez támogatott Microsoft Graph-műveletek indexe.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/15/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 9db46d13c9a798204958a7c295df9cca169fc08f
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2020
ms.locfileid: "94954035"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>A Azure AD B2C számára elérhető Microsoft Graph műveletek

A következő Microsoft Graph API-műveletek támogatottak Azure AD B2C erőforrások, például felhasználók, identitás-szolgáltatók, felhasználói folyamatok, egyéni házirendek és házirend-kulcsok kezeléséhez.

A következő részben található hivatkozások a Microsoft Graph API-referencián belüli megfelelő lapot célozzák meg az adott művelethez.

## <a name="user-management"></a>Felhasználókezelés

- [Felhasználók listázása](/graph/api/user-list)
- [Fogyasztói felhasználó létrehozása](/graph/api/user-post-users)
- [Felhasználó beolvasása](/graph/api/user-get)
- [Felhasználó frissítése](/graph/api/user-update)
- [Felhasználó törlése](/graph/api/user-delete)

Azure AD B2C felhasználói fiókok Microsoft Graph API-val való kezelésével kapcsolatos további információkért lásd: [Azure ad B2C felhasználói fiókok kezelése Microsoft Graphokkal](manage-user-accounts-graph-api.md).

## <a name="user-phone-number-management"></a>Felhasználói telefonszámok kezelése

- [Hozzáadás](https://docs.microsoft.com/graph/api/authentication-post-phonemethods)
- [Get](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-get)
- [Frissítés](https://docs.microsoft.com/graph/api/b2cauthenticationmethodspolicy-update)
- [Törlés](https://docs.microsoft.com/graph/api/phoneauthenticationmethod-delete)

A felhasználó bejelentkezési telefonszámának Microsoft Graph API-val való kezelésével kapcsolatos további információkért lásd a [B2C hitelesítési módszereit](https://docs.microsoft.com/graph/api/resources/b2cauthenticationmethodspolicy)ismertető témakört.

## <a name="identity-providers-user-flow"></a>Identitás-szolgáltatók (felhasználói folyamat)

Kezelheti az Azure AD B2C bérlője felhasználói folyamatai számára elérhető identitás-szolgáltatókat.

- [Az Azure AD B2C bérlőben regisztrált identitás-szolgáltatók listázása](/graph/api/identityprovider-list)
- [Identitás-szolgáltató létrehozása](/graph/api/identityprovider-post-identityproviders)
- [Identitás-szolgáltató beszerzése](/graph/api/identityprovider-get)
- [Identitás-szolgáltató frissítése](/graph/api/identityprovider-update)
- [Identitás-szolgáltató törlése](/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Felhasználói folyamat

A regisztrációhoz, a bejelentkezéshez, a kombinált regisztrációhoz, a bejelentkezéshez, a jelszó-visszaállításhoz és a profil frissítéséhez szükséges előre elkészített szabályzatok konfigurálása.

- [Felhasználói folyamatok listázása](/graph/api/identityuserflow-list)
- [Felhasználói folyamat létrehozása](/graph/api/identityuserflow-post-userflows)
- [Felhasználói folyamat beszerzése](/graph/api/identityuserflow-get)
- [Felhasználói folyamat törlése](/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Egyéni szabályzatok

A következő műveletek lehetővé teszik a Azure AD B2C megbízhatósági keretrendszer házirendjeinek, más néven [Egyéni házirendeknek](custom-policy-overview.md)a kezelését.

- [A bérlőben konfigurált összes megbízhatósági keretrendszer szabályzatának listázása](/graph/api/trustframework-list-trustframeworkpolicies)
- [Megbízhatósági keretrendszer házirendjének létrehozása](/graph/api/trustframework-post-trustframeworkpolicy)
- [Meglévő megbízhatósági keretrendszer házirendjének tulajdonságainak olvasása](/graph/api/trustframeworkpolicy-get)
- [Frissítse vagy hozza létre a megbízhatósági keretrendszer szabályzatát.](/graph/api/trustframework-put-trustframeworkpolicy)
- [Meglévő megbízhatósági keretrendszer házirendjének törlése](/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Szabályzatkulcsok

Az Identity Experience Framework egy egyéni szabályzatban hivatkozott titkokat tárolja az összetevők közötti megbízhatósági kapcsolat létrehozásához. Ezek a titkok lehetnek szimmetrikus vagy aszimmetrikus kulcsok/értékek. A Azure Portalban ezek az entitások **házirend kulcsaként** jelennek meg.

A Microsoft Graph API legfelső szintű erőforrása a [megbízható keretrendszer kulcskészlet](/graph/api/resources/trustframeworkkeyset). Mindegyik **kulcskészlet** legalább egy **kulcsot** tartalmaz. A kulcs létrehozásához először létre kell hoznia egy üres kulcskészlet-t, majd létre kell hoznia egy kulcsot a kulcskészlet alkalmazásban. Létrehozhat egy manuális titkot, feltölthet egy tanúsítványt vagy egy PKCS12/pfx-profil kulcsot is. A kulcs lehet egy generált titok, egy definiált karakterlánc (például a Facebook-alkalmazás titka) vagy egy feltöltött tanúsítvány. Ha egy kulcskészlet több kulccsal rendelkezik, akkor csak az egyik kulcs aktív.

### <a name="trust-framework-policy-keyset"></a>Megbízhatósági keretrendszer szabályzata kulcskészlet

- [A megbízhatósági keretrendszer alapbeállításainak listázása](/graph/api/trustframework-list-keysets)
- [Megbízhatósági keretrendszer alapbeállításainak létrehozása](/graph/api/trustframework-post-keysets)
- [Kulcskészlet beszerzése](/graph/api/trustframeworkkeyset-get)
- [Megbízhatósági keretrendszer alapbeállításainak frissítése](/graph/api/trustframeworkkeyset-update)
- [Megbízhatósági keretrendszer alapbeállításainak törlése](/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Megbízhatósági keretrendszer házirendjének kulcsa

- [Jelenleg aktív kulcs beolvasása a kulcskészlet-ban](/graph/api/trustframeworkkeyset-getactivekey)
- [Kulcs létrehozása a kulcskészlet-ban](/graph/api/trustframeworkkeyset-generatekey)
- [Karakterlánc-alapú titkos kód feltöltése](/graph/api/trustframeworkkeyset-uploadsecret)
- [X. 509 tanúsítvány feltöltése](/graph/api/trustframeworkkeyset-uploadcertificate)
- [PKCS12/pfx-profil-formátumú tanúsítvány feltöltése](/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Alkalmazások

- [Alkalmazások listázása](/graph/api/application-list)
- [Alkalmazás létrehozása](/graph/api/resources/application)
- [Az alkalmazás frissítése](/graph/api/application-update)
- [ServicePrincipal létrehozása](/graph/api/resources/serviceprincipal)
- [Oauth2Permission-támogatás létrehozása](/graph/api/resources/oauth2permissiongrant)
- [Alkalmazás törlése](/graph/api/application-delete)

## <a name="application-extension-properties"></a>Alkalmazás-bővítmény tulajdonságai

- [Bővítmény tulajdonságainak listázása](/graph/api/application-list-extensionproperty)

Azure AD B2C egy olyan könyvtárat biztosít, amely felhasználónként 100 egyéni attribútumot tud tárolni. A felhasználói folyamatok esetében ezek a bővítmény-tulajdonságok [a Azure Portal használatával kezelhetők](custom-policy-custom-attributes.md). Egyéni házirendek esetén a Azure AD B2C létrehozza a tulajdonságot, amikor a házirend első alkalommal ír egy értéket a bővítmény tulajdonságba.

## <a name="audit-logs"></a>Auditnaplók

- [Naplófájlok listázása](/graph/api/directoryaudit-list)

A Microsoft Graph API-val Azure AD B2C naplók elérésével kapcsolatos további információkért lásd: [Azure ad B2C naplók elérése](view-audit-logs.md).