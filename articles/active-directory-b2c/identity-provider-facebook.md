---
title: Regisztráció és bejelentkezés beállítása Facebook-fiókkal
titleSuffix: Azure AD B2C
description: A Azure Active Directory B2C használatával az alkalmazásokban Facebook-fiókkal rendelkező felhasználók számára biztosíthatja a regisztrációt és a bejelentkezést.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 10/26/2019
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 62956000e143f5504d32dae26953bcf877ce96a6
ms.sourcegitcommit: 3e8058f0c075f8ce34a6da8db92ae006cc64151a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/27/2020
ms.locfileid: "92628575"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés beállítása Facebook-fiókkal Azure Active Directory B2C használatával

## <a name="create-a-facebook-application"></a>Facebook-alkalmazás létrehozása

Ha Azure Active Directory B2C (Azure AD B2C) identitás- [szolgáltatóként](authorization-code-flow.md) szeretné használni a Facebook-fiókot, létre kell hoznia egy alkalmazást a bérlőben, amely azt jelképezi. Ha még nem rendelkezik Facebook-fiókkal, regisztrálhat a következő címen: [https://www.facebook.com/](https://www.facebook.com/) .

1. A Facebook-fiók hitelesítő adataival jelentkezzen be a [facebookba a fejlesztők számára](https://developers.facebook.com/) .
1. Ha még nem tette meg, regisztrálnia kell Facebook-fejlesztőként. Ehhez válassza az első **lépések** lehetőséget az oldal jobb felső sarkában, fogadja el a Facebook szabályzatait, és végezze el a regisztráció lépéseit.
1. Válassza **a saját alkalmazások** lehetőséget, majd **hozza létre az alkalmazást** .
1. Válassza a **csatlakoztatott élmények létrehozása** lehetőséget.
1. Adjon meg egy **megjelenítendő nevet** és egy érvényes **kapcsolattartási e-mailt** .
1. Válassza az **alkalmazás-azonosító létrehozása** lehetőséget. Előfordulhat, hogy el kell fogadnia a Facebook-platform szabályzatait, és el kell végeznie egy online biztonsági ellenőrzését.
1. Válassza a **Beállítások**  >  **alapszintű** lehetőséget.
    1. Válasszon egy **kategóriát** , például: `Business and Pages` . Ez az érték a Facebook számára szükséges, de Azure AD B2C nem használható.
    1. Adja meg az URL-címet a **szolgáltatási feltételek URL** -címéhez, például: `http://www.contoso.com/tos` . A szabályzat URL-címe egy olyan oldal, amelyet fenntart az alkalmazás használati feltételeinek megadásához.
    1. Adja meg az **adatvédelmi szabályzat URL** -címét (például `http://www.contoso.com/privacy` ). A szabályzat URL-címe olyan oldal, amelyet az alkalmazásra vonatkozó adatvédelmi információk biztosítására tart fenn.
1. A lap alján válassza a **platform hozzáadása** lehetőséget, majd válassza a **webhely** lehetőséget.
1. A **webhely URL** -címe mezőben adja meg a webhely címét, például: `https://contoso.com` . 
1. Válassza a **módosítások mentése** lehetőséget.
1. Az oldal tetején másolja az **alkalmazás-azonosító** értékét.
1. Válassza az **alkalmazás titkos kulcsának** **megjelenítése** és másolása lehetőséget. Mindkettőt használja a Facebook identitás-szolgáltatóként való konfigurálásához a bérlőben. Az **alkalmazás titkos kulcsa** fontos biztonsági hitelesítő adat.
1. A menüben válassza ki a **termékek** melletti **plusz** jelet. A **termékek hozzáadása az alkalmazáshoz** területen válassza a **beállítás** a **Facebook-Bejelentkezés** alatt lehetőséget.
1. A menüben válassza a **Facebook-Bejelentkezés** lehetőséget, majd válassza a **Beállítások** lehetőséget.
1. Az **érvényes OAuth átirányítási URI** -k mezőben adja meg a értéket `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` . Cserélje le a helyére a `your-tenant-name` bérlő nevét. A lap alján kattintson a **módosítások mentése** gombra.
1. Ha szeretné, hogy a Facebook-alkalmazás elérhető legyen a Azure AD B2C számára, válassza ki a lap jobb felső sarkában található állapotjelzőt, és kapcsolja be **, hogy az** alkalmazás nyilvános legyen, majd válassza a **váltás mód** lehetőséget.  Ezen a ponton az állapotnak a **fejlesztéstől** az **élő** értékre kell váltania.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Facebook-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C** -t.
1. Válassza az **identitás-szolgáltatók** , majd a **Facebook** lehetőséget.
1. Adjon meg egy **nevet** . Például: *Facebook* .
1. Az **ügyfél-azonosító** mezőben adja meg a korábban létrehozott Facebook-alkalmazás alkalmazás-azonosítóját.
1. Az **ügyfél titka** mezőben adja meg a rögzített alkalmazás titkát.
1. Válassza a **Mentés** lehetőséget.
