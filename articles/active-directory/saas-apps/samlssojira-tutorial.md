---
title: 'Oktatóanyag: Feloldási GmbH által az Azure Active Directory-integráció a Jira SAML SSO-val |} A Microsoft Docs'
description: Ismerje meg, hogyan konfigurálása egyszeri bejelentkezéshez tartozó Jira SAML SSO és az Azure Active Directory közötti GmbH felbontása.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9994232259231014c37beae81a5de007b142c732
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56163087"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Oktatóanyag: Feloldási GmbH által az Azure Active Directory-integráció a Jira SAML SSO-val

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja a Jira SAML SSO GmbH az Azure Active Directoryval (Azure AD-) megoldás.
A Jira SAML SSO integrálása feloldási GmbH az Azure AD által nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá az SAML SSO a Jira felbontása GmbH Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve SAML SSO a Jira felbontása GmbH (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Konfigurálni az Azure AD-integrációs SAML SSO a Jira-feloldási GmbH, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Jira felbontása GmbH egyszeri bejelentkezéshez a SAML SSO előfizetés engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Jira felbontása GmbH támogatja az SAML SSO **SP** és **Identitásszolgáltató**által kezdeményezett egyszeri bejelentkezés

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Feloldási GmbH SAML SSO a Jira hozzáadását a katalógusból

Integráció konfigurálásához a Jira SAML SSO-feloldási GmbH által az Azure AD-be, hozzá kell SAML SSO a Jira felbontása GmbH a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Jira SAML SSO GmbH felbontása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **SAML SSO a Jira felbontása GmbH**, jelölje be **SAML SSO a Jira felbontása GmbH** eredmény panelen kattintson a **hozzáadása** gombra kattintva vegye fel az alkalmazást .

     ![SAML SSO a Jira felbontása GmbH, a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a SAML SSO nevű tesztfelhasználó alapján Jira felbontása GmbH **Britta Simon**.
Az egyszeri bejelentkezés működéséhez egy Azure AD-felhasználót és a kapcsolódó felhasználó a SAML SSO a Jira felbontása hivatkozás kapcsolata GmbH kell létrehozni.

Az Azure AD egyszeri bejelentkezés a Jira SAML SSO-val felbontása GmbH tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[SAML SSO a Jira feloldási GmbH egyszeri bejelentkezés konfigurálásához](#configure-saml-sso-for-jira-by-resolution-gmbh-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Jira feloldási GmbH teszt felhasználó által a SAML SSO](#create-saml-sso-for-jira-by-resolution-gmbh-test-user)**  – SAML SSO a Jira-megfelelője a Britta Simon van megoldás, amely kapcsolódik az Azure AD felhasználói ábrázolása GmbH szerint.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezés a Jira SAML SSO-val feloldási GmbH, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **SAML SSO a Jira felbontása GmbH** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett módban a következő lépésekkel:

    ![SAML SSO a Jira felbontása GmbH tartomány és URL-címek egyszeri bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![SAML SSO a Jira felbontása GmbH tartomány és URL-címek egyszeri bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-címet. Kapcsolattartó [Jira feloldási GmbH ügyfél által a SAML SSO támogatási csoportjának](https://www.resolution.de/go/support) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **állítsa be a Jira SAML SSO felbontása GmbH** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-saml-sso-for-jira-by-resolution-gmbh-single-sign-on"></a>SAML SSO a Jira feloldási GmbH egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban, jelentkezzen be a **Jira feloldási GmbH felügyeleti portál által a SAML SSO** rendszergazdaként.

2. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **bővítmények**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon1.png)

3. Ekkor megnyílik a rendszergazdai hozzáférés lap. Adja meg a **jelszó** kattintson **megerősítése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon2.png)

4. A bővítmények lapon szakasz alatt kattintson **új bővítmények keresése**. Keresés **SAML egyszeri bejelentkezés (SSO) a JIRA** kattintson **telepítése** gombra az új SAML beépülő modul telepítéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon7.png)

5. A beépülő modul telepítése elindul. Kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon8.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon9.png)

6.  Kattintson a **Kezelés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon10.png)
    
8. Kattintson a **konfigurálása** a új beépülő modul konfigurálásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon11.png)

