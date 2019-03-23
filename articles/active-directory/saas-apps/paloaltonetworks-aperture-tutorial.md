---
title: 'Oktatóanyag: A Palo Alto Networks - rekesze és az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Palo Alto Networks - rekesze és között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: a5ea18d3-3aaf-4bc6-957c-783e9371d0f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/19/2019
ms.author: jeedes
ms.openlocfilehash: beb84671db01020f0118502e35128387d36845d6
ms.sourcegitcommit: 223604d8b6ef20a8c115ff877981ce22ada6155a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58361421"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---aperture"></a>Oktatóanyag: A Palo Alto Networks - rekesze és az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a Palo Alto Networks - nyílás, az Azure Active Directoryval (Azure AD).
Futtató Palo Alto Networks - nyílás, az Azure AD integrálása nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Palo Alto Networks - rekesze és az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett a Palo Alto Networks - (egyszeri bejelentkezés) rekesze és az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A Palo Alto Networks - nyílás, az Azure AD-integráció konfigurálása a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Futtató Palo Alto Networks - rekesze és egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Palo Alto Networks - rekesze és **SP** és **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-palo-alto-networks---aperture-from-the-gallery"></a>Hozzáadása a Palo Alto Networks - rekesze és a katalógusból

A Palo Alto Networks - rekesze és az Azure AD-integráció konfigurálása kell hozzáadnia a Palo Alto Networks - rekesze és a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Palo Alto Networks - katalógusból, rekesze és hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Palo Alto Networks - rekesze és**, jelölje be **Palo Alto Networks - rekesze és** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Palo Alto Networks - rekesze és az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Palo Alto Networks - rekesze és a egy teszt nevű felhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés munkáját, egy Azure AD-felhasználót és a kapcsolódó felhasználó a Palo Alto Networks - hivatkozás kapcsolatának rekesze és kell létrehozni.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés a Palo Alto Networks - nyílás, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálja a Palo Alto Networks - rekesze és egyszeri bejelentkezés](#configure-palo-alto-networks---aperture-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre futtató Palo Alto Networks - rekesze és tesztfelhasználó](#create-palo-alto-networks---aperture-test-user)**  – van egy Britta Simon megfelelője a Palo Alto Networks - nyílás, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

A Palo Alto Networks - nyílás, az Azure AD egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Palo Alto Networks - rekesze és** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Futtató Palo Alto Networks - rekesze és tartomány és URL-címek egyszeri bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/metadata`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/auth`

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Futtató Palo Alto Networks - rekesze és tartomány és URL-címek egyszeri bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<subdomain>.aperture.paloaltonetworks.com/d/users/saml/sign_in`

    > [!NOTE]
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-címet. Kapcsolattartó [Palo Alto Networks - rekesze és ügyfél-támogatási csapatának](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

7. Az a **állítsa be a Palo Alto Networks - rekesze és** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-palo-alto-networks---aperture-single-sign-on"></a>Konfigurálja a Palo Alto Networks - rekesze és egyszeri bejelentkezés

1. Egy másik böngészőablakban, jelentkezzen be a Palo Alto Networks - rekesze és rendszergazdaként.

2. A felső menüsávon kattintson **beállítások**.

    ![A beállítások lapon](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_settings.png)

3. Navigáljon a **alkalmazás** szakaszban kattintson **hitelesítési** alkotnak a bal oldali menüben.

    ![A hitelesítés lap](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_auth.png)
    
4. Az a **hitelesítési** oldalon tegye a következőket:
    
    ![A hitelesítési lapon](./media/paloaltonetworks-aperture-tutorial/tutorial_paloaltonetwork_singlesignon.png)

    a. Ellenőrizze a **engedélyezése egyetlen bejelentkezési-On(Supported SSP Providers are Okta, One login)** a **egyszeri bejelentkezés** mező.

    b. Az a **identitás Szolgáltatóazonosító** szövegmezőbe, illessze be az értéket a **az Azure AD-azonosító**, az Azure Portalról másolt.

    c. Kattintson a **fájl kiválasztása** töltse fel a letöltött az Azure AD-ből a **szolgáltató Identitástanúsítványt** mező.

    d. Az a **Identity Provider egyszeri bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.

    e. Tekintse át a adatainak identitásszolgáltatót **rekesze és Info** szakaszt, és töltse le a tanúsítványt a **rekesze és kulcs** mező.

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Palo Alto Networks - rekesze és Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Palo Alto Networks - rekesze és**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Palo Alto Networks - rekesze és**.

    ![A futtató Palo Alto Networks - alkalmazásainak listájában rekesze és hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-palo-alto-networks---aperture-test-user"></a>Futtató Palo Alto Networks - rekesze és tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű Palo Alto Networks - rekesze és a felhasználó hoz létre. Együttműködve [Palo Alto Networks - rekesze és ügyfél-támogatási csapatának](https://live.paloaltonetworks.com/t5/custom/page/page-id/Support) felhasználót is hozzáadhat a a Palo Alto Networks - rekesze és platform. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

A Palo Alto Networks - rekesze és csempe a hozzáférési panelen kattintva meg kell kell automatikusan bejelentkezett a Palo Alto Networks - nyílás, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

