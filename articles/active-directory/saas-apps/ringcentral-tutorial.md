---
title: 'Oktatóanyag: Azure Active Directory-integráció az RingCentral |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és RingCentral között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 35033e52fb54177428f8869ebcc462bd9465ad4c
ms.sourcegitcommit: 0bb8db9fe3369ee90f4a5973a69c26bff43eae00
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2018
ms.locfileid: "48871630"
---
# <a name="tutorial-azure-active-directory-integration-with-ringcentral"></a>Oktatóanyag: Azure Active Directory-integráció az RingCentral

Ebben az oktatóanyagban elsajátíthatja, hogyan RingCentral integrálása az Azure Active Directory (Azure AD).

RingCentral integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá RingCentral Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett RingCentral (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

RingCentral az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy RingCentral egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. RingCentral hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-ringcentral-from-the-gallery"></a>RingCentral hozzáadása a katalógusból
Az Azure AD-be RingCentral integráció konfigurálásához, hozzá kell RingCentral a galériából a felügyelt SaaS-alkalmazások listájára.

**RingCentral hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![image](./media/ringcentral-tutorial/selectazuread.png)

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![image](./media/ringcentral-tutorial/a_select_app.png)
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![image](./media/ringcentral-tutorial/a_new_app.png)

4. A Keresés mezőbe írja be a **RingCentral**válassza **RingCentral** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![image](./media/ringcentral-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés RingCentral a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó RingCentral mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó RingCentral hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az RingCentral tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy RingCentral tesztfelhasználót](#create-a-ringcentral-test-user)**  – egy megfelelője a Britta Simon RingCentral, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és RingCentral alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés RingCentral, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com/), az a **RingCentral** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![image](./media/ringcentral-tutorial/b1_b2_select_sso.png)

2. Kattintson a **egyszeri bejelentkezési mód megváltoztatása** felett válassza ki a képernyő a **SAML** mód.

      ![image](./media/ringcentral-tutorial/b1_b2_saml_ssso.png)

3. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![image](./media/ringcentral-tutorial/b1_b2_saml_sso.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![image](./media/ringcentral-tutorial/b1-domains_and_urlsedit.png)

5. Az a **alapszintű SAML-konfigurációja** szakaszt, ha rendelkezik **szolgáltató metaadatait tartalmazó fájl**, hajtsa végre az alábbi lépéseket:

    a. Kattintson a **metaadatfájl feltöltése**.

    ![image](./media/ringcentral-tutorial/b9_saml.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    ![image](./media/ringcentral-tutorial/b9(1)_saml.png)

    c. A metaadatfájl sikeres feltöltését követően a **azonosító** és **válasz URL-cím** értékeket automatikusan az első **alapszintű SAML-konfigurációja** szövegmező szakaszban, ahogy az alábbi :

    ![image](./media/ringcentral-tutorial/b21-domains_and_urls.png)

    d. Az a **bejelentkezési URL-cím** szövegmezőbe írja be egy URL-címe:
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    > [!NOTE]
    > Megjelenik a **szolgáltató metaadatait tartalmazó fájl** a az oktatóanyag későbbi részében ismertetett RingCentral egyszeri bejelentkezési konfiguráció lapon.

6. Ha nem rendelkezik **szolgáltató metaadatait tartalmazó fájl**, hajtsa végre az alábbi lépéseket:

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be egy URL-címe:
    | |
    |--|
    | `https://service.ringcentral.com` |
    | `https://service.ringcentral.com.au` |
    | `https://service.ringcentral.co.uk` |
    | `https://service.ringcentral.eu` |

    b. Az a **azonosító** szövegmezőbe írja be egy URL-címe:
    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    c. Az a **válasz URL-cím** szövegmezőbe írja be egy URL-címe:
    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

    ![image](./media/ringcentral-tutorial/b2-domains_and_urls.png)

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** töltheti le a tanúsítvány megfelelően a megadott beállításokat a követelmény, és mentse a számítógépre.

    ![image](./media/ringcentral-tutorial/certificatebase64.png)
    
8. Egy másik böngészőablakban, jelentkezzen be egy biztonsági rendszergazdai, RingCentral.

9. Az a képernyő felső részén kattintson a **eszközök**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

10. Navigáljon a **egyszeri bejelentkezési**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

11. Az a **egyszeri bejelentkezés** lap **egyszeri bejelentkezési konfiguráció** szakaszban a **1. lépés** kattintson **szerkesztése** , és hajtsa végre az alábbi lépéseket:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

12. Az a **egyszeri bejelentkezés beállítása** lapon, a következő lépésekkel:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Kattintson a **Tallózás** a metaadatait tartalmazó fájl, amely az Azure Portalról letöltött feltölteni.

    b. Metaadatok feltöltése után az értékek első automatikusan kitölti a **SSO általános információkat** szakaszban.

    c. A **attribútumleképzés** szakaszban jelölje be **térkép E-mail attribútumot** , `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Kattintson a **Save** (Mentés) gombra.

    e. A **2. lépés** kattintson **letöltése** letöltéséhez a **szolgáltató metaadatait tartalmazó fájl** , és töltse fel a **alapszintű SAML-konfigurációja** szakasz automatikus polulate a **azonosító** és **válasz URL-cím** értékeket az Azure Portalon.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Ugyanazon az oldalon keresse meg **SSO engedélyezése** szakaszt, és hajtsa végre az alábbi lépéseket:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    a. Válassza ki **egyszeri bejelentkezési szolgáltatás engedélyezése**.
    
    b. Válassza ki **engedélyezése a felhasználók számára, hogy jelentkezzenek be egyszeri bejelentkezés vagy RingCentral hitelesítő**.

    c. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![image](./media/ringcentral-tutorial/d_users_and_groups.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![image](./media/ringcentral-tutorial/d_adduser.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![image](./media/ringcentral-tutorial/d_userproperties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-ringcentral-test-user"></a>RingCentral tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű RingCentral hoz létre. Együttműködve [RingCentral ügyfél-támogatási csapatának](https://success.ringcentral.com/RCContactSupp) a felhasználók hozzáadása az RingCentral platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés RingCentral Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![image](./media/ringcentral-tutorial/d_all_applications.png)

2. Az alkalmazások listájában jelölje ki a **RingCentral**.

    ![image](./media/ringcentral-tutorial/d_all_proapplications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![image](./media/ringcentral-tutorial/d_leftpaneusers.png)

4. Válassza ki a **Hozzáadás** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![image](./media/ringcentral-tutorial/d_assign_user.png)

4. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

5. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a RingCentral csempére kattint, meg kell lekérése automatikusan bejelentkezett az RingCentral alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ringcentral-tutorial/tutorial_general_01.png
[2]: ./media/ringcentral-tutorial/tutorial_general_02.png
[3]: ./media/ringcentral-tutorial/tutorial_general_03.png
[4]: ./media/ringcentral-tutorial/tutorial_general_04.png

[100]: ./media/ringcentral-tutorial/tutorial_general_100.png

[200]: ./media/ringcentral-tutorial/tutorial_general_200.png
[201]: ./media/ringcentral-tutorial/tutorial_general_201.png
[202]: ./media/ringcentral-tutorial/tutorial_general_202.png
[203]: ./media/ringcentral-tutorial/tutorial_general_203.png

