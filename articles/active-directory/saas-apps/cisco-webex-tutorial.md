---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Cisco Webex értekezletek |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Cisco Webex értekezletek között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e4bf593473e646bbfe3d8db1df44bd48195b4cf7
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2019
ms.locfileid: "57729659"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex-meetings"></a>Oktatóanyag: Cisco Webex értekezletek az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Cisco Webex értekezletek integrálása az Azure Active Directory (Azure AD).
Cisco Webex értekezletek integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Cisco Webex értekezletek Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezve Cisco Webex értekezletek (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Cisco Webex értekezletek, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Cisco Webex értekezletek egy bejelentkezési engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Cisco Webex értekezletek támogatja **SP** által kezdeményezett egyszeri bejelentkezés

* Cisco Webex értekezletek támogatja **igény szerinti** felhasználók átadása

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Cisco Webex értekezletek hozzáadása a katalógusból

Cisco Webex értekezletek integrálása az Azure AD beállítása, hozzá kell Cisco Webex értekezletek a galériából a felügyelt SaaS-alkalmazások listájára.

**Cisco Webex értekezletek hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Cisco Webex értekezletek**válassza **Cisco Webex értekezletek** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Cisco Webex értekezletek a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Cisco Webex értekezletek nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó a Cisco Webex értekezletek hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés a Cisco Webex értekezletek tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Cisco Webex értekezletek egyszeri bejelentkezés konfigurálása](#configure-cisco-webex-meetings-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre a Cisco Webex értekezletek tesztfelhasználót](#create-cisco-webex-meetings-test-user)**  – egy megfelelője a Britta Simon Cisco Webex értekezletek, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Cisco Webex értekezletek, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Cisco Webex értekezletek** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az Azure Portalon az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszt, és töltse fel a letöltött **szolgáltató metaadatok** fájlt, és az alkalmazás konfigurálása a következő lépések végrehajtásával:

    >[!Note]
    >A Service Provider metaadatait tartalmazó fájl, a később ismertetett kap a **konfigurálása Cisco Webex értekezletek egyszeri bejelentkezés** az oktatóanyag szakaszában. 

    a. Kattintson a **metaadatfájl feltöltése**.

    ![Metaadatfájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    ![metaadatfájl kiválasztása](common/browse-upload-metadata.png)

    c. Szolgáltató metaadatfájl feltöltése sikeres befejezése után a **azonosító** és **válasz URL-cím** értékeket automatikusan az első **alapszintű SAML-konfigurációja** a szakaszban:

    ![Cisco Webex értekezletek tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier-reply.png)

    A a **bejelentkezési URL-cím** szövegmezőjébe illessze be az értéket, **válasz URL-cím** ami lekéri automatikusan kitöltött SP metaadat-fájl feltöltése.

5. Cisco Webex értekezletek alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg. Kattintson a **szerkesztése** ikonra kattintva adja hozzá az attribútumokat.

    ![image](common/edit-attribute.png)

6. Törölje az alapértelmezett attribútumok **felhasználói jogcímek** SAML-válasz a biztonsági szakaszban és a Cisco Webex értekezletek alkalmazás vár néhány további attribútumok adhatók át. A a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen a következő lépésekkel adja hozzá a SAML-jogkivonat attribútumot, ahogyan az alábbi táblázatban:
    
    | Name (Név) | Adatforrás-attribútum|
    | ---------------|  --------- |
    |   Keresztnév    | user.givenname |
    |   Vezetéknév    | user.surname |
    |   e-mail       | user.mail |
    |   egyedi azonosítója    | user.mail |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](./media/cisco-webex-tutorial/tutorial-cisco-webex-addnewclaim.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

8. Az a **beállításához a Cisco Webex értekezletek** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-cisco-webex-meetings-single-sign-on"></a>Cisco Webex értekezletek egyszeri bejelentkezés konfigurálása

1. Lépjen a [Cisco együttműködési Felhőfelügyelet](https://www.webex.com/go/connectadmin) a felügyeleti hitelesítő adataival.

2. Lépjen a **biztonsági beállítások** , és keresse meg **összevont webes egyszeri bejelentkezés konfigurálása**.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

3. Az a **összevont webes egyszeri bejelentkezés konfigurálása** hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

    a. Az összevonási protokollt szövegmezőbe írja be a protokoll nevét.

    b. Kattintson a **SAML-metaadatok importálása** az Azure Portalról letöltött metaadatfájl feltöltése mutató hivatkozást.

    c. Kattintson a **exportálása** gombra kattintva töltse le a Service Provider metaadatait tartalmazó fájl, és töltse fel a a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

    d. Az a **AuthContextClassRef** szövegmezőbe írja be `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` , és ha később engedélyezni kívánja az MFA, Azure AD-vel, írja be például a két érték `urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Válassza ki **fióklétrehozás automatikus**.

    >[!NOTE]
    >Engedélyezésének **just-in-time** felhasználókiépítés, ellenőriznie kell a **automatikus fióklétrehozás**. Emellett a SAML-jogkivonat attribútumai kell átadni a SAML-válasz.

    f. Kattintson a **Save** (Mentés) gombra. 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Cisco Webex értekezletek Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Cisco Webex értekezletek**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Cisco Webex értekezletek**.

    ![Az alkalmazások listáját a Cisco Webex értekezletek hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-cisco-webex-meetings-test-user"></a>Cisco Webex értekezletek tesztfelhasználó létrehozása

Ez a szakasz célja a Cisco Webex értekezletek Britta Simon nevű felhasználó létrehozásához. Cisco Webex értekezletek támogatja **just-in-time** kiépítést, amely alapértelmezés szerint ki van engedélyezve. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik a Cisco Webex értekezletek, amikor megpróbálja elérni a Cisco Webex értekezletek egy új jön létre.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Cisco Webex értekezletek csempére kattint, meg kell kell automatikusan bejelentkezett a Cisco Webex értekezletek, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

