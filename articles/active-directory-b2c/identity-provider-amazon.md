---
title: Regisztráció és bejelentkezés beállítása Amazon-fiókkal
titleSuffix: Azure AD B2C
description: Az Azure Active Directory B2C használatával amazonos fiókkal rendelkező ügyfelek számára biztosíthatja a regisztrációt és a bejelentkezést az alkalmazásokban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d4538c1d15aeae624f5d73e9985448bda2fd8f1b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188460"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Bejelentkezés és bejelentkezés beállítása Amazon-fiókkal az Azure Active Directory B2C használatával

## <a name="create-an-amazon-application"></a>Amazon alkalmazás létrehozása

Az Amazon-fiók [identitásszolgáltatóként](authorization-code-flow.md) való használatához az Azure Active Directory B2C (Azure AD B2C) szolgáltatásban létre kell hoznia egy alkalmazást a bérlőben, amely azt képviseli. Ha még nem rendelkezik Amazon-fiókkal, [https://www.amazon.com/](https://www.amazon.com/)regisztrálhat a .

1. Jelentkezzen be az [Amazon Developer Centerbe](https://login.amazon.com/) amazonos fiókjának hitelesítő adataival.
1. Ha még nem tette meg, kattintson a **Regisztráció**gombra, kövesse a fejlesztői regisztrációs lépéseket, és fogadja el a szabályzatot.
1. Válassza **az Új alkalmazás regisztrálása**lehetőséget.
1. Adja meg **a név,** **a leírás**és **az adatvédelmi nyilatkozat URL-címét,** majd kattintson a **Mentés gombra.** Az adatvédelmi nyilatkozat egy ön által kezelt oldal, amely adatvédelmi információkat biztosít a felhasználók számára.
1. A **Webbeállítások** csoportban másolja az **Ügyfélazonosító**értékét. Válassza **a Titkos titok megjelenítése** lehetőséget az ügyféltitok leéséhez, majd másolja azt. Mindkettőre szüksége van egy Amazon-fiók konfigurálásához a bérlőidentitás-szolgáltatóként. **Az ügyféltitkos adat** fontos biztonsági hitelesítő adat.
1. A **Webes beállítások** csoportban válassza a `https://your-tenant-name.b2clogin.com` **Szerkesztés**lehetőséget, majd `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` írja be az **Engedélyezett JavaScript-eredetek** és az Engedélyezett **visszatérési URL-címek mezőbe.** Cserélje `your-tenant-name` le a bérlő nevét. Az összes kisbetűt kell használnia a bérlő nevének megadásakor, még akkor is, ha a bérlő nagybetűkkel van definiálva az Azure AD B2C-ben.
1. Kattintson a **Mentés** gombra.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Amazon-fiók beállítása identitásszolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza **az Identitásszolgáltatók**lehetőséget, majd az **Amazon**lehetőséget.
1. Írjon be egy **nevet**. Például az *Amazon*.
1. Az **ügyfélazonosítóhoz**adja meg a korábban létrehozott Amazon-alkalmazás ügyfélazonosítóját.
1. Az **ügyféltitkos kulcsot**adja meg a rögzített ügyféltitkoskulcsot.
1. Kattintson a **Mentés** gombra.
