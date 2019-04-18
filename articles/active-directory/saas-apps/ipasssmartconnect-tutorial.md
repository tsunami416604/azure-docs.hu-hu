---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező iPass SmartConnect |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és iPass SmartConnect között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: bf8c7e76aa6dd69cc505be237e47d8e10e7e8db5
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59264862"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező iPass SmartConnect

Ebben az oktatóanyagban elsajátíthatja, hogyan iPass SmartConnect integrálása az Azure Active Directory (Azure AD).
IPass SmartConnect integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá iPass SmartConnect Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve iPass SmartConnect (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása iPass SmartConnect, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* iPass SmartConnect egyszeri bejelentkezéses engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* iPass SmartConnect támogatja **SP és IDP** által kezdeményezett egyszeri bejelentkezés
* iPass SmartConnect támogatja **igény szerinti** felhasználók átadása

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>IPass SmartConnect hozzáadása a katalógusból

Az Azure AD integrálása a iPass SmartConnect konfigurálásához hozzá kell iPass SmartConnect a katalógusból a felügyelt SaaS-alkalmazások listájára.

**IPass SmartConnect hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **iPass SmartConnect**, jelölje be **iPass SmartConnect** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![az eredmények listájában SmartConnect iPass](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SmartConnect alapján nevű tesztfelhasználó iPass **Britta Simon**.
Az egyszeri bejelentkezés működéséhez egy Azure AD-felhasználót és a kapcsolódó felhasználó iPass hivatkozás kapcsolata SmartConnect kell létrehozni.

Az Azure AD egyszeri bejelentkezés az iPass SmartConnect tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[IPass SmartConnect egyszeri bejelentkezés konfigurálása](#configure-ipass-smartconnect-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[IPass SmartConnect tesztfelhasználó létrehozása](#create-ipass-smartconnect-test-user)**  – egy megfelelője a Britta Simon iPass SmartConnect, amely kapcsolódik az Azure AD felhasználói ábrázolása a rendelkeznie.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés iPass SmartConnect, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **iPass SmartConnect** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, a felhasználónak nincs bármely lépése végrehajtani, mert az alkalmazás már előre integrálva van az Azure-ral.

    ![iPass SmartConnect tartomány és URL-címek egyszeri bejelentkezési adatait](common/preintegrated.png)

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![iPass SmartConnect tartomány és URL-címek egyszeri bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címe:  `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

6. iPass SmartConnect alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

7. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen, a jogcímek szerkesztése használatával **Szerkesztés ikon** , vagy adja hozzá a jogcímek használatával **hozzáadása új jogcímet**SAML-jogkivonat attribútum beállítása, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Name (Név) |  Adatforrás-attribútum|
    | ---------------| ----------|
    | Keresztnév | user.givenname |
    | Vezetéknév | user.surname |
    | e-mail | user.userprincipalname |
    | felhasználónév | user.userprincipalname |
    | | |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

8. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

9. Az a **iPass SmartConnect beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-ipass-smartconnect-single-sign-on"></a>IPass SmartConnect egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálása **iPass SmartConnect** oldalon kell küldenie a letöltött **összevonási metaadatainak XML** és az Azure Portalról másolt URL-címek megfelelő [iPass SmartConnect támogatási csoport](mailto:help@ipass.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

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

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezés iPass SmartConnect való hozzáférést.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **iPass SmartConnect**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **iPass SmartConnect**.

    ![A iPass SmartConnect hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-ipass-smartconnect-test-user"></a>IPass SmartConnect tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű iPass SmartConnect hoz létre. Együttműködve [iPass SmartConnect támogatási csoportjának](mailto:help@ipass.com) hozzáadása a felhasználók és a tartományt, amely a iPass SmartConnect platform az engedélyezéshez van szükség. Ha a tartomány a csapata által hozzáadott, a iPass SmartConnect platform automatikusan kiépítve felhasználók. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

**Az SP által kezdeményezett flow-ban az alkalmazás teszteléséhez hajtsa végre az alábbi lépéseket:**

a. Töltse le a windows iPass SmartConnect ügyfél [Itt](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![A iPass SmartConnect hivatkozásra az alkalmazások listáját](./media/ipasssmartconnect-tutorial/testing3.png)

b. Az ügyfél és az indítási telepítése.

c. Kattintson a **Ismerkedés**.

![A iPass SmartConnect hivatkozásra az alkalmazások listáját](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Adja meg a tartományt az Azure-beli felhasználónevével. Kattintson a **továbbra is**. Ez az Azure bejelentkezési oldalára irányítja

![A iPass SmartConnect hivatkozásra az alkalmazások listáját](./media/ipasssmartconnect-tutorial/testing2.png) 

e. Sikeres hitelesítés után az ügyfél aktiválási fog elindulni. Ügyfél fog aktiválni.

**Az identitásszolgáltató által kezdeményezett flow-ban az alkalmazás teszteléséhez hajtsa végre az alábbi lépéseket:**

a. Jelentkezzen be [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

b. Kattintson a iPass SmartConnect alkalmazást.

c. Elindul SSA lap, kattintson a **Windows letöltése alkalmazás** iPass SmartConnect ügyfél telepítéséhez.

![A iPass SmartConnect hivatkozásra az alkalmazások listáját](./media/ipasssmartconnect-tutorial/testing4.png)

d. Után az alkalmazás első indításakor az ügyfél a telepítés automatikusan elindul az aktiválási feltételek és kikötések elfogadása után.

e. Ha az aktiválás nem indul el, kattintson a SSA lapon elindíthatja az aktiválás gombra.

f. Ügyfél fog aktiválni.

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)