---
title: A Microsoft identitásplatformján regisztrált alkalmazások eltávolítása | Azure
description: Útmutató a Microsoft identitásplatformján regisztrált alkalmazás eltávolításához.
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/25/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: 916d34a0af1490404cb9f7851b3117f1967c54d0
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51240902"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform-preview"></a>Rövid útmutató: A Microsoft identitásplatformján regisztrált alkalmazások eltávolítása (előzetes verzió)

Az alkalmazásokat a Microsoft identitásplatformján regisztráló nagyvállalati fejlesztőknek és SaaS-szolgáltatóknak (szoftverszolgáltatók) esetenként el kell távolítaniuk az alkalmazás regisztrációját.

Ennek a rövid útmutatónak a segítségével megtanulhatja a következőket:

* [A saját maga vagy a szervezete által fejlesztett alkalmazás eltávolítása](#remove-an-application-authored-by-your-organization)
* [Más szervezet által fejlesztett alkalmazás eltávolítása](#remove-an-application-authoried-by-another-organization)

## <a name="prerequisites"></a>Előfeltételek

Először is győződjön meg arról, hogy az alábbi előfeltételek teljesülnek:

* Rendelkezik egy olyan bérlővel, amelyen vannak alkalmazások regisztrálva. Az alkalmazások hozzáadásával és regisztrálásával kapcsolatban tekintse meg [az alkalmazásoknak a Microsoft identitásplatformján történő regisztrálását](quickstart-register-app.md) ismertető cikket.
* Feliratkozott az előzetes verzióban elérhető alkalmazásregisztrációs szolgáltatásra az Azure Portalon. Az ebben a rövid útmutatóban leírt lépések az új felhasználói felületen alapulnak, és csak akkor használhatók, ha feliratkozott az előzetes verzióra.

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>A saját maga vagy a szervezete által fejlesztett alkalmazás eltávolítása

A saját maga vagy a szervezete által regisztrált alkalmazásokat egy alkalmazás- és egy szolgáltatásnév-objektum is jelöli a bérlőn. További információkért tekintse meg [az alkalmazás- és szolgáltatásnév-objektumokat](active-directory-application-objects.md) ismertető cikket.

### <a name="to-remove-an-application"></a>Alkalmazás eltávolítása

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
2. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
3. A bal oldali navigációs panelen válassza ki az **Azure Active Directory** szolgáltatást, majd válassza az **Alkalmazásregisztrációk (előzetes verzió)** elemet. Keresse meg, és válassza ki a konfigurálni kívánt alkalmazást. Miután kiválasztotta az alkalmazást, megjelenik annak **Áttekintés** lapja.
4. Az **Áttekintés** lapon válassza a **Törlés** elemet.
5. Kattintson az **Igen** gombra az alkalmazás törlésének megerősítéséhez.

  > [!NOTE]
  > Az alkalmazás törléséhez az alkalmazás tulajdonosaként kell szerepelnie, vagy rendszergazdai jogosultságokkal kell rendelkeznie.

## <a name="remove-an-application-authored-by-another-organization"></a>Más szervezet által fejlesztett alkalmazás eltávolítása

Ha az **alkalmazásregisztrációkat** egy bérlő kontextusában tekinti meg, a **Minden alkalmazás** lapon szereplő alkalmazások egy része más bérlőről származik, és a hozzájárulási folyamat során lettek regisztrálva a bérlőjében. Pontosabban ezeket az alkalmazásokat csak egy szolgáltatásnév-objektum jelöli a bérlőben, és nem tartozik hozzájuk alkalmazásobjektum. Az alkalmazás- és szolgáltatásnév-objektumok különbségeivel kapcsolatos további információért tekintse meg [az Azure AD-ben használt alkalmazás- és szolgáltatásnév-objektumokkal](active-directory-application-objects.md) foglalkozó cikket.

Egy alkalmazás az Ön könyvtárához való hozzáférésének eltávolításához (a hozzájárulás megadása után) a vállalati rendszergazdának el kell távolítania az alkalmazás szolgáltatásnevét. A rendszergazdának globális rendszergazdai hozzáféréssel kell rendelkeznie, és az Azure Portalon keresztül távolíthatja el az alkalmazást, vagy az [Azure AD PowerShell-parancsmagok](https://go.microsoft.com/fwlink/?LinkId=294151) használatával törölheti a hozzáférést.

## <a name="next-steps"></a>További lépések

Tekintse át az alkalmazásfelügyelethez kapcsolódó alábbi rövid útmutatókat is:

* [Alkalmazás regisztrálása a Microsoft identitásplatformján](quickstart-register-app.md)
* [Ügyfélalkalmazás konfigurálása a webes API-k elérésére](quickstart-configure-app-access-web-apis.md)
* [Alkalmazás konfigurálása webes API-k közzétételére](quickstart-configure-app-expose-web-apis.md)
* [Alkalmazás által támogatott fiókok módosítása](quickstart-modify-supported-accounts.md)
