---
title: 'Oktatóanyag: Azure Active Directory integráció az SAP Business Bydesignnal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az SAP Business Bydesignnal között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 04/18/2019
ms.author: jeedes
ms.openlocfilehash: 6959b7e819ad906f04a18661d6bd4aa2d82f8984
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88543364"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Oktatóanyag: Azure Active Directory-integráció az SAP Business ByDesign megoldással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAP Business Bydesignnal Azure Active Directory (Azure AD) használatával.
Az SAP Business Bydesignnal és az Azure AD integrálásával a következő előnyöket nyújtja:

* Megadhatja az Azure AD-t, aki hozzáfér az SAP Business Bydesignnal.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az SAP Business Bydesignnal (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az SAP Business Bydesignnal való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* SAP Business Bydesignnal egyszeri bejelentkezésre alkalmas előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az SAP Business Bydesignnal támogatja az **SP** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>SAP Business-Bydesignnal hozzáadása a katalógusból

Az SAP Business Bydesignnal az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SAP Business Bydesignnal a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Az SAP Business Bydesignnal a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson **Azure Active Directory** ikonra.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **SAP Business bydesignnal**, válassza az **SAP Business bydesignnal** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![SAP Business Bydesignnal az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az SAP Business Bydesignnal konfigurálja és teszteli a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és az SAP Business Bydesignnal kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés az SAP Business Bydesignnal való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. Az **[Azure ad egyszeri bejelentkezésének konfigurálása](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. Az **[SAP Business Bydesignnal egyszeri bejelentkezés konfigurálása](#configure-sap-business-bydesign-single-sign-on)** – az egyes Sign-On beállítások konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)** – a Britta Simon engedélyezése az Azure ad egyszeri bejelentkezés használatára.
5. **[Hozzon létre egy SAP Business bydesignnal-teszt felhasználót](#create-sap-business-bydesign-test-user)** – hogy a Britta Simon partnere legyen a SAP Business bydesignnal, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az SAP Business Bydesignnal való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **SAP Business bydesignnal** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** ikonra az **alapszintű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![SAP Business Bydesignnal tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<servername>.sapbydesign.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<servername>.sapbydesign.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Ezekhez az értékekhez forduljon az [SAP Business Bydesignnal ügyfél-támogatási csapatához](https://www.sap.com/products/cloud-analytics.support.html) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. Az SAP Business Bydesignnal alkalmazás meghatározott formátumban várja az SAML-kijelentéseket. Konfigurálja a következő jogcímeket ehhez az alkalmazáshoz. Az attribútumok értékeit az alkalmazás-integráció lapon, a **felhasználói attribútumok** szakaszban kezelheti. Az **egyszeres Sign-On beállítása az SAML-vel** lapon kattintson a **Szerkesztés** gombra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A **név azonosító értékének**szerkesztéséhez kattintson a **Szerkesztés** ikonra.

    ![image](media/sapbusinessbydesign-tutorial/mail-prefix1.png)

7. A **felhasználói jogcímek kezelése** szakaszban hajtsa végre a következő lépéseket:  ![ rendszerkép](media/sapbusinessbydesign-tutorial/mail-prefix2.png)

    a. Válassza a **transzformáció** **forrásként**lehetőséget.

    b. Az **átalakítás** legördülő listában válassza a **ExtractMailPrefix ()** elemet.

    c. Az **1. paraméter** legördülő listában válassza ki azt a felhasználói attribútumot, amelyet a megvalósításához használni kíván. Ha például az Alkalmazottkód-et egyedi felhasználói azonosítóként szeretné használni, és az attribútum értékét a ExtensionAttribute2 tárolja, válassza a User. ExtensionAttribute2 elemet.

    d. Kattintson a **Mentés** gombra.

8. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

9. Az **SAP Business Bydesignnal beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="configure-sap-business-bydesign-single-sign-on"></a>Az SAP Business Bydesignnal egyetlen Sign-On konfigurálása

1. Jelentkezzen be az SAP Business Bydesignnal-portálra rendszergazdai jogosultságokkal.

2. Navigáljon az **alkalmazás-és felhasználói kezelés általános feladathoz** , és kattintson az **identitás-szolgáltató** fülre.

3. Kattintson az **új identitás-szolgáltató** elemre, és válassza ki a metaadatokat tartalmazó XML-fájlt, amelyet a Azure Portal letöltött. A metaadatok importálásával a rendszer automatikusan feltölti a szükséges aláírási tanúsítványt és titkosítási tanúsítványt.

    ![Egyetlen Sign-On konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)

4. Ha a **szolgáltatói szolgáltatás URL-címét** bele szeretné foglalni az SAML-kérelembe, válassza a include Request **Consumer Service URL-cím**elemet.

5. Kattintson az **egyszeri bejelentkezés aktiválása**elemre.

6. Mentse a módosításokat.

7. Kattintson a **saját rendszerek** fülre.

    ![Egyetlen Sign-On konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)

8. Az **Azure ad bejelentkezési URL-címe** szövegmezőbe illessze be a **bejelentkezési URL-cím** értéket, amelyet a Azure Portal másolt.

    ![Egyetlen Sign-On konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)

9. Adja meg, hogy az alkalmazott manuálisan is kiválaszthatja-e a felhasználói AZONOSÍTÓval és jelszóval való bejelentkezést, vagy az SSO-t a **manuális identitás-szolgáltató kijelölésével**

10. Az **egyszeri bejelentkezési URL-cím** szakaszban válassza ki azt az URL-címet, amelyet az alkalmazottnak a rendszerbe való bejelentkezéshez használnia kell.
    Az alkalmazott legördülő listára eljuttatott URL-címben a következő lehetőségek közül választhat:

    **Nem egyszeri bejelentkezéses URL-cím**

    A rendszeren csak a normál rendszerurl-cím küldhető el az alkalmazottnak. Az alkalmazott nem tud bejelentkezik az SSO használatával, és ehelyett jelszót vagy tanúsítványt kell használnia.

    **EGYSZERI BEJELENTKEZÉS URL-CÍME**

    A rendszeren csak az egyszeri bejelentkezési URL-cím küldhető el az alkalmazottnak. Az alkalmazott az SSO használatával tud bejelentkezik. A hitelesítési kérést a rendszer átirányítja a identitásszolgáltató.

    **Automatikus kijelölés**

    Ha az egyszeri bejelentkezés nem aktív, a rendszer a normál rendszerurl-címet elküldi az alkalmazottnak. Ha az egyszeri bejelentkezés aktív, a rendszer ellenőrzi, hogy az alkalmazott rendelkezik-e jelszóval. Ha a jelszó elérhető, a rendszer az egyszeri bejelentkezési URL-címet és a nem SSO URL-címet is elküldi az alkalmazottnak. Ha azonban az alkalmazottnak nincs jelszava, csak az SSO URL-címet kell elküldeni az alkalmazottnak.

11. Mentse a módosításokat.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ennek a szakasznak a célja, hogy egy teszt felhasználót hozzon létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságainál végezze el a következő lépéseket.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **nevet** `brittasimon@yourcompanydomain.extension` . Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az SAP Business Bydesignnal való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza az **SAP Business bydesignnal**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza az **SAP Business bydesignnal**elemet.

    ![Az SAP Business Bydesignnal hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listán, majd kattintson a képernyő alján található **kiválasztás** gombra.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-sap-business-bydesign-test-user"></a>SAP Business Bydesignnal-tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az SAP Business Bydesignnal-ben. Az SAP Business [Bydesignnal ügyfél-támogatási csapatával](https://www.sap.com/products/cloud-analytics.support.html) vegye fel a felhasználókat az SAP Business bydesignnal platformon. 

> [!NOTE]
> Győződjön meg arról, hogy a NameID értékének egyeznie kell az SAP Business Bydesignnal platform username mezőjével.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a SAP Business Bydesignnal csempére kattint, automatikusan be kell jelentkeznie az SAP üzleti Bydesignnal, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
