---
title: Regisztráció és bejelentkezés beállítása Facebook-fiókkal – Azure Active Directory B2C
description: A Azure Active Directory B2C használatával az alkalmazásokban Facebook-fiókkal rendelkező felhasználók számára biztosíthatja a regisztrációt és a bejelentkezést.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 524e1e5f877fcb03d4252d79635ef855b9811f09
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622096"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés beállítása Facebook-fiókkal Azure Active Directory B2C használatával

## <a name="create-a-facebook-application"></a>Facebook-alkalmazás létrehozása

Ha Facebook-fiókot szeretne használni Azure Active Directory (Azure AD) B2C-beli [identitás](active-directory-b2c-reference-oauth-code.md) -szolgáltatóként, létre kell hoznia egy alkalmazást a bérlőben, amely azt képviseli. Ha még nem rendelkezik Facebook-fiókkal, regisztrálhat a következő címen: [https://www.facebook.com/](https://www.facebook.com/).

1. A Facebook-fiók hitelesítő adataival jelentkezzen be a facebookba a [fejlesztők számára](https://developers.facebook.com/) .
1. Ha még nem tette meg, regisztrálnia kell Facebook-fejlesztőként. Ehhez válassza az első **lépések** lehetőséget az oldal jobb felső sarkában, fogadja el a Facebook szabályzatait, és végezze el a regisztráció lépéseit.
1. Válassza **a saját alkalmazások** lehetőséget, majd **vegyen fel új alkalmazást**.
1. Adjon meg egy **megjelenítendő nevet** és egy érvényes **kapcsolattartási e-mailt**.
1. Kattintson az **alkalmazás-azonosító létrehozása**elemre. Előfordulhat, hogy el kell fogadnia a Facebook-platform szabályzatait, és el kell végeznie egy online biztonsági ellenőrzését.
1. Válassza a **Beállítások** > alapszintű lehetőséget.
1. Válasszon egy **kategóriát**, például `Business and Pages`:. Ez az érték a Facebook számára szükséges, de Azure AD B2C nem használható.
1. A lap alján válassza a **platform hozzáadása**lehetőséget, majd válassza a **webhely**lehetőséget.
1. A **webhely URL**-címe `https://your-tenant-name.b2clogin.com/` mezőbe `your-tenant-name` írja be a helyére a bérlő nevét. Adja meg az **adatvédelmi szabályzat URL**-címét (például `http://www.contoso.com`). A szabályzat URL-címe olyan oldal, amelyet az alkalmazásra vonatkozó adatvédelmi információk biztosítására tart fenn.
1. Válassza a **módosítások mentése**lehetőséget.
1. Az oldal tetején másolja az **alkalmazás-azonosító**értékét.
1. Kattintson a **Megjelenítés** elemre, és másolja ki az **alkalmazás titkos kulcsának**értékét. Mindkettőt használja a Facebook identitás-szolgáltatóként való konfigurálásához a bérlőben. Az **alkalmazás titkos kulcsa** fontos biztonsági hitelesítő adat.
1. Válassza ki a **termékek**melletti plusz jelet, majd válassza a **beállítás** a **Facebook-Bejelentkezés**alatt lehetőséget.
1. A **Facebook-Bejelentkezés**területen válassza a **Beállítások**lehetőséget.
1. Az **érvényes OAuth átirányítási URI**- `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`k mezőben adja meg a értéket. Cserélje `your-tenant-name` le a helyére a bérlő nevét. Kattintson a lap alján található **módosítások mentése** gombra.
1. Ahhoz, hogy a Facebook-alkalmazás elérhető legyen a Azure AD B2C számára, kattintson az oldal jobb felső sarkában található állapot-választóra, majd kapcsolja be, hogy az alkalmazás nyilvános legyen, majd kattintson a **Confirm (megerősítés**) gombra.  Ezen a ponton az állapotnak a **fejlesztéstől** az **élő**értékre kell váltania.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Facebook-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a **Facebook**lehetőséget.
1. Adjon meg egy **nevet**. Például: *Facebook*.
1. Az **ügyfél-azonosító**mezőben adja meg a korábban létrehozott Facebook-alkalmazás alkalmazás-azonosítóját.
1. Az **ügyfél titka**mezőben adja meg a rögzített alkalmazás titkát.
1. Kattintson a **Mentés** gombra.
