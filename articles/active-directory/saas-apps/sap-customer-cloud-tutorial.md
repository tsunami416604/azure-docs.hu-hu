---
title: 'Oktatóanyag: Az ügyfél az Azure Active Directory-integráció az SAP-felhővel |} A Microsoft Docs'
description: Ismerje meg az egyszeri bejelentkezés az Azure Active Directory és az SAP-felhő között az ügyfél konfigurálása.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 60246d2ed77bb3dad9e9bd7b35b106a7c4b62ff8
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780813"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Oktatóanyag: Az ügyfél az Azure Active Directory-integráció az SAP-felhővel

Ebben az oktatóanyagban megismerheti, hogyan integrálható az SAP Cloud az Azure Active Directoryval (Azure AD-) ügyfél.
SAP-felhő integrálása az Azure AD-ügyfél nyújt a következő előnyökkel jár:

* Szabályozhatja, aki hozzáféréssel rendelkezik az SAP-felhő ügyfél Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett a SAP Cloud ügyfél (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az SAP-felhővel ügyfél, a következő elemek van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Az ügyfél egyszeri bejelentkezést az SAP Cloud előfizetés engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Az ügyfél támogatja az SAP Cloud **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Vegye fel az SAP-felhő-ügyfél a katalógusból

Ügyfél az Azure AD-be a SAP felhőalapú integráció konfigurálásához, hozzá kell az SAP Cloud ügyfél a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá az SAP Cloud ügyfél számára a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **ügyfél SAP Cloud**, jelölje be **ügyfél SAP-felhő** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában ügyfél SAP-felhő](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SAP-felhővel ügyfél nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó a SAP felhőben ügyfél közötti kapcsolat kapcsolatot kell hozható létre.

Az Azure AD egyszeri bejelentkezés az SAP-felhővel ügyfél tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az ügyfél egyszeri bejelentkezést az SAP Cloud konfigurálása](#configure-sap-cloud-for-customer-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[SAP-felhő létrehozása az ügyfél tesztfelhasználó](#create-sap-cloud-for-customer-test-user)**  – egy megfelelője a Britta Simon rendelkeznie a SAP felhőben ügyfél, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezés az SAP-felhővel ügyfél, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **ügyfél SAP-felhő** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SAP-felhő a felhasználói tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<server name>.crm.ondemand.com`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<server name>.crm.ondemand.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [ügyfél ügyfél-támogatási csapatával a SAP Cloud](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. SAP-felhő a felhasználói alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

6. Az a **felhasználói attribútumok** szakaszában a **felhasználói attribútumok & jogcímek** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    a. Kattintson a **Szerkesztés ikon** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/sap-customer-cloud-tutorial/tutorial_usermailedit.png)

    b. Válassza ki **átalakítási** , **forrás**.

    c. Az a **átalakítási** listáról válassza ki **ExtractMailPrefix()**.

    d. Az a **paraméter 1** listájához, válassza ki a példányhoz használni kívánt felhasználói attribútum.
    Például ha az attribútum értékét a ExtensionAttribute2 tárolt az EmployeeID használandó egyedi felhasználói azonosítóként, majd válassza ki user.extensionattribute2.

    e. Kattintson a **Save** (Mentés) gombra.

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

8. Az a **SAP-felhő beállítása az ügyfél** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-sap-cloud-for-customer-single-sign-on"></a>Az SAP Cloud ügyfél egyszeri bejelentkezés konfigurálása
   
1. Jelentkezzen be SAP-felhő a felhasználói portál rendszergazda jogosultsággal rendelkező.
   
2. Keresse meg a **alkalmazás- és felhasználókezelési gyakori feladatot** , és kattintson a **identitásszolgáltató** fülre.
   
3. Kattintson a **új identitásszolgáltató** , és válassza ki az Azure Portalról letöltött metaadatainak XML-fájl. Importálja a metaadatokat, a rendszer automatikusan feltölti a szükséges aláírási tanúsítvány és a titkosítási tanúsítványt.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
4. Az Azure Active Directory gyermekelemet a helyességi feltétel fogyasztói URL-címe az az SAML-kérelmet, ezért válassza a **helyességi feltétel fogyasztói szolgáltatás URL-címek** jelölőnégyzetet.
   
5. Kattintson a **egyszeri bejelentkezés aktiválása**.
   
6. Mentse a módosításokat.
   
7. Kattintson a **saját rendszer** fülre.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
8. A **Azure AD bejelentkezési URL-cím** szövegmezőjébe illessze be **bejelentkezési URL-cím** Azure Portalról másolt.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
9. Adja meg, hogy az alkalmazott manuálisan választhatnak, hogy naplózás a felhasználói azonosító és jelszó vagy az SSO kiválasztásával a **manuális Identity Provider kijelölés**.
   
10. Az a **egyszeri bejelentkezési URL-cím** szakaszban adja meg, jelentkezzen be a rendszer az alkalmazottak által használandó URL-CÍMÉT. 
    Az a **alkalmazott küldött URL-cím** listájában, a következő lehetőségek közül választhat:
   
    **Nem egyszeri bejelentkezési URL-címe**
   
    A rendszer csak a normál rendszer URL-CÍMÉT az alkalmazott küld. Az alkalmazott nem jelentkezik be egyszeri Bejelentkezést, és kell jelszó használata vagy a tanúsítvány helyett.
   
    **EGYSZERI BEJELENTKEZÉSI URL-CÍME** 
   
    A rendszer elküldi az alkalmazott csak az egyszeri bejelentkezési URL-címet. Az alkalmazott bejelentkezhet az egyszeri bejelentkezés használatával. Hitelesítési kérelem az identitásszolgáltató keresztül lesz átirányítva.
   
    **Automatikus kiválasztása**
   
    Ha egyszeri bejelentkezés nem aktív, a rendszer az alkalmazott küld a rendszer normál URL-CÍMÉT. Ha egyszeri bejelentkezés aktív, a rendszer ellenőrzi, hogy az alkalmazott rendelkezik-e a jelszó. A jelszó nem érhető el, ha egyszeri bejelentkezési URL-cím és a nem-egyszeri bejelentkezési URL-címet kapnak az alkalmazott. Azonban ha az alkalmazott nem tartozik jelszó, csak az egyszeri bejelentkezési URL-cím küld az alkalmazottnak.
   
11. Mentse a módosításokat.

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

Ebben a szakaszban engedélyezze Britta Simon használandó Azure egyszeri bejelentkezést az SAP Cloud való hozzáférés biztosítása az ügyfél által.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **ügyfél SAP Cloud**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **SAP-felhő ügyfél**.

    ![Az SAP-felhő, az alkalmazások listáját az ügyfél-hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-sap-cloud-for-customer-test-user"></a>SAP-felhő létrehozása ügyfél tesztfelhasználó számára

Ebben a szakaszban egy felhasználói Britta Simon nevű SAP felhőben ügyfél hoz létre. Együttműködve [ügyfélszolgálati csoport az SAP-felhő](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) a felhasználók hozzáadása a SAP felhőben ügyfél platform. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

> [!NOTE]
> Győződjön meg arról, hogy NameID értékének egyeznie kell a felhasználónév mező, a SAP felhőben ügyfél platform.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha rákattint az SAP-felhő a felhasználói csempe a hozzáférési panelen, meg kell automatikusan megtörténik a a SAP felhőben ügyfél, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

