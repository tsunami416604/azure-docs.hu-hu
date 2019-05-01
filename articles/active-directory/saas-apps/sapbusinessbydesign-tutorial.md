---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az SAP Business Bydesignnal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az SAP Business bydesign megoldással között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 23669671c9aec2ebad8e03e06a0ea1b139214cad
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2019
ms.locfileid: "64683851"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Oktatóanyag: Az Azure Active Directory-integráció az SAP Business Bydesignnal

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható az SAP Business bydesign megoldással az Azure Active Directoryval (Azure AD).
SAP Business bydesign megoldással integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá az SAP Business bydesign megoldással az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett az SAP Business bydesign megoldással (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az SAP Business Bydesignnal konfigurálásához lesz szüksége a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a [ingyenes fiókkal](https://azure.microsoft.com/free/)
* SAP Business bydesign megoldással egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* SAP Business bydesign megoldással támogatja **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>SAP Business bydesign megoldással hozzáadása a katalógusból

Konfigurálja az integráció az SAP Business bydesign megoldással az Azure AD-be, szüksége a katalógus az SAP Business bydesign megoldással hozzáadása a felügyelt SaaS-alkalmazások listájában.

**SAP Business bydesign megoldással hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **SAP Business bydesign megoldással**válassza **SAP Business bydesign megoldással** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![SAP Business bydesign megoldással a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SAP Business Bydesignnal nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és az SAP Business bydesign megoldással a kapcsolódó felhasználó közötti kapcsolat kapcsolatot kell hozható létre.

Az Azure AD egyszeri bejelentkezés az SAP Business Bydesignnal tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[SAP Business bydesign megoldással egyszeri bejelentkezés konfigurálása](#configure-sap-business-bydesign-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[SAP Business bydesign megoldással tesztfelhasználó létrehozása](#create-sap-business-bydesign-test-user)**  - a-megfelelője a Britta Simon rendelkezik az SAP Business bydesign megoldással, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezés az SAP Business Bydesignnal, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **SAP Business bydesign megoldással** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SAP Business bydesign megoldással tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<servername>.sapbydesign.com`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [SAP Business bydesign megoldással ügyfél-támogatási csapatának](https://www.sap.com/products/cloud-analytics.support.html) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. SAP Business bydesign megoldással alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

6. Kattintson a **szerkesztése** ikonra kattintva szerkessze a **azonosító értékét adja**.

    ![image](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. Az a **kezelheti a felhasználói jogcímek** szakaszban, a következő lépésekkel: ![kép](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Válassza ki **átalakítási** , egy **forrás**.

    b. Az a **átalakítási** legördülő listában válassza **ExtractMailPrefix()**.

    c. Az a **paraméter 1** legördülő listára, válassza ki a példányhoz használni kívánt felhasználói attribútum. Például ha az attribútum értékét a ExtensionAttribute2 tárolt az EmployeeID használandó egyedi felhasználói azonosítóként, majd válassza ki user.extensionattribute2.

    d. Kattintson a **Save** (Mentés) gombra.

8. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

9. Az a **beállítása az SAP Business bydesign megoldással** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-sap-business-bydesign-single-sign-on"></a>SAP Business bydesign megoldással egyszeri bejelentkezés konfigurálása

1. Jelentkezzen be rendszergazdai jogosultságokkal az SAP Business bydesign megoldással portálon.

2. Navigáljon a **alkalmazás- és felhasználókezelési gyakori feladatot** , és kattintson a **identitásszolgáltató** fülre.

3. Kattintson a **új identitásszolgáltató** , és válassza ki az Azure Portalról letöltött metaadatainak XML-fájl. Importálja a metaadatokat, a rendszer automatikusan feltölti a szükséges aláírási tanúsítvány és a titkosítási tanúsítványt.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Tartalmazza a **helyességi feltétel fogyasztói URL-címe** válassza ki azokat az SAML-kérelmet **helyességi feltétel fogyasztói szolgáltatás URL-címek**.

5. Kattintson a **egyszeri bejelentkezés aktiválása**.

6. Mentse a módosításokat.

7. Kattintson a **saját rendszer** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. Az a **Azure AD bejelentkezési URL-cím** szövegmezőjébe illessze be **bejelentkezési URL-cím** érték, amely az Azure Portalról másolta.

    ![Egyszeri bejelentkezés konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Adja meg, hogy az alkalmazott manuálisan választhatnak, hogy naplózás a felhasználói azonosító és jelszó vagy az SSO kiválasztásával **manuális Identity Provider kijelölés**.

10. Az a **egyszeri bejelentkezési URL-cím** szakaszban adja meg az URL-cím, az alkalmazott, a rendszer a bejelentkezés által használandó.
    Az az URL-cím küldött alkalmazott legördülő listából választhat a következő beállításokat:

    **Nem egyszeri bejelentkezési URL-címe**

    A rendszer csak a normál rendszer URL-CÍMÉT az alkalmazott küld. Az alkalmazott frissítsen SSO, használatával nem kell és használja a jelszót, vagy inkább tanúsítvány is.

    **EGYSZERI BEJELENTKEZÉSI URL-CÍME**

    A rendszer elküldi az alkalmazott csak az egyszeri bejelentkezési URL-címet. Az alkalmazott frissítsen SSO használatával is. Hitelesítési kérelem az identitásszolgáltató keresztül lesz átirányítva.

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
  
    b. Az a **felhasználónév** mezőbe írja be `brittasimon@yourcompanydomain.extension`. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezést az SAP Business bydesign megoldással való hozzáférés biztosítása használja.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **SAP Business bydesign megoldással**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **SAP Business bydesign megoldással**.

    ![Az alkalmazások listáját az SAP Business bydesign megoldással hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-sap-business-bydesign-test-user"></a>SAP Business bydesign megoldással tesztfelhasználó létrehozása

Ebben a szakaszban egy SAP Business bydesign megoldással Britta Simon nevű felhasználói hoz létre. Együttműködve [SAP Business bydesign megoldással ügyfél-támogatási csapatának](https://www.sap.com/products/cloud-analytics.support.html) a felhasználók hozzáadása az SAP Business bydesign megoldással platformon. 

> [!NOTE]
> Győződjön meg arról, hogy NameID értékének egyeznie kell a felhasználónév mező az SAP Business bydesign megoldással platformon.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen az SAP Business bydesign megoldással csempére kattint, meg kell lehet automatikusan bejelentkezett, amelynek beállítása egyszeri Bejelentkezést az SAP Business bydesign megoldással. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
