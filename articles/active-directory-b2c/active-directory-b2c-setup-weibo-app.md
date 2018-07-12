---
title: Regisztráció és bejelentkezés a az Azure Active Directory B2C fiókkal Weibo beállítása |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés Weibo az alkalmazások az Azure Active Directory B2C fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/09/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: 2534e3e39d4a6dd1659dced5a1b0342798c0049e
ms.sourcegitcommit: f606248b31182cc559b21e79778c9397127e54df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/12/2018
ms.locfileid: "38969129"
---
# <a name="set-up-sign-up-and-sign-in-with-a-weibo-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés a az Azure Active Directory B2C fiókkal Weibo beállítása

> [!NOTE]
> Ez a funkció előzetes verzióban érhető el.
> 

## <a name="create-a-weibo-application"></a>Weibo alkalmazás létrehozása

Weibo fiók használatára az identitásszolgáltatójaként az Azure Active Directory (Azure AD) B2C-ben, hozzon létre egy alkalmazást a bérlőben, hogy az azt jelölő kell. Ha egy Weibo fiók még nem rendelkezik, beszerezheti a [ http://weibo.com/signup/signup.php?lang=en-us ](http://weibo.com/signup/signup.php?lang=en-us).

1. Jelentkezzen be a [Weibo fejlesztői portál](http://open.weibo.com/) Weibo-fiókja hitelesítő adataival.
2. Bejelentkezés után a jobb felső sarokban válassza ki a megjelenítési nevet.
3. A legördülő listában válassza ki a**编辑开发者信息**(fejlesztői adatok szerkesztése).
4. Adja meg a szükséges információkat, és válassza ki**提交**(beküldése).
5. Fejezze be az e-mail ellenőrzésének folyamatát.
6. Nyissa meg a [identitás-ellenőrzési lapot](http://open.weibo.com/developers/identity/edit).
7. Adja meg a szükséges információkat, és válassza ki**提交**(beküldése).

### <a name="register-a-weibo-application"></a>Egy Weibo alkalmazás regisztrálása

1. Nyissa meg a [új Weibo alkalmazásregisztrációs oldalán](http://open.weibo.com/apps/new).
2. Adja meg a szükséges alkalmazás adatait.
3. Válassza ki**创建**(létrehozás).
4. Másolja le az értékeket a **Alkalmazáskulcs** és **titkos Alkalmazáskulcs**. Mindkettő az identitásszolgáltató hozzáadása a bérlőhöz van szüksége.
5. A szükséges fényképek feltöltése, és adja meg a szükséges információkat.
6. Válassza ki**保存以上信息**(Mentés).
7. Válassza ki**高级信息**(speciális információk).
8. Válassza ki**编辑**(szerkesztési) mező mellett a OAuth2.0**授权设置**(átirányítási URL-cím).
9. Adja meg `https://login.microsoftonline.com/te/{tenant_name}/oauth2/authresp` a OAuth2.0**授权设置**(átirányítási URL-cím). Például ha a `tenant_name` van contoso.onmicrosoft.com, állítsa be az URL-címet kell `https://login.microsoftonline.com/te/contoso.onmicrosoft.com/oauth2/authresp`.
10. Válassza ki**提交**(beküldése).  

## <a name="configure-a-weibo-account-as-an-identity-provider"></a>Identitás-szolgáltatóként Weibo fiók konfigurálása

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Úgy győződhet meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárt használja, hogy átvált rá az Azure Portal jobb felső sarkában. Jelölje ki előfizetői adatait, majd válassza a **Könyvtárváltás** lehetőséget. 

    ![Átváltás az Azure AD B2C-bérlőre](./media/active-directory-b2c-setup-weibo-app/switch-directories.png)

    Válassza ki a bérlőjét tartalmazó könyvtárt.

    ![Könyvtár kijelölése](./media/active-directory-b2c-setup-weibo-app/select-directory.png)

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *Weibo*.
6. Válassza ki **identitásszolgáltató típusa**válassza **Weibo (előzetes verzió)**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és írja be az Alkalmazáskulcs, mint korábban rögzített a **ügyfél-azonosító** , és adja meg az alkalmazás titkos kulcsát, feljegyzett a **titkos Ügyfélkód** , a korábban létrehozott Weibo alkalmazás.
8. Kattintson a **OK** majd **létrehozás** a Weibo konfigurációjának mentéséhez.