9. A **SAML egyszeri bejelentkezés beépülő modul konfigurációs** kattintson **adja hozzá az új identitásszolgáltató** gombra kattintva adja meg a beállításokat az identitásszolgáltató.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon4.png)

10. A **válassza ki a SAML-identitásszolgáltató** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon5a.png)
 
    a. Állítsa be **Azure ad-ben** az identitásszolgáltató típusa.
    
    b. Adjon hozzá **neve** az identitásszolgáltató (például: Azure ad-ben).
    
    c. Adjon hozzá **leírás** az identitásszolgáltató (például: Azure ad-ben).
    
    d. Kattintson a **tovább**.
    
11. A **identitás-szolgáltató konfigurációjának** kattintson **tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon5b.png)

12. A **SAML-identitásszolgáltató metaadatok importálása** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon5c.png)

    a. Kattintson a **fájl betöltése** gombra, és 5. lépésében letöltött metaadatainak XML-fájlt válasszon.

    b. Kattintson a **importálás** gombra.
    
    c. Várjon rövid ideig, amíg importálása sikeres.
    
    d. Kattintson a **tovább** gombra.
    
13. A **felhasználói azonosító attribútum és -átalakítási** kattintson **tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon5d.png)
    
14. A **felhasználói létrehozáskor és frissítéskor** kattintson **Mentés & következő** beállításainak mentése.   
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon6a.png)
    
15. A **-beállítások tesztelése** kattintson **ugorja & manuális konfigurálása** ugorja most a felhasználó. Ez a következő szakaszban történik, és bizonyos beállítások az Azure Portalon igényel. 
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon6b.png)
    
16. Apprearing párbeszédpanel olvasási **a rendszer kihagyja a teszt azt jelenti, hogy...** , kattintson a **OK**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon6c.png)

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SAML SSO a Jira felbontása GmbH Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **SAML SSO a Jira felbontása GmbH**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **SAML SSO a Jira felbontása GmbH**.

    ![Az SAML egyszeri bejelentkezés az alkalmazásainak listájában feloldási GmbH csatlakozásonkénti Jira](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>SAML SSO a Jira feloldási GmbH teszt felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a Jira SAML SSO felbontása GmbH, akkor ki kell építeni az SAML SSO a Jira felbontása GmbH.  
SAML egyszeri bejelentkezés az Jira felbontása GmbH a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Jira feloldási GmbH vállalati hely által a SAML SSO.

2. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **felhasználókezelés**.

    ![Alkalmazott hozzáadása](./media/samlssojira-tutorial/user1.png) 

3. Rendszergazdai hozzáférés lapon adja meg a rendszer átirányítja **jelszó** kattintson **megerősítése** gombra.

    ![Alkalmazott hozzáadása](./media/samlssojira-tutorial/user2.png) 

4. A **felhasználókezelés** szakasz lapra, majd **felhasználó létrehozása**.

    ![Alkalmazott hozzáadása](./media/samlssojira-tutorial/user3.png) 

5. Az a **"Új felhasználó létrehozása"** párbeszédpanel lapon, a következő lépésekkel:

    ![Alkalmazott hozzáadása](./media/samlssojira-tutorial/user4.png) 

    a. Az a **E-mail-cím** szövegmezőbe írja be az e-mail-cím, felhasználó, például Brittasimon@contoso.com.

    b. Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be a felhasználó például Britta Simon teljes neve.

    c. Az a **felhasználónév** szövegmezőbe írja be az e-mailt, felhasználó, például Brittasimon@contoso.com.

    d. Az a **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    e. Kattintson a **felhasználó létrehozása**.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Az SAML SSO a Jira kattintva által feloldási GmbH csempe a hozzáférési panelen, meg kell automatikusan megtörténik a az SAML SSO-nak a Jira feloldási GmbH, amelynek beállítása egyszeri bejelentkezés által. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

