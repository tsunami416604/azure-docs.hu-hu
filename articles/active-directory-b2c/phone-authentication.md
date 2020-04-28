---
title: Telefonos regisztráció és bejelentkezés egyéni szabályzatokkal (előzetes verzió)
titleSuffix: Azure AD B2C
description: Egyszeri jelszó (OTP) küldése szöveges üzenetekben az alkalmazás felhasználói telefonokra egyéni szabályzatokkal Azure Active Directory B2Cban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 02/25/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: eadac0e973b361b1fdee63dcc9cfa848a0b2bacb
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "78183958"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Telefonos regisztráció és bejelentkezés beállítása egyéni szabályzatokkal Azure AD B2Cban (előzetes verzió)

A telefonos regisztráció és bejelentkezés Azure Active Directory B2C (Azure AD B2C) lehetővé teszi a felhasználók számára, hogy egy szöveges üzenetben SMS-ben küldött egyszeri jelszó (OTP) használatával regisztráljanak és jelentkezzenek be az alkalmazásaiba. Az egyszeri jelszavak segítségével csökkentheti a felhasználók felejtésének kockázatát, vagy megsérült a jelszavuk.

A cikk lépéseit követve az egyéni szabályzatok használatával engedélyezheti ügyfelei számára, hogy regisztráljanak, és bejelentkezzenek az alkalmazásaiba a telefonjára eljuttatott egyszeri jelszó használatával.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>Díjszabás

Az egyszeri jelszavakat SMS szöveges üzenetek formájában küldi el a felhasználók számára, és minden egyes elküldött üzenet után díjat számítunk fel. A díjszabással kapcsolatos információkért tekintse meg a [Azure Active Directory B2C díjszabásának](https://azure.microsoft.com/pricing/details/active-directory-b2c/) **külön** díjszabását ismertető szakaszt.

## <a name="prerequisites"></a>Előfeltételek

Az OTP beállítása előtt a következő erőforrásokra van szükség.

* [Azure AD B2C bérlő](tutorial-create-tenant.md)
* A bérlőben [regisztrált webalkalmazás](tutorial-register-applications.md)
* A bérlőre feltöltött [Egyéni szabályzatok](custom-policy-get-started.md)

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Telefonos regisztráció & bejelentkezési indító csomag beszerzése

Először frissítse a telefonos regisztrációt és a bejelentkezési egyéni házirend-fájlokat a Azure AD B2C Bérlővel való együttműködéshez.

A következő lépések azt feltételezik, hogy végrehajtotta az [előfeltételeket](#prerequisites) , és már klónozotta az [egyéni házirend-indító csomag][starter-pack] tárházát a helyi gépre.

1. Keresse meg a [telefonos regisztrációs és bejelentkezési egyéni házirend-fájlokat][starter-pack-phone] az alapszintű csomag tárházának helyi klónjában, vagy töltse le őket közvetlenül. Az XML-házirend fájljai a következő könyvtárban találhatók:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. Minden fájlban cserélje le a karakterláncot `yourtenant` a Azure ad B2C bérlő nevére. Ha például a B2C-bérlő neve *contosob2c*, az összes példánya `yourtenant.onmicrosoft.com` lesz. `contosob2c.onmicrosoft.com`

1. Hajtsa végre az [alkalmazás-azonosítók hozzáadása az egyéni házirendhez](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) című szakasz lépéseit az [Egyéni szabályzatok beszerzése Azure Active Directory B2Cban](custom-policy-get-started.md)című témakörben. Ebben az esetben az előfeltételek `/phone-number-passwordless/` **`Phone_Email_Base.xml`** , a *IdentityExperienceFramework* és a *ProxyIdentityExperienceFramework*végrehajtásakor regisztrált két alkalmazás **alkalmazás-(ügyfél-) azonosítóit** kell frissíteni.

## <a name="upload-the-policy-files"></a>A szabályzat fájljainak feltöltése

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com) , és navigáljon a Azure ad B2C bérlőhöz.
1. A **szabályzatok**területen válassza az **identitási élmény keretrendszere**elemet.
1. Válassza az **egyéni házirend feltöltése**lehetőséget.
1. Töltse fel a házirend-fájlokat a következő sorrendben:
    1. *Phone_Email_Base. XML*
    1. *SignUpOrSignInWithPhone. XML*
    1. *SignUpOrSignInWithPhoneOrEmail. XML*
    1. *ProfileEditPhoneOnly. XML*
    1. *ProfileEditPhoneEmail. XML*
    1. *ChangePhoneNumber. XML*
    1. *PasswordResetEmail. XML*

Az egyes fájlok feltöltésekor az Azure hozzáadja az előtagot `B2C_1A_`.

## <a name="test-the-custom-policy"></a>Egyéni szabályzat tesztelése

1. Az **Egyéni házirendek**területen válassza a **B2C_1A_SignUpOrSignInWithPhone**lehetőséget.
1. Az **alkalmazás kiválasztása**területen válassza ki azt a *webapp1* -alkalmazást, amelyet az előfeltételek végrehajtásakor regisztrált.
1. Válassza a **Válasz URL-cím kiválasztása**lehetőséget `https://jwt.ms`.
1. Válassza a **Futtatás most** lehetőséget, és regisztráljon e-mail-cím vagy telefonszám használatával.
1. Kattintson ismét a **Futtatás** gombra, és jelentkezzen be ugyanazzal a fiókkal, és ellenőrizze, hogy megfelelő-e a konfigurációja.

## <a name="get-user-account-by-phone-number"></a>Felhasználói fiók beolvasása telefonszám alapján

Egy olyan felhasználó, amely regisztrál egy telefonszámot, de nem biztosít helyreállítási e-mail-címet, a rendszer a bejelentkezési nevüknek megfelelő telefonszámon rögzíti a Azure AD B2C könyvtárban. Ha a felhasználó ezután módosítani szeretné a telefonszámát, akkor az ügyfélszolgálatnak vagy a támogatási csapatnak először meg kell keresnie a fiókját, majd frissítenie kell a telefonszámát.

A felhasználó telefonszáma alapján (bejelentkezési név) a [Microsoft Graph](manage-user-accounts-graph-api.md)használatával kereshet:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Például:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>További lépések

Megtalálhatja a telefonos regisztrációt és az egyéni házirend-előfizetési csomagot (és más kezdő csomagokat is) a GitHubon:

[Azure-Samples/Active-Directory-B2C-Custom-Policy-starterpack/forgatókönyvek/telefon-szám-jelszó][starter-pack-phone]

Az alapszintű csomag házirend-fájljai a multi-Factor Authentication technikai profilokat és a telefonszám-adatfeldolgozási jogcímeket használják:

* [Azure Multi-Factor Authentication technikai profil megadása](multi-factor-auth-technical-profile.md)
* [Telefonszám-jogcímek átalakításának meghatározása](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
