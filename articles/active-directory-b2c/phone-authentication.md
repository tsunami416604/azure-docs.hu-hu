---
title: Telefonos regisztráció és bejelentkezés egyéni szabályzatokkal (előzetes verzió)
titleSuffix: Azure AD B2C
description: Az Azure Active Directory B2C egyéni szabályzataival sms-ben egyszeri jelszavakat (OTP) küldhet az alkalmazás felhasználóinak telefonjaira.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78183958"
---
# <a name="set-up-phone-sign-up-and-sign-in-with-custom-policies-in-azure-ad-b2c-preview"></a>Telefonos regisztráció és bejelentkezés beállítása egyéni szabályzatokkal az Azure AD B2C-ben (előzetes verzió)

Telefonos regisztráció és bejelentkezés az Azure Active Directory B2C (Azure AD B2C) lehetővé teszi a felhasználók számára, hogy regisztráljon, és jelentkezzen be az alkalmazások segítségével egy egyszeri jelszó (OTP) küldött szöveges üzenetet a telefonra. Az egyszeri jelszavak minimálisra csökkenthetik annak kockázatát, hogy a felhasználók elfelejtsék vagy felveszélyezzék a jelszavukat.

A cikkben ismertetett lépéseket követve az egyéni házirendek használatával lehetővé teszi az ügyfelek számára, hogy a telefonjukra küldött egyszeri jelszóval regisztráljanak és jelentkezzenek be az alkalmazásokba.

[!INCLUDE [b2c-public-preview-feature](../../includes/active-directory-b2c-public-preview.md)]

## <a name="pricing"></a>Díjszabás

