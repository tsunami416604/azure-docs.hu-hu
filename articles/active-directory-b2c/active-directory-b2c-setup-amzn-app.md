---
title: Regisztráció és bejelentkezés a az Azure Active Directory B2C fiókkal Amazon beállítása |} A Microsoft Docs
description: Adja meg a regisztráció és bejelentkezés az alkalmazásokban az Azure Active Directory B2C használatával Amazon-fiókkal rendelkező ügyfelek számára.
services: active-directory-b2c
author: davidmu1
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/06/2018
ms.author: davidmu
ms.component: B2C
ms.openlocfilehash: c64b32656db2d3b821833450b4e866b9e33e44cd
ms.sourcegitcommit: 0c64460a345c89a6b579b1d7e273435a5ab4157a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/31/2018
ms.locfileid: "43337345"
---
# <a name="set-up-sign-up-and-sign-in-with-an-amazon-account-using-azure-active-directory-b2c"></a>Regisztráció és bejelentkezés a az Azure Active Directory B2C fiókkal Amazon beállítása

## <a name="create-an-amazon-application"></a>Amazon-alkalmazás létrehozása

Amazon fiók használatára az identitásszolgáltatójaként az Azure Active Directory (Azure AD) B2C-ben, hozzon létre egy alkalmazást a bérlőben, hogy az azt jelölő kell. Ha még nem rendelkezik egy Amazon-fiókkal beszerezheti a [ http://www.amazon.com/ ](http://www.amazon.com/).

1. Jelentkezzen be a [Amazon fejlesztői központ](https://login.amazon.com/) Amazon-fiókja hitelesítő adataival.
2. Ha még nem tette meg, kattintson a **regisztráció**, hajtsa végre a fejlesztői regisztrációs lépéseket, és fogadja el a szabályzatot.
3. Válassza ki **új alkalmazás regisztrálása**.
4. Adjon meg egy **neve**, **leírás**, és **adatvédelmi nyilatkozat URL-címe**, és kattintson a **mentése**.
5. Az a **Webbeállításainak** területén, az értékek másolásához **ügyfél-azonosító**. Válassza ki **Show Secret** az ügyfél titkos kulcsát, és másolja azt. Mindkettő konfigurálásához Amazon-fiókkal a bérlőben Identitásszolgáltatóként van szüksége. **Titkos Ügyfélkód** egy fontos biztonsági hitelesítő adat.
6. Az a **Webbeállításainak** szakaszban jelölje be **szerkesztése**, majd adja meg `https://{tenant}.b2clogin.com` a **JavaScript engedélyezett eredetek** és `https://{tenant}.b2clogin.com/te/{tenant}.onmicrosoft.com/oauth2/authresp` a **engedélyezett URL-címek vissza**. Cserélje le **{tenant}** a bérlő nevét (például: contosob2c). 
7. Kattintson a **Save** (Mentés) gombra.

## <a name="configure-an-amazon-account-as-an-identity-provider"></a>Amazon-fiókkal konfigurálása identitás-szolgáltatóként

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) az Azure AD B2C-bérlő globális rendszergazdájaként.
2. Úgy győződhet meg arról, hogy az Azure AD B2C-bérlőt tartalmazó könyvtárt használja, hogy átvált rá az Azure Portal jobb felső sarkában. Jelölje ki előfizetői adatait, majd válassza a **Könyvtárváltás** lehetőséget. 

    ![Átváltás az Azure AD B2C-bérlőre](./media/active-directory-b2c-setup-fb-app/switch-directories.png)

    Válassza ki a bérlőjét tartalmazó könyvtárt.

    ![Könyvtár kijelölése](./media/active-directory-b2c-setup-fb-app/select-directory.png)

3. Válassza az Azure Portal bal felső sarkában található **Minden szolgáltatás** lehetőséget, majd keresse meg és válassza ki az **Azure AD B2C**-t.
4. Válassza ki **Identitásszolgáltatók**, majd válassza ki **Hozzáadás**.
5. Adjon meg egy **neve**. Adja meg például *Amazon*.
6. Válassza ki **identitásszolgáltató típusa**válassza **Amazon**, és kattintson a **OK**.
7. Válassza ki **az identitásszolgáltató beállítása** , és adja meg az ügyfél-Azonosítót, mint korábban rögzített a **ügyfél-azonosító** , és adja meg a titkos Ügyfélkulcsot, feljegyzett a **titkos Ügyfélkód**az Amazon-alkalmazás, amelyet korábban hozott létre.
8. Kattintson a **OK** majd **létrehozás** az Amazon-konfiguráció mentéséhez.

