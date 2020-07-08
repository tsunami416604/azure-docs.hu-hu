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
ms.date: 02/20/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 27fe1a41365d96a4179f8c659b63dc22c7b9fc93
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "78184248"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>A Azure AD B2C számára elérhető Microsoft Graph műveletek

A következő Microsoft Graph API-műveletek támogatottak Azure AD B2C erőforrások, például felhasználók, identitás-szolgáltatók, felhasználói folyamatok, egyéni házirendek és házirend-kulcsok kezeléséhez.

A következő részben található hivatkozások a Microsoft Graph API-referencián belüli megfelelő lapot célozzák meg az adott művelethez.

## <a name="user-management"></a>Felhasználókezelés

- [Felhasználók listázása](https://docs.microsoft.com/graph/api/user-list)
- [Fogyasztói felhasználó létrehozása](https://docs.microsoft.com/graph/api/user-post-users)
- [Felhasználó beolvasása](https://docs.microsoft.com/graph/api/user-get)
- [Felhasználó frissítése](https://docs.microsoft.com/graph/api/user-update)
- [Felhasználó törlése](https://docs.microsoft.com/graph/api/user-delete)

Azure AD B2C felhasználói fiókok Microsoft Graph API-val való kezelésével kapcsolatos további információkért lásd: [Azure ad B2C felhasználói fiókok kezelése Microsoft Graphokkal](manage-user-accounts-graph-api.md).

## <a name="identity-providers-user-flow"></a>Identitás-szolgáltatók (felhasználói folyamat)

Kezelheti az Azure AD B2C bérlője felhasználói folyamatai számára elérhető identitás-szolgáltatókat.

- [Az Azure AD B2C bérlőben regisztrált identitás-szolgáltatók listázása](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Identitás-szolgáltató létrehozása](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Identitás-szolgáltató beszerzése](https://docs.microsoft.com/graph/api/identityprovider-get)
- [Identitás-szolgáltató frissítése](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Identitás-szolgáltató törlése](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Felhasználói folyamat

A regisztrációhoz, a bejelentkezéshez, a kombinált regisztrációhoz, a bejelentkezéshez, a jelszó-visszaállításhoz és a profil frissítéséhez szükséges előre elkészített szabályzatok konfigurálása.

- [Felhasználói folyamatok listázása](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [Felhasználói folyamat létrehozása](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [Felhasználói folyamat beszerzése](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [Felhasználói folyamat törlése](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Egyéni szabályzatok

A következő műveletek lehetővé teszik a Azure AD B2C megbízhatósági keretrendszer házirendjeinek, más néven [Egyéni házirendeknek](custom-policy-overview.md)a kezelését.

- [A bérlőben konfigurált összes megbízhatósági keretrendszer szabályzatának listázása](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [Megbízhatósági keretrendszer házirendjének létrehozása](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [Meglévő megbízhatósági keretrendszer házirendjének tulajdonságainak olvasása](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [Frissítse vagy hozza létre a megbízhatósági keretrendszer szabályzatát.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [Meglévő megbízhatósági keretrendszer házirendjének törlése](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Szabályzat kulcsai

Az Identity Experience Framework egy egyéni szabályzatban hivatkozott titkokat tárolja az összetevők közötti megbízhatósági kapcsolat létrehozásához. Ezek a titkok lehetnek szimmetrikus vagy aszimmetrikus kulcsok/értékek. A Azure Portalban ezek az entitások **házirend kulcsaként**jelennek meg.

A Microsoft Graph API legfelső szintű erőforrása a [megbízható keretrendszer kulcskészlet](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset). Mindegyik **kulcskészlet** legalább egy **kulcsot**tartalmaz. A kulcs létrehozásához először létre kell hoznia egy üres kulcskészlet-t, majd létre kell hoznia egy kulcsot a kulcskészlet alkalmazásban. Létrehozhat egy manuális titkot, feltölthet egy tanúsítványt vagy egy PKCS12/pfx-profil kulcsot is. A kulcs lehet egy generált titok, egy definiált karakterlánc (például a Facebook-alkalmazás titka) vagy egy feltöltött tanúsítvány. Ha egy kulcskészlet több kulccsal rendelkezik, akkor csak az egyik kulcs aktív.

### <a name="trust-framework-policy-keyset"></a>Megbízhatósági keretrendszer szabályzata kulcskészlet

- [A megbízhatósági keretrendszer alapbeállításainak listázása](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [Megbízhatósági keretrendszer alapbeállításainak létrehozása](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [Kulcskészlet beszerzése](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [Megbízhatósági keretrendszer alapbeállításainak frissítése](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [Megbízhatósági keretrendszer alapbeállításainak törlése](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>Megbízhatósági keretrendszer házirendjének kulcsa

- [Jelenleg aktív kulcs beolvasása a kulcskészlet-ban](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [Kulcs létrehozása a kulcskészlet-ban](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [Karakterlánc-alapú titkos kód feltöltése](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [X. 509 tanúsítvány feltöltése](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [PKCS12/pfx-profil-formátumú tanúsítvány feltöltése](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Alkalmazások

- [Alkalmazások listázása](https://docs.microsoft.com/graph/api/application-list)
- [Alkalmazás létrehozása](https://docs.microsoft.com/graph/api/resources/application)
- [Az alkalmazás frissítése](https://docs.microsoft.com/graph/api/application-update)
- [ServicePrincipal létrehozása](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Oauth2Permission-támogatás létrehozása](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [Alkalmazás törlése](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>Alkalmazás-bővítmény tulajdonságai

- [Bővítmény tulajdonságainak listázása](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Azure AD B2C egy olyan könyvtárat biztosít, amely felhasználónként 100 egyéni attribútumot tud tárolni. A felhasználói folyamatok esetében ezek a bővítmény-tulajdonságok [a Azure Portal használatával kezelhetők](custom-policy-custom-attributes.md). Egyéni házirendek esetén a Azure AD B2C létrehozza a tulajdonságot az első alkalommal, amikor a házirend egy értéket ír a bővítmény tulajdonságba.

## <a name="audit-logs"></a>Auditnaplók

- [Naplófájlok listázása](https://docs.microsoft.com/graph/api/directoryaudit-list)

A Microsoft Graph API-val Azure AD B2C naplók elérésével kapcsolatos további információkért lásd: [Azure ad B2C naplók elérése](view-audit-logs.md).
