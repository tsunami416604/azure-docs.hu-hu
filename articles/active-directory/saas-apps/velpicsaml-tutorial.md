---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az Velpic SAML |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Velpic SAML között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bed1c201831b1edf4f5c267d2093ada4ba91d7df
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59263315"
---
# <a name="tutorial-azure-active-directory-integration-with-velpic-saml"></a>Oktatóanyag: Az Azure Active Directory-integráció az Velpic SAML

Ebben az oktatóanyagban elsajátíthatja, hogyan Velpic SAML integrálható az Azure Active Directory (Azure AD).
Velpic SAML integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Velpic SAML Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezve Velpic SAML (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Velpic SAML, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* Velpic SAML egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja az Velpic SAML **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-velpic-saml-from-the-gallery"></a>Velpic SAML hozzáadása a katalógusból

Az Azure AD integrálása a Velpic SAML konfigurálása, hozzá kell Velpic SAML a galériából a felügyelt SaaS-alkalmazások listájára.

**Velpic SAML hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Velpic SAML**válassza **Velpic SAML** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Velpic SAML](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálása és tesztelése az Azure AD egyszeri bejelentkezés az Velpic SAML alapján nevű tesztfelhasználó **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó Velpic SAML-hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés az Velpic SAML tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Velpic SAML egyszeri bejelentkezés konfigurálása](#configure-velpic-saml-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Velpic SAML tesztfelhasználót](#create-velpic-saml-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon Velpic SAML-, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása az Velpic SAML, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Velpic SAML** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Velpic SAML tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<sub-domain>.velpicsaml.net`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Vegye figyelembe, hogy a bejelentkezési URL-cím a Velpic SAML-csapat által megadott és azonosító értéket Velpic SAML oldalán az SSO beépülő modul konfigurálásakor lesz elérhető. Az érték másolásához Velpic SAML-kérelem oldaláról, és illessze be ide kell.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **Velpic SAML beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-velpic-saml-single-sign-on"></a>Velpic SAML egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Velpic SAML vállalati hely rendszergazdaként.

2. Kattintson a **kezelés** lapra, és nyissa meg **integrációs** szakaszban kattintson a kell **beépülő modulok** gombra kattintva hozzon létre új beépülő modult a bejelentkezéshez.

    ![Beépülő modul](./media/velpicsaml-tutorial/velpic_1.png)

3. Kattintson a **"Beépülő modul hozzáadása"** gombra.
    
    ![Beépülő modul](./media/velpicsaml-tutorial/velpic_2.png)

4. Kattintson a **SAML** csempére a beépülő modul hozzáadása oldalon.
    
    ![Beépülő modul](./media/velpicsaml-tutorial/velpic_3.png)

5. Adja meg az új SAML beépülő modul nevét, majd kattintson a **"Hozzáadás"** gombra.

    ![Beépülő modul](./media/velpicsaml-tutorial/velpic_4.png)

6. Adja meg a részleteket a következők szerint:

    ![Beépülő modul](./media/velpicsaml-tutorial/velpic_5.png)

    a. Az a **neve** szövegmezőbe írja be a SAML beépülő modul nevét.

    b. Az a **kiállítójának URL-címe** szövegmezőjébe illessze be a **az Azure AD-azonosító** , átmásolja a **bejelentkezés konfigurálása** ablakot, az Azure Portalon.

    c. Az a **szolgáltató metaadatok Config** töltse fel a metaadatainak XML-fájlt, amely az Azure Portalról letöltött.

    d. Azt is beállíthatja az SAML engedélyezése csak az idő-kiépítés engedélyezése a **"Automatikus létrehozása az új felhasználók"** jelölőnégyzetet. Ha egy felhasználó Velpic nem létezik, és ez a jelző nincs engedélyezve, az Azure-ból nem fog tudni bejelentkezni. Ha a jelző engedélyezve van a felhasználó automatikusan kiépítheti Velpic történő bejelentkezés alkalmával. 

    e. Másolás a **egyszeri bejelentkezési URL** szövegből mezőbe, és illessze be az Azure Portalon.
    
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
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Velpic SAML Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Velpic SAML**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Velpic SAML**.

    ![Az alkalmazások listáját a Velpic SAML hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-velpic-saml-test-user"></a>Velpic SAML tesztfelhasználó létrehozása

Ezt a lépést általában nem kötelező, csak az idő felhasználókiépítés az alkalmazás támogatja. Ha nincs engedélyezve a felhasználók automatikus átadása majd a felhasználó manuális létrehozása hajtható végre az alább ismertetett.

Jelentkezzen be rendszergazdaként vállalati Velpic SAML webhelyét, és hajtsa végre a következő lépéseket:
    
1. Kattintson a kezelés lapon, majd nyissa meg a felhasználók szakaszban kattintson az új felhasználók hozzáadása gombra.

    ![Felhasználó hozzáadása](./media/velpicsaml-tutorial/velpic_7.png)

2. Az a **"Az új felhasználó létrehozása"** párbeszédpanel lapon, a következő lépésekkel.

    ![Felhasználó](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Az a **Utónév** szövegmezőbe írja be az első nevére Britta.

    b. Az a **Vezetéknév** szövegmezőbe írja be a Legutóbbi Simon nevét.

    c. Az a **felhasználónév** szövegmezőbe írja be a felhasználónevet Britta Simon.

    d. Az a **E-mail** szövegmezőbe írja be az e-mail-címe Brittasimon@contoso.com fiókot.

    e. Az adatok a REST használata nem kötelező, szükség esetén kitöltheti.
    
    f. Kattintson a **SAVE** (Mentés) gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

1. Ha a hozzáférési panelen a Velpic SAML csempére kattint, bejelentkezési oldal Velpic SAML-kérelem szerezheti be. Megtekintheti a **"Bejelentkezés az Azure ad-ben"** gomb a bejelentkezési oldalon.

    ![Beépülő modul](./media/velpicsaml-tutorial/velpic_6.png)

1. Kattintson a **"Bejelentkezés az Azure ad-ben"** gombra kattintva jelentkezzen be az Azure AD-fiókot használó Velpic.

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