Az egyszeri jelszavakat sms-ben küldik el a felhasználóknak, és előfordulhat, hogy minden egyes elküldött üzenetért díjat kell fizetnie. Az árképzéssel kapcsolatos információkért tekintse meg az [Azure Active Directory B2C díjszabásának](https://azure.microsoft.com/pricing/details/active-directory-b2c/) **külön díjak** című részét.

## <a name="prerequisites"></a>Előfeltételek

Az OTP beállítása előtt a következő erőforrásokra van szükség.

* [Azure AD B2C-bérlő](tutorial-create-tenant.md)
* A bérlőben [regisztrált webalkalmazás](tutorial-register-applications.md)
* A bérlőbe feltöltött [egyéni házirendek](custom-policy-get-started.md)

## <a name="get-the-phone-sign-up--sign-in-starter-pack"></a>A telefonos regisztráció & a bejelentkezési csomag

Kezdje azzal, hogy frissíti a telefonos előfizetést, és egyéni szabályzatfájlokat regisztrál az Azure AD B2C-bérlővel való együttműködésre.

A következő lépések feltételezik, hogy befejezte az [előfeltételeket,](#prerequisites) és már klónozta az [egyéni szabályzat kezdőcsomag-tárházat][starter-pack] a helyi számítógépre.

1. Keresse meg a [telefonos előfizetést és az egyéni házirendfájlokat][starter-pack-phone] az indítócsomag-tártár helyi klónjában, vagy töltse le őket közvetlenül. Az XML-házirendfájlok a következő könyvtárban találhatók:

    `active-directory-b2c-custom-policy-starterpack/scenarios/`**`phone-number-passwordless`**

1. Minden fájlban cserélje `yourtenant` le a karakterláncot az Azure AD B2C-bérlő nevére. Ha például a B2C-bérlő neve *contosob2c,* akkor `yourtenant.onmicrosoft.com` `contosob2c.onmicrosoft.com`a lesz a .

1. Hajtsa végre az [Alkalmazásazonosítók hozzáadása az](custom-policy-get-started.md#add-application-ids-to-the-custom-policy) [Azure Active Directory B2C egyéni szabályzataihoz](custom-policy-get-started.md)tartozó egyéni szabályzatok hoz című szakaszlépéseit. Ebben az esetben `/phone-number-passwordless/` **`Phone_Email_Base.xml`** frissítse a két regisztrált alkalmazás **(ügyfél) azonosítóival** az előfeltételek, az *IdentityExperienceFramework* és a *ProxyIdentityExperienceFramework*.

## <a name="upload-the-policy-files"></a>A házirendfájlok feltöltése

1. Jelentkezzen be az [Azure Portalon,](https://portal.azure.com) és keresse meg az Azure AD B2C-bérlőt.
1. A **Házirendek**csoportban válassza **az Identitáskezelési keretrendszert**.
1. Válassza **az Egyéni házirend feltöltése lehetőséget.**
1. Töltse fel a házirendfájlokat a következő sorrendben:
    1. *Phone_Email_Base.xml fájl*
    1. *SignUpOrSignInWithPhone.xml*
    1. *SignUpOrSignInWithPhoneOrEmail.xml*
    1. *ProfilEditPhoneOnly.xml*
    1. *Profil: Cím: PhoneEmail.xml*
    1. *ChangePhoneNumber.xml*
    1. *PasswordResetEmail.xml*

Az egyes fájlok feltöltésekén `B2C_1A_`az Azure hozzáadja az előtagot.

## <a name="test-the-custom-policy"></a>Az egyéni házirend tesztelése

1. Az **Egyéni házirendek csoportban**válassza **a B2C_1A_SignUpOrSignInWithPhone**lehetőséget.
1. Az **Alkalmazás kiválasztása**csoportban válassza ki azt a *webapp1-alkalmazást,* amelyet az előfeltételek teljesítésekor regisztrált.
1. A Válasz url `https://jwt.ms`kiválasztása **területen**válassza a lehetőséget.
1. Válassza **a Futtatás most** lehetőséget, és regisztráljon egy e-mail címmel vagy telefonszámmal.
1. Válassza a **Futtatás újra** lehetőséget, és jelentkezzen be ugyanazzal a fiókkal, és ellenőrizze, hogy a megfelelő konfigurációval rendelkezik-e.

## <a name="get-user-account-by-phone-number"></a>Felhasználói fiók beszerezni telefonszám szerint

A felhasználó, aki feliratkozik egy telefonszámot, de nem adja meg a helyreállítási e-mail-címet rögzíti az Azure AD B2C címtárban a telefonszámukat, mint a bejelentkezési nevet. Ha a felhasználó ezután módosítani kívánja a telefonszámát, az ügyfélszolgálatnak vagy az ügyfélszolgálatnak először meg kell találnia a fiókját, majd frissítenie kell a telefonszámát.

A felhasználót telefonszámuk (bejelentkezési neve) alapján megtalálhatjuk a [Microsoft Graph](manage-user-accounts-graph-api.md)segítségével:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+{phone number}' and c/issuer eq '{tenant name}.onmicrosoft.com')
```

Példa:

```http
GET https://graph.microsoft.com/v1.0/users?$filter=identities/any(c:c/issuerAssignedId eq '+450334567890' and c/issuer eq 'contosob2c.onmicrosoft.com')
```

## <a name="next-steps"></a>További lépések

A telefonra való feliratkozást és a bejelentkezésegyéni szabályzatkezdőcsomagját (és más kezdőcsomagokat) a GitHubon találja:

[Azure-Samples/active-directory-b2c-custom-policy-starterpack/scenarios/phone-number-passwordless][starter-pack-phone]

Az indítócsomag házirendfájljai többtényezős hitelesítéstechnikai profilokat és telefonszámjogcímek-átalakításokat használnak:

* [Azure többtényezős hitelesítéstechnikai profiljának definiálása](multi-factor-auth-technical-profile.md)
* [Telefonszámjogcím-átalakítások definiálása](phone-number-claims-transformations.md)

<!-- LINKS - External -->
[starter-pack]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack
[starter-pack-phone]: https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/scenarios/phone-number-passwordless
