---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Five9 Plus-adaptert (PBX, ügyfél-Center-ügynökök) |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/04/2019
ms.author: jeedes
ms.openlocfilehash: f42e58ff7bf76af561dd49169c73838499718168
ms.sourcegitcommit: 031e4165a1767c00bb5365ce9b2a189c8b69d4c0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2019
ms.locfileid: "59547284"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Oktatóanyag: Az Azure Active Directory-integráció Five9 Plus-adaptert (PBX, ügyfél-Center-ügynökök)

Ebben az oktatóanyagban megismerheti, hogyan Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) integrálható az Azure Active Directoryval (Azure AD).
Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett Five9 plusz adapterhez (PBX, ügyfél-Center-ügynökök) (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Five9 Plus-adaptert (PBX, ügyfél-Center-ügynökök), a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókot](https://azure.microsoft.com/free/).
* Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) támogatja a **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) hozzáadása a katalógusból

Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) integrálásának konfigurálása az Azure AD-be, hozzá kell Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) a galériából a felügyelt SaaS-alkalmazások listájára.

**Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök)**, jelölje be **Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök)** eredmény panelen kattintson a **Hozzáadás** az alkalmazás hozzáadására szolgáló gomb.

     ![Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés adapterrel Five9 plusz (PBX, ügyfél-Center-ügynökök) nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) közötti kapcsolat kapcsolatot kell hozható létre.

Az Azure AD egyszeri bejelentkezés adapterrel Five9 plusz (PBX, ügyfél-Center-ügynökök) tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) egyszeri bejelentkezés konfigurálása](#configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) tesztfelhasználót](#create-five9-plus-adapter-cti-contact-center-agents-test-user)**  – egy megfelelője a Britta Simon Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök), amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezés adapterrel Five9 plusz (PBX, ügyfél-Center-ügynökök), hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök)** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lapon, a következő lépésekkel:

    ![Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím:
    
    |    Környezet      |       URL-cím      |
    | :-- | :-- |
    | "Five9, valamint a Microsoft Dynamics CRM Adapter" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | "Five9 plusz Zendesk adaptert" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | "Five9, valamint az ügynök asztali eszközkészlet Adapter" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím:

    |      Környezet     |      URL-cím      |
    | :--                  | :--           |
    | "Five9, valamint a Microsoft Dynamics CRM Adapter" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | "Five9 plusz Zendesk adaptert" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | "Five9, valamint az ügynök asztali eszközkészlet Adapter" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

7. Az a **Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-five9-plus-adapter-cti-contact-center-agents-single-sign-on"></a>Five9 plusz (PBX, ügyfél-Center-ügynökök) Adapter egyszeri bejelentkezés konfigurálása

1. Az egyszeri bejelentkezés konfigurálása **Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök)** oldalon kell küldenie a letöltött **Certificate(Base64)** és másolt URL-címe a megfelelő [Five9 plusz Csatoló (PBX, ügyfél-Center-ügynökök) támogatási csoport](https://www.five9.com/about/contact). Is ezenkívül SSO további konfigurálásához kövesse az alábbi lépéseket a csatoló megfelelően:

    a. "Five9 plusz ügynök asztali eszközkészlet adaptert" rendszergazdai útmutató: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. "Five9 és a Microsoft Dynamics CRM Adapter" rendszergazdai útmutató: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Five9 plusz Zendesk adaptert" rendszergazdai útmutató: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használandó Azure egyszeri bejelentkezést a hozzáférés biztosításával Five9 plusz adapterhez (PBX, ügyfél-Center-ügynökök).

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök)**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök)**.

    ![A Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Tesztfelhasználó Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) létrehozása

Ebben a szakaszban egy Britta Simon nevű Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) a felhasználó hoz létre. Együttműködve [Five9 plusz (PBX, ügyfél-Center-ügynökök) támogatás adaptercsoporton](https://www.five9.com/about/contact) a felhasználók hozzáadása a Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) platform. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva. 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Amikor rákattint az Five9 plusz Adapter (PBX, a partner Center-ügynökök csempére a hozzáférési panelen, meg kell hogy automatikusan jelentkezzenek be a Five9 plusz adapterhez (PBX, ügyfél-Center-ügynökök), amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

