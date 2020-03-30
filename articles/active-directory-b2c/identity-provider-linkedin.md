---
title: Regisztráció és bejelentkezés beállítása LinkedIn-fiókkal
titleSuffix: Azure AD B2C
description: Az Azure Active Directory B2C használatával linkedin-fiókkal rendelkező ügyfelek nek is biztosíthatja a regisztrációt és a bejelentkezést az alkalmazásokban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: d32eb80ffe296d86164a6d27a1b7a28181357243
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188100"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés beállítása LinkedIn-fiókkal az Azure Active Directory B2C használatával

## <a name="create-a-linkedin-application"></a>LinkedIn-alkalmazás létrehozása

A LinkedIn-fiók [identitásszolgáltatóként](authorization-code-flow.md) való használatához az Azure Active Directory B2C (Azure AD B2C) szolgáltatásban létre kell hoznia egy alkalmazást a bérlőben, amely azt képviseli. Ha még nem rendelkezik LinkedIn-fiókkal, regisztrálhat a. [https://www.linkedin.com/](https://www.linkedin.com/)

1. Jelentkezzen be a [LinkedIn Developers webhelyére](https://www.developer.linkedin.com/) a LinkedIn-fiók hitelesítő adataival.
1. Válassza **a Saját alkalmazások**lehetőséget, majd kattintson az Alkalmazás létrehozása **gombra.**
1. Adja meg **a vállalat nevét**, az alkalmazás **nevét**, az **alkalmazás leírását**, **az alkalmazásemblémát,** **az alkalmazás használatát,** **a webhely URL-címét,** **az üzleti e-mail címet**és a vállalati **telefonszámot.**
1. Fogadja el a **LinkedIn API használati feltételeit,** és kattintson a **Küldés gombra.**
1. Másolja az **ügyfélazonosító** és az **ügyféltitok értékeit.** A **hitelesítési kulcsok csoportban**találhatók. Mindkettőre szüksége lesz a LinkedIn identitásszolgáltatóként való konfigurálásához a bérlőben. **Az ügyféltitkos adat** fontos biztonsági hitelesítő adat.
1. Írja `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` be az **Engedélyezett átirányítási URL-címek mezőbe.** Cserélje `your-tenant-name` le a bérlő nevét. Az összes kisbetűt kell használnia a bérlő nevének megadásakor, még akkor is, ha a bérlő nagybetűkkel van definiálva az Azure AD B2C-ben. Válassza **a Hozzáadás**lehetőséget, majd kattintson a **Frissítés gombra.**

## <a name="configure-a-linkedin-account-as-an-identity-provider"></a>LinkedIn-fiók beállítása identitásszolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza **az Identitásszolgáltatók**lehetőséget, majd a **LinkedIn**lehetőséget.
1. Írjon be egy **nevet**. Például a *LinkedIn*.
1. Az **ügyfélazonosítóhoz**adja meg a korábban létrehozott LinkedIn-alkalmazás ügyfélazonosítóját.
1. Az **ügyféltitkos kulcsot**adja meg a rögzített ügyféltitkoskulcsot.
1. Kattintson a **Mentés** gombra.

## <a name="migration-from-v10-to-v20"></a>Áttelepítés az 1.0-s és 2.0-s alkalmazásközött

LinkedIn nemrég [frissítette API-k a v1.0 v2.0](https://engineering.linkedin.com/blog/2018/12/developer-program-updates). Az áttelepítés részeként az Azure AD B2C csak a LinkedIn-felhasználó teljes nevét tudja megszerezni a regisztráció során. Ha az e-mail-cím a regisztráció során gyűjtött attribútumok egyike, a felhasználónak manuálisan kell megadnia az e-mail címet, és érvényesítenie kell azt.
