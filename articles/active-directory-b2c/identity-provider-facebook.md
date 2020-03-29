---
title: Regisztráció és bejelentkezés beállítása Facebook-fiókkal
titleSuffix: Azure AD B2C
description: Az Azure Active Directory B2C használatával facebookos fiókkal rendelkező ügyfeleknek adhat meg regisztrációt és bejelentkezést az alkalmazásokban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 09/26/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: cd0e19de88a6a65d72a2e7e19f7fca2a94d8da55
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188273"
---
# <a name="set-up-sign-up-and-sign-in-with-a-facebook-account-using-azure-active-directory-b2c"></a>Facebook-fiókkal való regisztráció és bejelentkezés beállítása az Azure Active Directory B2C használatával

## <a name="create-a-facebook-application"></a>Facebook-alkalmazás létrehozása

Ha egy Facebook-fiókot [szeretne identitásszolgáltatóként](authorization-code-flow.md) használni az Azure Active Directory B2C (Azure AD B2C) szolgáltatásban, létre kell hoznia egy alkalmazást a bérlőben, amely azt képviseli. Ha még nem rendelkezik Facebook-fiókkal, regisztrálhat a . [https://www.facebook.com/](https://www.facebook.com/)

1. Jelentkezz be [a Facebookra a fejlesztők számára](https://developers.facebook.com/) a Facebook-fiók hitelesítő adataiddal.
1. Ha még nem tetted meg, facebook-fejlesztőként kell regisztrálnod. Ehhez válaszd az **Első lépések** lehetőséget a lap jobb felső sarkában, fogadd el a Facebook irányelveit, és hajtsd végre a regisztrációs lépéseket.
1. Válassza **a Saját alkalmazások** lehetőséget, majd az Alkalmazás létrehozása **lehetőséget.**
1. Adja meg **a megjelenítendő nevet** és az érvényes **partneri e-mailt.**
1. Válassza **az Alkalmazásazonosító létrehozása**lehetőséget. Ehhez szükség lehet arra, hogy elfogadd a Facebook platformszabályzatát, és elvégezz egy online biztonsági ellenőrzést.
1. Válassza az > **Alapbeállítások**lehetőséget. **Settings**
1. Válasszon például `Business and Pages` **egy kategóriát.** Ezt az értéket a Facebook nak kell megadnia, de az Azure AD B2C-hez nem használja.
1. A lap alján válassza a **Platform hozzáadása**lehetőséget, majd a **Webhely**lehetőséget.
1. A **Webhely URL-címében**írja be `https://your-tenant-name.b2clogin.com/` a bérlő nevének helyettesítését. `your-tenant-name` Adja meg például `http://www.contoso.com`az **Adatvédelmi szabályzat URL-címét.** A házirend URL-címe egy olyan oldal, amelyet azért tart fenn, hogy az alkalmazás adatvédelmi adatait megadhassa.
1. Válassza a **Módosítások mentése lehetőséget.**
1. A lap tetején másolja az **Alkalmazásazonosító**értékét .
1. Válassza az **Alkalmazástitkos**ság **megjelenítése** és másolása lehetőséget. Mindkettőt arra használod, hogy a Facebookot identitásszolgáltatóként konfiguráld a bérlődben. **Az Alkalmazástitkosítsa** fontos biztonsági hitelesítő adatokat.
1. Válaszd a **Termékek**melletti pluszjelet, majd a **Facebook Bejelentkezés csoportban**a **Beállítás** lehetőséget.
1. A **Facebook bejelentkezése csoportban**válassza a **Beállítások lehetőséget.**
1. Az Érvényes OAuth átirányítási `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp`URI-k mezőbe írja be a ( **érvényességi azonosító) átirányítási urisz-t.** Cserélje `your-tenant-name` le a bérlő nevét. Válassza a **Módosítások mentése lehetőséget** a lap alján.
1. Ha elérhetővé szeretné tenni a Facebook-alkalmazását az Azure AD B2C számára, **On** válassza az oldal jobb felső részén található Állapotválasztót, és kapcsolja be az alkalmazás nyilvánossá tegyük, majd válassza a **Váltás mód lehetőséget.**  Ezen a ponton az állapotnak **fejlesztésiről** **live-ra**kell változnia.

## <a name="configure-a-facebook-account-as-an-identity-provider"></a>Facebook-fiók beállítása identitásszolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza **az Identitásszolgáltatók**lehetőséget, majd a **Facebook**lehetőséget.
1. Írjon be egy **nevet**. Például a *Facebook*.
1. Az **ügyfélazonosítóhoz**adja meg a korábban létrehozott Facebook-alkalmazás alkalmazásazonosítóját.
1. Az **ügyfél titkos kulcsot**adja meg az alkalmazás titkos, hogy a rögzített.
1. Kattintson a **Mentés** gombra.
