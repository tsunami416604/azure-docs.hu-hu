---
title: Regisztráció és bejelentkezés beállítása GitHub-fiókkal – Azure Active Directory B2C
description: Adja meg a regisztráció és bejelentkezés az alkalmazásokban az Azure Active Directory B2C használatával GitHub-fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 74c663d8847c2829a5d9466f8e601dd44593a6f8
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/17/2019
ms.locfileid: "71065197"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Regisztráljon, és jelentkezzen be a GitHub-fiók az Azure Active Directory B2C beállítása

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>Egy GitHub-OAuth-alkalmazás létrehozása

Ha a GitHub-fiókot Azure Active Directory B2C (Azure AD B2C) [identitás-szolgáltatóként](active-directory-b2c-reference-oauth-code.md) szeretné használni, létre kell hoznia egy alkalmazást a bérlőben, amely azt képviseli. Ha még nem rendelkezik GitHub-fiókkal, regisztrálhat a következő címen: [https://www.github.com/](https://www.github.com/).

1. Jelentkezzen be a [GitHub fejlesztői](https://github.com/settings/developers) webhely a GitHub hitelesítő adataival.
1. Válassza ki **OAuth alkalmazások** majd **új OAuth-alkalmazás**.
1. Adjon meg egy **alkalmazásnév** és a **kezdőlap URL-címe**.
1. Adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` a **engedélyezési visszahívási URL-Címének**. Cserélje le `your-tenant-name` az Azure AD B2C-bérlő nevével. Kisbetűk használhatók, ha akkor is, ha a bérlő Azure AD B2C-ben nagybetűk van definiálva, írja be a bérlő neve.
1. Kattintson a **alkalmazás regisztrálása**.
1. Másolja le az értékeket a **ügyfél-azonosító** és **titkos Ügyfélkód**. Mindkettő az identitásszolgáltató hozzáadása a bérlőhöz van szüksége.

## <a name="configure-a-github-account-as-an-identity-provider"></a>GitHub-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a **GitHub (előzetes verzió)** lehetőséget.
1. Adjon meg egy **nevet**. Például: *GitHub*.
1. Az **ügyfél-azonosító**mezőben adja meg a korábban létrehozott GitHub-alkalmazás ügyfél-azonosítóját.
1. Az **ügyfél titka**mezőben adja meg a rögzített ügyfél-titkot.
1. Kattintson a **Mentés** gombra.
