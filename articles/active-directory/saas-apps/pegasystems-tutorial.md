---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Pega rendszerekkel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Pega rendszerek között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 34fe5d85829d909989513214406ba96ea5be0aa8
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59271101"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Oktatóanyag: Az Azure Active Directory-integráció Pega rendszerekkel

Ebben az oktatóanyagban megismerheti, hogyan Pega rendszerek integrálását az Azure Active Directory (Azure AD).
Pega rendszerek integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Pega rendszerek Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett Pega rendszerekhez (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Pega rendszerekkel, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Pega rendszerek egy bejelentkezési engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Pega rendszerek **SP** és **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-pega-systems-from-the-gallery"></a>Pega rendszerek hozzáadása a katalógusból

Pega rendszerek integrálását az Azure AD beállítása, hozzá kell Pega rendszerek a galériából a felügyelt SaaS-alkalmazások listájára.

**Pega rendszerek hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Pega rendszerek**válassza **Pega rendszerek** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Pega rendszerek](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés alapján nevű tesztfelhasználó Pega rendszerekkel **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó Pega rendszerek közötti kapcsolat kapcsolat kell hozható létre.

Konfigurálása és az Azure AD egyszeri bejelentkezés Pega rendszerekkel való teszteléséhez hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Pega rendszerek egyszeri bejelentkezés konfigurálása](#configure-pega-systems-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Pega rendszerek tesztfelhasználót](#create-pega-systems-test-user)**  - a-megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása Pega rendszerekben.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása Pega rendszerekkel, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Pega rendszerek** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Pega rendszerek tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/sp/<INSTANCEID>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<CUSTOMERNAME>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Pega rendszerek tartomány és URL-címeket egyetlen bejelentkezési adatait](common/both-advanced-urls.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a bejelentkezési URL-cím értékét.

    b. Az a **továbbítási állapot** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<CUSTOMERNAME>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezeket az értékeket frissítse a tényleges azonosítója, válasz URL-cím, bejelentkezési URL-cím, és továbbítási állapot URL-címe. Az oktatóanyag későbbi részében ismertetett Pega alkalmazásból azonosítója és a válasz URL-cím értékeit is megtalálhatja. Továbbítási állapot, lépjen kapcsolatba a [Pega rendszerek ügyfél-támogatási csapatának](https://www.pega.com/contact-us) a gépkulcsengedélyek értékének. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

6. Pega rendszerek alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg. Kattintson a **szerkesztése** ikonra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

7. Emellett a fentiekben Pega rendszerek alkalmazás vár néhány további attribútumok vissza SAML-válasz átadni. A a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen a következő lépésekkel adja hozzá a SAML-jogkivonat attribútumot, ahogyan az alábbi táblázatban:

    | Name (Név) | Adatforrás-attribútum|
    | ------------------- | -------------------- |
    | egyedi azonosítója | *********** |
    | CN  | *********** |
    | levelezés | *********** |
    | accessgroup | *********** |
    | szervezet | *********** |
    | orgdivision | *********** |
    | orgunit | *********** |
    | Munkacsoport | *********** |
    | Telefonszám | *********** |

    > [!NOTE]
    > Ezek a vásárlói adott értékekre. Adja meg a megfelelő értékekre.

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

9. Az a **Pega rendszerek beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-pega-systems-single-sign-on"></a>Pega rendszerek egyszeri bejelentkezés konfigurálása

1. Az egyszeri bejelentkezés konfigurálása **Pega rendszerek** oldalán, nyissa meg a **Pega portál** rendszergazdai fiókkal egy másik böngészőablakban.

2. Válassza ki **létrehozása** -> **SysAdmin (rendszergazda)** -> **hitelesítési szolgáltatás**.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. A következő műveleteket végrehajtására **hitelesítési szolgáltatás-létrehozás** képernyőjén:

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    a. Válassza ki **SAML 2.0-s** típusból

    b. Az a **neve** szövegmezőbe, adja meg a bármely neve például: Azure AD egyszeri bejelentkezés

    c. Az a **rövid leírása** szövegmezőben adjon meg minden olyan leírást  

    d. Kattintson a **létrehozása, és nyissa meg** 
    
4. A **identitásszolgáltató (IdP) információt** területén kattintson a **importálás IdP-metaadatok** , és keresse meg a metaadat-fájlt, amely már letöltötte az Azure Portalról. Kattintson a **küldés** a metaadatok betöltése.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
5. Az IdP-adat Ez kitölti a lent látható módon.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. A következő műveleteket végrehajtására **Service Provider (SP) beállítások** szakaszban:

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    a. Másolás a **entitás azonosító** értékét, és illessze be a **azonosító** szövegmezőjébe a **alapszintű SAML-konfigurációja** az Azure Portalon.

    b. Másolás a **helyességi feltétel fogyasztói Service (ACS) helyen** értékét, és illessze be a **válasz URL-cím** szövegmezőjébe a **alapszintű SAML-konfigurációja** az Azure Portalon.

    c. Válassza ki **tiltsa le a kérelem-aláírás**.

7. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be brittasimon@yourcompanydomain.extension. Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Pega rendszerek Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Pega rendszerek**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Pega rendszerek**.

    ![Az alkalmazások listáját a Pega rendszerek hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-pega-systems-test-user"></a>Pega rendszerek tesztfelhasználó létrehozása

Ez a szakasz célja egy Pega rendszerekben Britta Simon nevű felhasználó létrehozásához. Együttműködve [Pega rendszerek ügyfél-támogatási csapatának](https://www.pega.com/contact-us) Pega rendszerekben felhasználók létrehozásához.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Pega rendszerek csempére kattint, akkor kell automatikusan megtörténik a a Pega rendszereken, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)