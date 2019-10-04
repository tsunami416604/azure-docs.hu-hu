---
title: A Microsoft identitásplatformján regisztrált alkalmazások eltávolítása | Azure
description: Útmutató a Microsoft identitásplatformján regisztrált alkalmazás eltávolításához.
services: active-directory
documentationcenter: ''
author: rwike77
manager: CelesteDG
editor: ''
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 05/08/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: aragra, lenalepa, sureshja
ms.collection: M365-identity-device-management
ms.openlocfilehash: eddf16ea4179d3c254538a367ceb80ed02598345
ms.sourcegitcommit: f6c85922b9e70bb83879e52c2aec6307c99a0cac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2019
ms.locfileid: "65545746"
---
# <a name="quickstart-remove-an-application-registered-with-the-microsoft-identity-platform"></a>Gyors útmutató: A Microsoft identity platform regisztrált alkalmazás eltávolítása

Az alkalmazásokat a Microsoft identitásplatformján regisztráló nagyvállalati fejlesztőknek és SaaS-szolgáltatóknak (szoftverszolgáltatók) esetenként el kell távolítaniuk az alkalmazás regisztrációját.

Ennek a rövid útmutatónak a segítségével megtanulhatja a következőket:

* A saját maga vagy a szervezete által fejlesztett alkalmazás eltávolítása
* Más szervezet által fejlesztett alkalmazás eltávolítása

## <a name="prerequisites"></a>Előfeltételek

Rendelkeznie kell egy, a regisztrált alkalmazások rendelkező bérlő. Az alkalmazások hozzáadásával és regisztrálásával kapcsolatban tekintse meg [az alkalmazásoknak a Microsoft identitásplatformján történő regisztrálását](quickstart-register-app.md) ismertető cikket.

## <a name="remove-an-application-authored-by-you-or-your-organization"></a>A saját maga vagy a szervezete által fejlesztett alkalmazás eltávolítása

A saját maga vagy a szervezete által regisztrált alkalmazásokat egy alkalmazás- és egy szolgáltatásnév-objektum is jelöli a bérlőn. További információkért tekintse meg [az alkalmazás- és szolgáltatásnév-objektumokat](active-directory-application-objects.md) ismertető cikket.

### <a name="to-remove-an-application"></a>Alkalmazás eltávolítása

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
2. Ha a fiókja több bérlőhöz is biztosít hozzáférést, válassza ki a fiókot az oldal jobb felső sarkában, és állítsa a portálmunkamenetét a kívánt Azure AD-bérlőre.
3. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatásra, majd válassza ki **alkalmazásregisztrációk**. Keresse meg, és válassza ki a konfigurálni kívánt alkalmazást. Miután kiválasztotta az alkalmazást, megjelenik annak **Áttekintés** lapja.
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
