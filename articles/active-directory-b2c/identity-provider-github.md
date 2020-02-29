---
title: Regisztráció és bejelentkezés beállítása GitHub-fiókkal
titleSuffix: Azure AD B2C
description: Adja meg a regisztráció és bejelentkezés az alkalmazásokban az Azure Active Directory B2C használatával GitHub-fiókkal rendelkező ügyfelek számára.
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
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78188205"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Regisztráljon, és jelentkezzen be a GitHub-fiók az Azure Active Directory B2C beállítása

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Egy GitHub-OAuth-alkalmazás létrehozása

Ha a GitHub-fiókot Azure Active Directory B2C (Azure AD B2C) [identitás-szolgáltatóként](authorization-code-flow.md) szeretné használni, létre kell hoznia egy alkalmazást a bérlőben, amely azt képviseli. Ha még nem rendelkezik GitHub-fiókkal, regisztrálhat [https://www.github.com/](https://www.github.com/)címen.

1. Jelentkezzen be a [GitHub fejlesztői](https://github.com/settings/developers) webhelyére a GitHub hitelesítő adataival.
1. Válassza a **OAuth-alkalmazások** lehetőséget, majd az **új OAuth alkalmazást**.
1. Adja meg az **alkalmazás nevét** és a **Kezdőlap URL-címét**.
1. Adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` az **engedélyezési visszahívási URL-címben**. Cserélje le a `your-tenant-name`t a Azure AD B2C bérlő nevére. Kisbetűk használhatók, ha akkor is, ha a bérlő Azure AD B2C-ben nagybetűk van definiálva, írja be a bérlő neve.
1. Kattintson az **alkalmazás regisztrálása**elemre.
1. Másolja ki az **ügyfél-azonosító** és az **ügyfél titkos kulcsának**értékeit. Mindkettő az identitásszolgáltató hozzáadása a bérlőhöz van szüksége.

## <a name="configure-a-github-account-as-an-identity-provider"></a>GitHub-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a **GitHub (előzetes verzió)** lehetőséget.
1. Adjon meg egy **nevet**. Például: *GitHub*.
1. Az **ügyfél-azonosító**mezőben adja meg a korábban létrehozott GitHub-alkalmazás ügyfél-azonosítóját.
1. Az **ügyfél titka**mezőben adja meg a rögzített ügyfél-titkot.
1. Kattintson a **Mentés** gombra.
