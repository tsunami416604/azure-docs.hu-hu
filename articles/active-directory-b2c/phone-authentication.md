---
title: Telefonos regisztráció és bejelentkezés egyéni szabályzatokkal
titleSuffix: Azure AD B2C
description: Megtudhatja, hogyan küldhet egyszeri jelszavakat szöveges üzenetekben az alkalmazás felhasználói telefonjára egyéni szabályzatokkal Azure Active Directory B2Cban.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/17/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 96bf088e6db29817d1ae4276c254a84723e5d03d
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480189"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c"></a>A telefonos regisztráció és a bejelentkezés beállítása egyéni szabályzatokkal Azure AD B2C

A telefonos regisztráció és bejelentkezés Azure Active Directory B2C (Azure AD B2C) lehetővé teszi a felhasználók számára, hogy egy szöveges üzenetben SMS-ben küldött egyszeri jelszó (OTP) használatával regisztráljanak és jelentkezzenek be az alkalmazásaiba. Az egyszeri jelszavak segítségével csökkentheti a felhasználók felejtésének kockázatát, vagy megsérült a jelszavuk.

A cikk lépéseit követve az egyéni szabályzatok használatával engedélyezheti ügyfelei számára, hogy regisztráljanak, és bejelentkezzenek az alkalmazásaiba a telefonjára eljuttatott egyszeri jelszó használatával.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="prerequisites"></a>Előfeltételek

* [Azure AD B2C bérlő](tutorial-create-tenant.md)
* A bérlőben [regisztrált webalkalmazás](tutorial-register-applications.md)
* A bérlőre feltöltött [Egyéni szabályzatok](active-directory-b2c-get-started-custom.md)

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>Telefonos regisztráció & bejelentkezési indító csomag beszerzése

Először frissítse a telefonos regisztrációt és a bejelentkezési egyéni házirend-fájlokat a Azure AD B2C Bérlővel való együttműködéshez.

A következő lépések azt feltételezik, hogy végrehajtotta az [előfeltételeket](#prerequisites) , és már klónozotta az [egyéni házirend-indító csomag][starter-pack] tárházát a helyi gépre.

1. Keresse meg a [telefonos regisztrációs és bejelentkezési egyéni házirend-fájlokat][starter-pack-phone] az alapszintű csomag tárházának helyi klónjában, vagy töltse le őket közvetlenül. Az XML-házirend fájljai a következő könyvtárban találhatók:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. Minden fájlban cserélje le a `yourtenant` karakterláncot a Azure AD B2C bérlő nevére. Ha például a B2C-bérlő neve *contosob2c*, a `yourtenant.onmicrosoft.com` összes példánya `contosob2c.onmicrosoft.com`válik.

1. Hajtsa végre az [alkalmazás-azonosítók hozzáadása az egyéni házirendhez](active-directory-b2c-get-started-custom.md#add-application-ids-to-the-custom-policy) című szakasz lépéseit az [Egyéni szabályzatok beszerzése Azure Active Directory B2Cban](active-directory-b2c-get-started-custom.md)című témakörben. Ebben az esetben frissítse `/phone-number-passwordless/` **`Phone_Email_Base.xml`** az előfeltételek, a *IdentityExperienceFramework* és a *ProxyIdentityExperienceFramework*végrehajtásakor regisztrált két alkalmazás **alkalmazás-(ügyfél-) azonosítóját** .

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

Az egyes fájlok feltöltésekor az Azure hozzáadja a `B2C_1A_`előtagot.

## <a name="test-the-custom-policy"></a>Egyéni szabályzat tesztelése

1. Az **Egyéni házirendek**területen válassza a **B2C_1A_SignUpOrSignInWithPhone**lehetőséget.
1. Az **alkalmazás kiválasztása**területen válassza ki azt a *webapp1* -alkalmazást, amelyet az előfeltételek végrehajtásakor regisztrált.
1. A **Válasz URL-címének kiválasztása lapon**válassza a `https://jwt.ms`lehetőséget.
1. Válassza a **Futtatás most** lehetőséget, és regisztráljon e-mail-cím vagy telefonszám használatával.
1. Kattintson ismét a **Futtatás** gombra, és jelentkezzen be ugyanazzal a fiókkal, és ellenőrizze, hogy megfelelő-e a konfigurációja.

## <a name="next-steps"></a>Következő lépések

Megtalálhatja a telefonos regisztrációt és az egyéni házirend-előfizetési csomagot (és más kezdő csomagokat is) a GitHubon:

[Azure-Samples/Active-Directory-B2C-Custom-Policy-starterpack/forgatókönyvek/telefon-szám-jelszó][starter-pack-phone]

Az alapszintű csomag házirend-fájljai a multi-Factor Authentication technikai profilokat és a telefonszám-adatfeldolgozási jogcímeket használják:

* [Azure Multi-Factor Authentication technikai profil megadása](multi-factor-auth-technical-profile.md)
* [Telefonszám-jogcímek átalakításának meghatározása](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
