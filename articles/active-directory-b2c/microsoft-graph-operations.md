---
title: Támogatott Microsoft Graph-műveletek
titleSuffix: Azure AD B2C
description: Az Azure AD B2C-erőforrások kezeléséhez támogatott Microsoft Graph-műveletek indexe, beleértve a felhasználókat, a felhasználói folyamatokat, az identitásszolgáltatókat, az egyéni szabályzatokat, a házirendkulcsokat és egyebeket.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78184248"
---
# <a name="microsoft-graph-operations-available-for-azure-ad-b2c"></a>Az Azure AD B2C-hez elérhető Microsoft Graph-műveletek

A következő Microsoft Graph API-műveletek támogatottak az Azure AD B2C-erőforrások kezeléséhez, beleértve a felhasználókat, az identitásszolgáltatókat, a felhasználói folyamatokat, az egyéni szabályzatokat és a házirendkulcsokat.

A következő szakaszokban található hivatkozások a Microsoft Graph API-hivatkozásmegfelelő lapját célozzák meg az adott művelethez.

## <a name="user-management"></a>Felhasználókezelés

- [Felhasználók listázása](https://docs.microsoft.com/graph/api/user-list)
- [Fogyasztói felhasználó létrehozása](https://docs.microsoft.com/graph/api/user-post-users)
- [Felhasználó beszerezni](https://docs.microsoft.com/graph/api/user-get)
- [Felhasználó frissítése](https://docs.microsoft.com/graph/api/user-update)
- [Felhasználó törlése](https://docs.microsoft.com/graph/api/user-delete)

Az Azure AD B2C felhasználói fiókok Microsoft Graph API-val való kezeléséről az [Azure AD B2C felhasználói fiókok kezelése a Microsoft Graph programmal](manage-user-accounts-graph-api.md)című témakörben talál további információt.

## <a name="identity-providers-user-flow"></a>Identitásszolgáltatók (felhasználói folyamat)

Az Azure AD B2C-bérlőben a felhasználói folyamatok számára elérhető identitásszolgáltatók kezelése.

- [Az Azure AD B2C-bérlőben regisztrált identitásszolgáltatók listázása](https://docs.microsoft.com/graph/api/identityprovider-list)
- [Identitásszolgáltató létrehozása](https://docs.microsoft.com/graph/api/identityprovider-post-identityproviders)
- [Identitásszolgáltató beszereznie](https://docs.microsoft.com/graph/api/identityprovider-get)
- [Identitásszolgáltató frissítése](https://docs.microsoft.com/graph/api/identityprovider-update)
- [Identitásszolgáltató törlése](https://docs.microsoft.com/graph/api/identityprovider-delete)

## <a name="user-flow"></a>Felhasználói folyamat

Konfigurálja az előre elkészített házirendeket a regisztrációhoz, a bejelentkezéshez, a kombinált regisztrációhoz és a bejelentkezéshez, a jelszó alaphelyzetbe állításához és a profilfrissítéshez.

- [Felhasználói folyamatok listázása](https://docs.microsoft.com/graph/api/identityuserflow-list)
- [Felhasználói folyamat létrehozása](https://docs.microsoft.com/graph/api/identityuserflow-post-userflows)
- [Felhasználói folyamat beszereznie](https://docs.microsoft.com/graph/api/identityuserflow-get)
- [Felhasználói folyamat törlése](https://docs.microsoft.com/graph/api/identityuserflow-delete)

## <a name="custom-policies"></a>Egyéni szabályzatok

A következő műveletek lehetővé teszik az Azure AD B2C megbízhatósági keretrendszer szabályzatok, más néven [egyéni szabályzatok](custom-policy-overview.md)kezelését.

- [A bérlőben konfigurált összes megbízhatósági keretházirend listázása](https://docs.microsoft.com/graph/api/trustframework-list-trustframeworkpolicies)
- [Megbízhatósági keretházirend létrehozása](https://docs.microsoft.com/graph/api/trustframework-post-trustframeworkpolicy)
- [Meglévő megbízhatósági keretrendszer-házirend tulajdonságainak olvasása](https://docs.microsoft.com/graph/api/trustframeworkpolicy-get)
- [Megbízhatósági keretházirend frissítése vagy létrehozása.](https://docs.microsoft.com/graph/api/trustframework-put-trustframeworkpolicy)
- [Meglévő megbízhatósági keretházirend törlése](https://docs.microsoft.com/graph/api/trustframeworkpolicy-delete)

## <a name="policy-keys"></a>Házirendkulcsok

Az identitáskezelési élmény keretrendszer tárolja a hivatkozott titkos kulcsokat egy egyéni szabályzat ban az összetevők közötti bizalmi kapcsolat létrehozásához. Ezek a titkos kulcsok lehetnek szimmetrikus vagy aszimmetrikus kulcsok/értékek. Az Azure Portalon ezek az entitások **házirendkulcsokként**jelennek meg.

A Microsoft Graph API házirendkulcsainak legfelső szintű erőforrása a [Megbízható keretrendszer iszatkészlete.](https://docs.microsoft.com/graph/api/resources/trustframeworkkeyset) Minden **kulcskészlet** legalább egy **kulcsot**tartalmaz. Kulcs létrehozásához először hozzon létre egy üres kulcskészletet, majd hozzon létre egy kulcsot a kulcskészletben. Manuális titkos kulcsot hozhat létre, feltölthet egy tanúsítványt vagy egy PKCS12 kulcsot. A kulcs lehet egy létrehozott titkos kulcs, egy ön által definiált karakterlánc (például a Facebook-alkalmazás titkoskulcsa) vagy egy feltöltött tanúsítvány. Ha egy kulcskészlet több billentyűvel rendelkezik, csak az egyik kulcs aktív.

### <a name="trust-framework-policy-keyset"></a>Megbízhatósági keretrendszer házirend-kulcskészlete

- [A megbízhatósági keretrendszer kulcskészletének felsorolása](https://docs.microsoft.com/graph/api/trustframework-list-keysets)
- [Megbízhatósági keretrendszer kulcskészletének létrehozása](https://docs.microsoft.com/graph/api/trustframework-post-keysets)
- [Kulcskészlet beszerzése](https://docs.microsoft.com/graph/api/trustframeworkkeyset-get)
- [Megbízhatósági keretrendszer kulcskészletének frissítése](https://docs.microsoft.com/graph/api/trustframeworkkeyset-update)
- [Megbízhatósági keretrendszer kulcskészletének törlése](https://docs.microsoft.com/graph/api/trustframeworkkeyset-delete)

### <a name="trust-framework-policy-key"></a>A Megbízhatósági keret szakpolitikai kulcsa

- [A kulcskészlet aktuális aktív kulcsának beszerzése](https://docs.microsoft.com/graph/api/trustframeworkkeyset-getactivekey)
- [Kulcs létrehozása a kulcskészletben](https://docs.microsoft.com/graph/api/trustframeworkkeyset-generatekey)
- [Karakterlánc-alapú titkos tartalom feltöltése](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadsecret)
- [X.509-es tanúsítvány feltöltése](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadcertificate)
- [PKCS12 formátumú tanúsítvány feltöltése](https://docs.microsoft.com/graph/api/trustframeworkkeyset-uploadpkcs12)

## <a name="applications"></a>Alkalmazások

- [Alkalmazások listázása](https://docs.microsoft.com/graph/api/application-list)
- [Alkalmazás létrehozása](https://docs.microsoft.com/graph/api/resources/application)
- [Az alkalmazás frissítése](https://docs.microsoft.com/graph/api/application-update)
- [ServicePrincipal létrehozása](https://docs.microsoft.com/graph/api/resources/serviceprincipal)
- [Oauth2Permission Grant létrehozása](https://docs.microsoft.com/graph/api/resources/oauth2permissiongrant)
- [Alkalmazás törlése](https://docs.microsoft.com/graph/api/application-delete)

## <a name="application-extension-properties"></a>Alkalmazásbővítmény tulajdonságai

- [Bővítmény tulajdonságainak listázása](https://docs.microsoft.com/graph/api/application-list-extensionproperty)

Az Azure AD B2C egy könyvtárat biztosít, amely felhasználónként 100 egyéni attribútumot képes tárolni. Felhasználói folyamatok esetén ezeket a bővítménytulajdonságokat [az Azure Portal on keresztül kezeli.](custom-policy-custom-attributes.md) Egyéni szabályzatok, az Azure AD B2C hozza létre a tulajdonságot az Első alkalommal, amikor a szabályzat értéket ír a bővítmény tulajdonság.

## <a name="audit-logs"></a>Naplók

- [Naplónaplók listázása](https://docs.microsoft.com/graph/api/directoryaudit-list)

Az Azure AD B2C naplózási naplóinak microsoft graph API-val való eléréséről az [Azure AD B2C naplózási naplóinak elérése](view-audit-logs.md)című témakörben talál további információt.
