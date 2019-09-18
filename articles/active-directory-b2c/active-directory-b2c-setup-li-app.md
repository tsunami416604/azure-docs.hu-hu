---
title: Regisztráció és bejelentkezés beállítása LinkedIn-fiókkal – Azure Active Directory B2C
description: Adja meg a regisztrációt, és jelentkezzen be az alkalmazásokban a LinkedIn-fiókkal rendelkező ügyfeleknek Azure Active Directory B2C használatával.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: be347fe33e03b4c2a7ecc1015a407c5a58062326
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065153"
---
# <a name="set-up-sign-up-and-sign-in-with-a-linkedin-account-using-azure-active-directory-b2c"></a>Bejelentkezés és bejelentkezés beállítása LinkedIn-fiókkal Azure Active Directory B2C használatával

## <a name="create-a-linkedin-application"></a>LinkedIn-alkalmazás létrehozása

Ha Azure Active Directory B2C (Azure AD B2C) identitás- [szolgáltatóként](active-directory-b2c-reference-oauth-code.md) szeretné használni a LinkedIn-fiókot, létre kell hoznia egy alkalmazást a bérlőben, amely azt jelképezi. Ha még nem rendelkezik LinkedIn-fiókkal, regisztrálhat a következő címen: [https://www.linkedin.com/](https://www.linkedin.com/).

1. Jelentkezzen be a [LinkedIn Developers webhelyére](https://www.developer.linkedin.com/) a LinkedIn-fiókja hitelesítő adataival.
1. Válassza **a saját alkalmazások**lehetőséget, majd kattintson az **alkalmazás létrehozása**elemre.
1. Adja meg a **vállalat nevét**, az **alkalmazás nevét**, az **alkalmazás leírását**, az **alkalmazás emblémáját**, **az alkalmazás használatát**, a **webhely URL-címét**, az **üzleti levelezést**és a **munkahelyi telefont**
1. Fogadja el a **LINKEDIN API használati feltételeit** , és kattintson a **Submit (Küldés**) lehetőségre.
1. Másolja le az értékeket a **ügyfél-azonosító** és **titkos Ügyfélkód**. Ezek a **hitelesítési kulcsok**alatt találhatók. Mindkettőre szüksége lesz a LinkedIn identitás-szolgáltatóként való konfigurálásához a bérlőben. Az **ügyfél titkos kulcsa** fontos biztonsági hitelesítő adat.
1. Adja `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` meg a **jóváhagyott átirányítási URL-címeket**. Cserélje `your-tenant-name` le a helyére a bérlő nevét. A bérlő nevének megadásakor az összes kisbetűt kell használnia, még akkor is, ha a bérlőt nagybetűvel definiálták Azure AD B2C. Válassza a **Hozzáadás**, majd a **frissítés**lehetőséget.

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
