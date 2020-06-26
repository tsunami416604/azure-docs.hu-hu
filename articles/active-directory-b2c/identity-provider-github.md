---
title: Regisztráció és bejelentkezés beállítása GitHub-fiókkal
titleSuffix: Azure AD B2C
description: Adja meg a regisztrációt, és jelentkezzen be az ügyfeleknek a GitHub-fiókokkal az alkalmazásaiban Azure Active Directory B2C használatával.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ba2441ae48c99d63ae637d2b80069058a04c5ef9
ms.sourcegitcommit: b56226271541e1393a4b85d23c07fd495a4f644d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/26/2020
ms.locfileid: "85388187"
---
# <a name="set-up-sign-up-and-sign-in-with-a-github-account-using-azure-active-directory-b2c"></a>Hozzon létre egy GitHub-fiókkal történő regisztrációt és bejelentkezést Azure Active Directory B2C

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-github-oauth-application"></a>GitHub OAuth-alkalmazás létrehozása

Ha a GitHub-fiókot Azure Active Directory B2C (Azure AD B2C) [identitás-szolgáltatóként](authorization-code-flow.md) szeretné használni, létre kell hoznia egy alkalmazást a bérlőben, amely azt képviseli. Ha még nem rendelkezik GitHub-fiókkal, regisztrálhat a következő címen: [https://www.github.com/](https://www.github.com/) .

1. Jelentkezzen be a [GitHub fejlesztői](https://github.com/settings/developers) webhelyére a GitHub hitelesítő adataival.
1. Válassza a **OAuth-alkalmazások** lehetőséget, majd az **új OAuth alkalmazást**.
1. Adja meg az **alkalmazás nevét** és a **Kezdőlap URL-címét**.
1. Adja meg `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` az **engedélyezési visszahívási URL-címet**. Cserélje le a helyére `your-tenant-name` a Azure ad B2C bérlő nevét. A bérlő nevének megadásakor használja az összes kisbetűt, még akkor is, ha a bérlőt nagybetűvel definiálják Azure AD B2Cban.
1. Kattintson az **alkalmazás regisztrálása**elemre.
1. Másolja ki az **ügyfél-azonosító** és az **ügyfél titkos kulcsának**értékeit. Mindkét eszközhöz hozzá kell adnia az identitás-szolgáltatót a bérlőhöz.

## <a name="configure-a-github-account-as-an-identity-provider"></a>GitHub-fiók konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy a Azure AD B2C bérlőjét tartalmazó könyvtárat használja, majd a felső menüben válassza ki a **címtár + előfizetés** szűrőt, és válassza ki a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza az **identitás-szolgáltatók**, majd a **GitHub (előzetes verzió)** lehetőséget.
1. Adjon meg egy **nevet**. Például: *GitHub*.
1. Az **ügyfél-azonosító**mezőben adja meg a korábban létrehozott GitHub-alkalmazás ügyfél-azonosítóját.
1. Az **ügyfél titka**mezőben adja meg a rögzített ügyfél-titkot.
1. Kattintson a **Mentés** gombra.
