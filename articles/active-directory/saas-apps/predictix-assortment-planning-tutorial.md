---
title: 'Oktatóanyag: Predictix növekedett tervezése az Azure Active Directory-integráció |} A Microsoft Docs'
description: Ebben az oktatóanyagban elsajátíthatja fog konfigurálása egyszeri bejelentkezéshez Predictix növekedett tervezési és Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 37e686ff-f8e5-40b1-9d7e-f64b076917b7
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: c14362a220cc60b0b8956ca41950faecd0563b3e
ms.sourcegitcommit: 67625c53d466c7b04993e995a0d5f87acf7da121
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2019
ms.locfileid: "65890626"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-assortment-planning"></a>Oktatóanyag: Predictix növekedett tervezése az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja a Predictix növekedett tervezési integrálása az Azure Active Directory (Azure AD) lesz.
Ez az integráció ezeket az előnyöket biztosítja:

* Az Azure AD-szabályozza, ki férhet hozzá Predictix növekedett tervezési is használhatja.
* Engedélyezheti a felhasználók számára, hogy automatikusan jelentkezzenek be a Predictix növekedett tervezési (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen kezelheti: az Azure Portalon.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Predictix növekedett tervezésével, szüksége lesz:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókot](https://azure.microsoft.com/pricing/free-trial/).
* Predictix növekedett tervezési-előfizetés, amely egyszeri bejelentkezés engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban fogja konfigurálni, és egy tesztelési környezetben az Azure AD egyszeri bejelentkezés tesztelése.

* Predictix növekedett tervezési támogatja az SP által kezdeményezett egyszeri Bejelentkezést.

## <a name="add-predictix-assortment-planning-from-the-gallery"></a>Predictix növekedett tervezési hozzáadása a katalógusból

Predictix növekedett tervezési integrálása az Azure AD beállításához, hozzá kell Predictix növekedett tervezése a galériából a felügyelt SaaS-alkalmazások listájára.

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Lépjen a **vállalati alkalmazások** > **minden alkalmazás**:

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Egy alkalmazás hozzáadásához válassza **új alkalmazás** az ablak tetején:

    ![Válassza ki az új alkalmazás](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Predictix növekedett tervezési**. Válassza ki **Predictix növekedett tervezési** a keresési eredmények, és válassza ki a **Hozzáadás**.

     ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban fog konfigurálása és tesztelése az Azure AD egyszeri bejelentkezés Predictix növekedett tervezésének Britta Simon nevű tesztfelhasználó használatával.
Egyszeri bejelentkezés engedélyezéséhez szüksége egy Azure AD-felhasználót és a megfelelő felhasználó közötti kapcsolat létrehozására Predictix növekedett megtervezésekor.

Az Azure AD egyszeri bejelentkezés Predictix növekedett tervezésének tesztelése és konfigurálása, szüksége a lépések elvégzéséhez:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  a felhasználók számára a funkció engedélyezéséhez.
2. **[Predictix növekedett tervezési egyszeri bejelentkezés konfigurálása](#configure-predictix-assortment-planning-single-sign-on)**  az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  ahhoz, hogy az Azure AD egyszeri bejelentkezés a felhasználó számára.
5. **[Hozzon létre egy Predictix növekedett tervezési tesztfelhasználót](#create-a-predictix-assortment-planning-test-user)**  , amely kapcsolódik a felhasználó Azure ad-ben ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása Predictix növekedett tervezésével, hajtsa végre ezeket a lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Predictix növekedett tervezési** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**:

    ![Válassza ki az egyszeri bejelentkezés](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válasszon **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése:

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon válassza ki a **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** párbeszédpanelen töltse ki az alábbi lépéseket.

    ![Alapszintű SAML-konfigurációja párbeszédpanel](common/sp-identifier.png)

    1. Az a **bejelentkezési URL-cím** mezőbe írjon be egy URL-címe ebben a mintában:

       | |
        |--|
        | `https://<sub-domain>.ap.predictix.com/sso/request`|
        | `https://<sub-domain>.dev.ap.predictix.com/`|
        | |

    1. Az a **azonosító (entityid)** mezőbe írjon be egy URL-címe ebben a mintában:

        | |
        |--|
        | `https://<sub-domain>.ap.predictix.com`|
        | `https://<sub-domain>.dev.ap.predictix.com`|
        | |

    > [!NOTE]
    > Ezeket az értékeket a helyőrzők. Szeretné használni, a tényleges bejelentkezési URL-cím és azonosító. Forduljon a [ügyfélszolgálathoz Predictix növekedett tervezési](https://www.infor.com/support) értékének lekéréséhez. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** párbeszédpanel az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** szakaszban jelölje be a **letöltése** mellett kapcsolni **tanúsítvány (Base64)** , a igényeknek, és mentse el a tanúsítványt a számítógépen:

    ![Tanúsítvány letöltésére szolgáló hivatkozásra.](common/certificatebase64.png)

6. Az a **Predictix növekedett tervezési beállítása** területén másolja a megfelelő URL-címeket, a követelmények alapján:

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL-cím**.

    1. **Az Azure AD-azonosító**.

    1. **Kijelentkezési URL-címe**.

### <a name="configure-predictix-assortment-planning-single-sign-on"></a>Predictix növekedett tervezési egyszeri bejelentkezés konfigurálása

A Predictix növekedett tervezési oldalon konfigurálása egyszeri bejelentkezéshez, kell küldenie a letöltött tanúsítvány és az Azure Portalról másolt URL-címeket a [Predictix növekedett tervezési támogatási csapatával](https://www.infor.com/support). Ez a csapat biztosítja, hogy a SAML SSO-kapcsolat mindkét oldalán megfelelően beállítva.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon Britta Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portalon válassza ki a **Azure Active Directory** a bal oldali panelen válassza ki a **felhasználók**, majd válassza ki **minden felhasználó**:

    ![Válassza ki az összes felhasználó](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején:

    ![Válassza ki az új felhasználó](common/new-user.png)

3. Az a **felhasználói** párbeszédpanel mezőbe az alábbi lépéseket.

    ![Felhasználói párbeszédpanel](common/user-properties.png)

    1. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    1. Az a **felhasználónév** mezőbe írja be **BrittaSimon @\<vállalati_tartomány >.\< bővítmény >**. (Például BrittaSimon@contoso.com.)

    1. Válassza ki **Show jelszó**, és jegyezze fel az értéket, amely szerepel a **jelszó** mezőbe.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon Predictix növekedett tervezési számára a hozzáférés biztosításával az Azure AD egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd válassza ki **Predictix növekedett tervezési**.

    ![Vállalati alkalmazások](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Predictix növekedett tervezési**.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza ki **felhasználó hozzáadása**, majd válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza ki **Britta Simon** a felhasználók listában, és kattintson a **válassza** gombra a képernyő alján.

6. Ha a SAML-előfeltétel szerepkör értéket a várt a **Szerepkörválasztás** párbeszédpanelen jelölje ki a megfelelő szerepkört a felhasználóhoz a listából. Kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen jelölje ki **hozzárendelése**.

### <a name="create-a-predictix-assortment-planning-test-user"></a>Predictix növekedett tervezési tesztfelhasználó létrehozása

Ezután szüksége Britta Simon Predictix növekedett tervezési nevű felhasználó létrehozásához. Együttműködik a [ügyfélszolgálathoz Predictix növekedett tervezési](https://www.infor.com/support) adhat hozzá felhasználókat. Felhasználók létrehozása és egyszeri bejelentkezés használata előtt aktiválni kell.

> [!NOTE]
> Az Azure ad-ben fióktulajdonos kap egy e-mailt, és mielőtt aktívvá válik, győződjön meg arról, hogy a fiók mutató hivatkozást választja.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Most szüksége az Azure AD egyszeri bejelentkezési konfigurációjának tesztelése a hozzáférési Panel használatával.

A Predictix növekedett tervezési csempe kiválasztásakor a hozzáférési panelen azt kell automatikusan megtörténik a a Predictix növekedett tervezési példányhoz, amelyhez beállítani az egyszeri bejelentkezés Konfigurálásához. További információkért lásd: [alkalmazások használatának és elérésének a saját alkalmazások portál](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory számára oktatóanyagokkal](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)