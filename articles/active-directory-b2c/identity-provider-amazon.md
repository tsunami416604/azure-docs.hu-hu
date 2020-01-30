---
title: Regisztráció és bejelentkezés beállítása Amazon-fiókkal
titleSuffix: Azure AD B2C
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
ms.openlocfilehash: b22c4fb8f5c54437281e90a74032e8ee1d05bcb8
ms.sourcegitcommit: 5d6ce6dceaf883dbafeb44517ff3df5cd153f929
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2020
ms.locfileid: "76846639"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Egy Amazon-fiókkal történő regisztráció és bejelentkezés beállítása Azure Active Directory B2C

## <a name="create-an-amazon-application"></a>Amazon-alkalmazás létrehozása

Ha Azure Active Directory B2C (Azure AD B2C) identitás- [szolgáltatóként](authorization-code-flow.md) szeretné használni az Amazon-fiókot, létre kell hoznia egy alkalmazást a bérlőben, amely azt képviseli. Ha még nem rendelkezik Amazon-fiókkal, regisztrálhat [https://www.amazon.com/](https://www.amazon.com/)címen.

1. Jelentkezzen be az [Amazon fejlesztői központjába](https://login.amazon.com/) az Amazon-fiókja hitelesítő adataival.
1. Ha még nem tette meg, kattintson a **regisztráció**gombra, kövesse a fejlesztői regisztráció lépéseit, és fogadja el a szabályzatot.
1. Válassza az **új alkalmazás regisztrálása**lehetőséget.
1. Adja meg a **név**, a **Leírás**és az **adatvédelmi nyilatkozat URL-címét**, majd kattintson a **Mentés**gombra. Az adatvédelmi nyilatkozat egy olyan oldal, amelyet Ön kezel, és amely adatvédelmi információkat biztosít a felhasználóknak.
1. A **webes beállítások** szakaszban másolja ki az **ügyfél-azonosító**értékét. Válassza a **titok megjelenítése** lehetőséget az ügyfél titkos kódjának beolvasásához, majd másolja azt. Mindkettőnek szüksége van egy Amazon-fiók konfigurálására a bérlőben. Az **ügyfél titkos kulcsa** fontos biztonsági hitelesítő adat.
1. A **Webbeállítások** szakaszban válassza a **Szerkesztés**lehetőséget, majd írja be `https://your-tenant-name.b2clogin.com` az **engedélyezett JavaScript** -forrásokban és `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` az **engedélyezett visszatérési URL-címek**között. Cserélje le a `your-tenant-name`t a bérlő nevére. A bérlő nevének megadásakor az összes kisbetűt kell használnia, még akkor is, ha a bérlőt nagybetűvel definiálták Azure AD B2C.
1. Kattintson a **Mentés** gombra.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Amazon-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd az **Amazon**elemet.
1. Adjon meg egy **nevet**. Például: *Amazon*.
1. Az **ügyfél-azonosító**mezőben adja meg a korábban létrehozott Amazon-alkalmazás ügyfél-azonosítóját.
1. Az **ügyfél titka**mezőben adja meg a rögzített ügyfél-titkot.
1. Kattintson a **Mentés** gombra.
