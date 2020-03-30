---
title: Regisztráció és bejelentkezés beállítása GitHub-fiókkal
titleSuffix: Azure AD B2C
description: Az Azure Active Directory B2C használatával GitHub-fiókkal rendelkező ügyfelek számára biztosíthatja a regisztrációt és a bejelentkezést az alkalmazásokban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: a7435abf2740800184a6de1aad07bca53cd56cf8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78188205"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés beállítása GitHub-fiókkal az Azure Active Directory B2C használatával

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>GitHub OAuth-alkalmazás létrehozása

A GitHub-fiók [identitásszolgáltatóként](authorization-code-flow.md) való használatához az Azure Active Directory B2C (Azure AD B2C) szolgáltatásban létre kell hoznia egy alkalmazást a bérlőben, amely azt képviseli. Ha még nem rendelkezik GitHub-fiókkal, regisztrálhat a. [https://www.github.com/](https://www.github.com/)

1. Jelentkezzen be a [GitHub fejlesztői](https://github.com/settings/developers) webhelyére a GitHub-hitelesítő adataival.
1. Válassza **az OAuth Apps** lehetőséget, majd az Új **OAuth alkalmazás**lehetőséget.
1. Adja meg **az alkalmazás nevét** és a kezdőlap **URL-címét.**
1. Írja `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` be az **engedélyezési visszahívás URL-címét.** Cserélje `your-tenant-name` le az Azure AD B2C-bérlő nevét. Használja az összes kisbetűt a bérlő nevének megadásakor, még akkor is, ha a bérlő nagybetűkkel van definiálva az Azure AD B2C-ben.
1. Kattintson **az Alkalmazás regisztrálása gombra.**
1. Másolja az **ügyfélazonosító** és az **ügyféltitok értékeit.** Mindkettőnek hozzá kell adnia az identitásszolgáltatót a bérlőhöz.

## <a name="configure-a-github-account-as-an-identity-provider"></a>GitHub-fiók konfigurálása identitásszolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza **az Identitásszolgáltatók**lehetőséget, majd válassza a **GitHub (Előzetes verzió)** lehetőséget.
1. Írjon be egy **nevet**. Például *a GitHub*.
1. Az **ügyfélazonosítóhoz**adja meg a korábban létrehozott GitHub-alkalmazás ügyfélazonosítóját.
1. Az **ügyféltitkos kulcsot**adja meg a rögzített ügyféltitkoskulcsot.
1. Kattintson a **Mentés** gombra.
