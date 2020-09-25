---
title: Regisztráció és bejelentkezés beállítása LinkedIn-fiókkal
titleSuffix: Azure AD B2C
description: Adja meg a regisztrációt, és jelentkezzen be az alkalmazásokban a LinkedIn-fiókkal rendelkező ügyfeleknek Azure Active Directory B2C használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/22/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 10c460775bcb63028f03d0e8d0b1f7ed1507cdb4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91259458"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Bejelentkezés és bejelentkezés beállítása LinkedIn-fiókkal Azure Active Directory B2C használatával

## <a name="create-a-linkedin-application"></a>LinkedIn-alkalmazás létrehozása

Ha Azure Active Directory B2C (Azure AD B2C) identitás- [szolgáltatóként](authorization-code-flow.md) szeretné használni a LinkedIn-fiókot, létre kell hoznia egy alkalmazást a bérlőben, amely azt jelképezi. Ha még nem rendelkezik LinkedIn-fiókkal, regisztrálhat a következő címen: [https://www.linkedin.com/](https://www.linkedin.com/) .

1. Jelentkezzen be a [LinkedIn Developers webhelyére](https://www.developer.linkedin.com/) a LinkedIn-fiókja hitelesítő adataival.
1. Válassza **a saját alkalmazások**lehetőséget, majd kattintson az **alkalmazás létrehozása**elemre.
1. Adja meg az **alkalmazás nevét**, a **LinkedIn oldalt**, az **adatvédelmi szabályzat URL-címét**és az **alkalmazás emblémáját**.
1. Fogadja el a LinkedIn **API használati feltételeit** , és kattintson az **alkalmazás létrehozása**elemre.
1. Válassza az **Auth (hitelesítés** ) fület. A **hitelesítési kulcsok**területen másolja ki az **ügyfél-azonosító** és az **ügyfél titkos**kulcsának értékeit. Mindkettőre szüksége lesz a LinkedIn identitás-szolgáltatóként való konfigurálásához a bérlőben. Az **ügyfél titkos kulcsa** fontos biztonsági hitelesítő adat.
1. Jelölje be az **alkalmazáshoz tartozó, a hitelesítő átirányítási URL-címek**melletti ceruza szerkesztése elemet, majd válassza az **átirányítási URL-cím hozzáadása**lehetőséget Adja meg a `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` kifejezést a `your-tenant-name` bérlő nevével. A bérlő nevének megadásakor az összes kisbetűt kell használnia, még akkor is, ha a bérlőt nagybetűvel definiálták Azure AD B2C. Válassza a **Frissítés** lehetőséget.
2. Alapértelmezés szerint a LinkedIn alkalmazás nem rendelkezik jóváhagyással a bejelentkezéshez kapcsolódó hatókörökhöz. Ha felülvizsgálatot szeretne kérni, válassza a **termékek** lapot, majd válassza a **Bejelentkezés a LinkedIn**használatával lehetőséget. A felülvizsgálat befejezésekor a rendszer hozzáadja a szükséges hatóköröket az alkalmazáshoz.
   > [!NOTE]
   > Az alkalmazáshoz jelenleg engedélyezett hatóköröket az **OAuth 2,0-hatókörök** szakaszban található **hitelesítés** lapon tekintheti meg.

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>LinkedIn-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a **LinkedIn**lehetőséget.
1. Adjon meg egy **nevet**. Például: *LinkedIn*.
1. Az **ügyfél-azonosító**mezőben adja meg a korábban létrehozott LinkedIn-alkalmazás ügyfél-azonosítóját.
1. Az **ügyfél titka**mezőben adja meg a rögzített ügyfél-titkot.
1. Kattintson a **Mentés** gombra.

## <a name="migration-from-v10-to-v20"></a>Migrálás v 1.0-ről v 2.0-ra

A LinkedIn nemrég [frissítette az API-kat 1.0-s és v 2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates)-s verzióra. A Migrálás részeként Azure AD B2C csak a LinkedIn-felhasználó teljes nevét tudja megszerezni a regisztráció során. Ha egy e-mail-cím a regisztráció során összegyűjtött attribútumok egyike, a felhasználónak manuálisan kell megadnia az e-mail-címet, és hitelesítenie kell azt.
