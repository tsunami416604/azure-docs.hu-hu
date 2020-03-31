---
title: Regisztráció és bejelentkezés beállítása Weibo-fiókkal
titleSuffix: Azure AD B2C
description: Az Azure Active Directory B2C használatával weibo-fiókkal rendelkező ügyfeleknek is biztosíthatja a regisztrációt és a bejelentkezést az alkalmazásokban.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: e99b5717cdcc32d8fd138d1edf5fe1bd3283c70e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78187899"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés beállítása Weibo-fiókkal az Azure Active Directory B2C használatával

[!INCLUDE [active-directory-b2c-public-preview](../../includes/active-directory-b2c-public-preview.md)]

## <a name="create-a-weibo-application"></a>Weibo-alkalmazás létrehozása

A Weibo-fiók identitásszolgáltatóként való használatához az Azure Active Directory B2C (Azure AD B2C) szolgáltatásban létre kell hoznia egy alkalmazást a bérlőben, amely azt képviseli. Ha még nem rendelkezik Weibo-fiókkal, regisztrálhat a. [https://weibo.com/signup/signup.php?lang=en-us](https://weibo.com/signup/signup.php?lang=en-us)

1. Jelentkezzen be a [Weibo fejlesztői portálra](https://open.weibo.com/) a Weibo-fiók hitelesítő adataival.
1. A bejelentkezés után válassza ki a megjelenítendő nevet a jobb felső sarokban.
1. A legördülő menüben válassza a **(fejlesztői** információk szerkesztése) lehetőséget.
1. Adja meg a szükséges információkat, és válassza **a (küldés).**
1. Fejezze be az e-mail-ellenőrzési folyamatot.
1. Nyissa meg a [személyazonosság-ellenőrzési lapot.](https://open.weibo.com/developers/identity/edit)
1. Adja meg a szükséges információkat, és válassza **a (küldés).**

### <a name="register-a-weibo-application"></a>Weibo-alkalmazás regisztrálása

1. Nyissa meg a [Weibo új alkalmazásregisztrációs oldalát.](https://open.weibo.com/apps/new)
1. Adja meg a szükséges alkalmazásadatokat.
1. Válassza **a (létrehozás)** lehetőséget.
1. Másolja az **Alkalmazáskulcs** és az **Alkalmazástitkos kulcs értékeit.** Mindkét identitásszolgáltató hozzáadása a bérlőhöz mindkét szükséges.
1. Töltse fel a szükséges képeket, és adja meg a szükséges információkat.
1. Válassza a**啦ンンンンンン (mentés) lehetőséget.**
1. Válassza a**啦ンンン (speciális** információk) lehetőséget.
1. Válassza **az** OAuth2.0.0(átirányítási URL) **授权设置** mező je melletti (szerkesztés) lehetőséget.
1. Írja `https://your-tenant-name.b2clogin.com/your-tenant-name.onmicrosoft.com/oauth2/authresp` be az OAuth2.0 **授权设置** (átirányítási URL) értéket. Ha például a bérlő neve contoso, állítsa `https://contoso.b2clogin.com/contoso.onmicrosoft.com/oauth2/authresp`be az URL-címet.
1. Válassza **a (küldés) lehetőséget.**

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Weibo-fiók beállítása identitásszolgáltatóként

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
1. Győződjön meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárat használja, ha a felső **menüben** a Directory + előfizetésszűrőt választja, és kiválasztja a bérlőt tartalmazó könyvtárat.
1. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
1. Válassza **az Identitásszolgáltatók**lehetőséget, majd a **Weibo (Előzetes verzió)** lehetőséget.
1. Írjon be egy **nevet**. *Például, Weibo*.
1. Az **ügyfélazonosítóhoz**adja meg a korábban létrehozott Weibo-alkalmazás alkalmazáskulcsát.
1. Az **ügyfél titkos kulcsot**adja meg az alkalmazás titkos, hogy a rögzített.
1. Kattintson a **Mentés** gombra.
