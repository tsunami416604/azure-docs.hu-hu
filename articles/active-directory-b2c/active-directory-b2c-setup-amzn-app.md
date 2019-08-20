---
title: Regisztráció és bejelentkezés beállítása Amazon-fiókkal – Azure Active Directory B2C
description: A regisztráció és bejelentkezés az Amazon-fiókkal rendelkező ügyfelek számára Azure Active Directory B2C használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 89dd592e6e5ea1ce71277035654068ce2f782890
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/19/2019
ms.locfileid: "69622202"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Egy Amazon-fiókkal történő regisztráció és bejelentkezés beállítása Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Amazon-alkalmazás létrehozása

Ha Amazon-fiókot szeretne használni Azure Active Directory (Azure AD) B2C-beli [identitás](active-directory-b2c-reference-oauth-code.md) -szolgáltatóként, létre kell hoznia egy alkalmazást a bérlőben, amely azt képviseli. Ha még nem rendelkezik Amazon-fiókkal, regisztrálhat [https://www.amazon.com/](https://www.amazon.com/).

1. Jelentkezzen be az [Amazon fejlesztői](https://login.amazon.com/) központjába az Amazon-fiókja hitelesítő adataival.
1. Ha még nem tette meg, kattintson a **regisztráció**gombra, kövesse a fejlesztői regisztráció lépéseit, és fogadja el a szabályzatot.
1. Válassza az **új alkalmazás regisztrálása**lehetőséget.
1. Adja meg a **név**, a **Leírás**és az **adatvédelmi nyilatkozat URL-címét**, majd kattintson a **Mentés**gombra. Az adatvédelmi nyilatkozat egy olyan oldal, amelyet Ön kezel, és amely adatvédelmi információkat biztosít a felhasználóknak.
1. A **webes beállítások** szakaszban másolja ki az **ügyfél-azonosító**értékét. Válassza a **titok megjelenítése** lehetőséget az ügyfél titkos kódjának beolvasásához, majd másolja azt. Mindkettőnek szüksége van egy Amazon-fiók konfigurálására a bérlőben. Az **ügyfél titkos kulcsa** fontos biztonsági hitelesítő adat.
1. A **webes beállítások** szakaszban válassza a **Szerkesztés**lehetőséget, majd adja meg `https://your-tenant-name.b2clogin.com` az **engedélyezett JavaScript** -eredetek és `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` az **engedélyezett visszatérési URL-címek**közötti értéket. Cserélje `your-tenant-name` le a helyére a bérlő nevét. A bérlő nevének megadásakor az összes kisbetűt kell használnia, még akkor is, ha a bérlőt nagybetűvel definiálták Azure AD B2C.
1. Kattintson a **Save** (Mentés) gombra.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Amazon-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd az **Amazon**elemet.
1. Adjon meg egy **nevet**. Például: *Amazon*.
1. Az **ügyfél-azonosító**mezőben adja meg a korábban létrehozott Amazon-alkalmazás ügyfél-azonosítóját.
1. Az **ügyfél titka**mezőben adja meg a rögzített ügyfél-titkot.
1. Kattintson a **Mentés** gombra.
