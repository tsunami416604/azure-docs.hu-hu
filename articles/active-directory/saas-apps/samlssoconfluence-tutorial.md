---
title: 'Oktatóanyag: Feloldási GmbH által az Azure Active Directory-integráció való összefolyás felett az SAML SSO-val |} A Microsoft Docs'
description: Ismerje meg, hogy egyszeri bejelentkezéssel való összefolyás felett az SAML SSO és az Azure Active Directory közötti felbontása GmbH konfigurálása.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 6b47d483-d3a3-442d-b123-171e3f0f7486
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6e98327fbb0f2d10774b6ccb757a4d7c655031ff
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57433195"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Oktatóanyag: Feloldási GmbH által az Azure Active Directory-integráció való összefolyás felett az SAML SSO-val

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja való összefolyás felett az SAML SSO GmbH az Azure Active Directoryval (Azure AD-) megoldás.
Való összefolyás felett az SAML SSO integrálása az Azure ad-vel GmbH felbontása nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá az SAML SSO való összefolyás felett a felbontása GmbH Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve való összefolyás felett az SAML SSO felbontása GmbH (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs konfigurálni való összefolyás felett az SAML SSO-feloldási GmbH, a következő elemek szükségesek:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* SAML SSO felbontása GmbH egyszeri bejelentkezéssel való összefolyás felett az előfizetés engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Való összefolyás felett felbontása GmbH támogatja az SAML SSO **SP** és **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Feloldási GmbH való összefolyás felett az SAML SSO hozzáadását a katalógusból

Integráció konfigurálásához való összefolyás felett az SAML SSO-feloldási GmbH által az Azure AD-be, hozzá kell való összefolyás felett az SAML SSO GmbH felbontása a galériából a felügyelt SaaS-alkalmazások listájára.

**Feloldási GmbH való összefolyás felett az SAML SSO hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **felbontása GmbH való összefolyás felett az SAML SSO**, jelölje be **felbontása GmbH való összefolyás felett az SAML SSO** eredmény panelen kattintson a **Hozzáadás** gombra kattintva adhat hozzá az alkalmazás.

     ![SAML SSO a felbontása GmbH, a találatok listájában való összefolyás felett](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés SAML SSO-val való összefolyás felett felbontása GmbH alapján nevű tesztfelhasználó **Britta Simon**.
Az egyszeri bejelentkezés működéséhez egy Azure AD-felhasználót és a kapcsolódó felhasználó a SAML SSO való összefolyás felett a megoldás által hivatkozás kapcsolatának GmbH kell létrehozni.

Az Azure AD egyszeri bejelentkezés való összefolyás felett az SAML SSO-val felbontása GmbH tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Feloldási GmbH egyszeri bejelentkezéssel való összefolyás felett az SAML SSO konfigurálása](#configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre való összefolyás felett feloldási GmbH teszt felhasználó által a SAML SSO](#create-saml-sso-for-confluence-by-resolution-gmbh-test-user)**  – való összefolyás felett az SAML SSO-megfelelője a Britta Simon van megoldás, amely kapcsolódik az Azure AD felhasználói ábrázolása GmbH szerint.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezéssel való összefolyás felett az SAML SSO-val feloldási GmbH, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **felbontása GmbH való összefolyás felett az SAML SSO** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban a következő lépésekkel, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezve mód:

    ![A felbontása GmbH tartományhoz való összefolyás felett SAML SSO és URL-címek egyszeri bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Kattintson a **további URL-címet beállítani** , és végezze el a következő lépés, ha az alkalmazás konfigurálása a SP kezdeményezett módban:

    ![A felbontása GmbH tartományhoz való összefolyás felett SAML SSO és URL-címek egyszeri bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-címet. Kapcsolattartó [való összefolyás felett feloldási GmbH ügyfél által a SAML SSO támogatási csoportjának](https://www.resolution.de/go/support) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

### <a name="configure-saml-sso-for-confluence-by-resolution-gmbh-single-sign-on"></a>Feloldási GmbH egyszeri bejelentkezéssel való összefolyás felett az SAML SSO konfigurálása

1. Egy másik böngészőablakban, jelentkezzen be a **való összefolyás felett feloldási GmbH felügyeleti portál által a SAML SSO** rendszergazdaként.

2. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **bővítmények**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon1.png)

3. Ekkor megnyílik a rendszergazdai hozzáférés lap. Adja meg a jelszót, és kattintson a **megerősítése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon2.png)

4. A **ATLASSIAN MARKETPLACE** lapra, majd **új bővítmények keresése**. 

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon.png)

5. Keresés **SAML egyszeri bejelentkezés (SSO) az való összefolyás felett** kattintson **telepítése** gombra az új SAML beépülő modul telepítéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon7.png)

6. A beépülő modul telepítése elindul. Kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon8.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon9.png)

7.  Kattintson a **Kezelés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon10.png)
    
8. Kattintson a **konfigurálása** a új beépülő modul konfigurálásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon11.png)

9. Az új beépülő modult is területen található **felhasználók és biztonsági** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon3.png)
    
10. A **SAML egyszeri bejelentkezés beépülő modul konfigurációs** kattintson **adja hozzá az új identitásszolgáltató** gombra kattintva adja meg a beállításokat az identitásszolgáltató.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon4.png)

11. A **válassza ki a SAML-identitásszolgáltató** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon5a.png)
 
    a. Állítsa be **Azure ad-ben** az identitásszolgáltató típusa.
    
    b. Adjon hozzá **neve** az identitásszolgáltató (például: Azure ad-ben).
    
    c. Adjon hozzá **leírás** az identitásszolgáltató (például: Azure ad-ben).
    
    d. Kattintson a **tovább**.
    
12. A **identitás-szolgáltató konfigurációjának** kattintson **tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon5b.png)

13. A **SAML-identitásszolgáltató metaadatok importálása** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon5c.png)

    a. Kattintson a **fájl betöltése** gombra, és 5. lépésében letöltött metaadatainak XML-fájlt válasszon.

    b. Kattintson a **importálás** gombra.
    
    c. Várjon rövid ideig, amíg importálása sikeres.
    
    d. Kattintson a **tovább** gombra.
    
14. A **felhasználói azonosító attribútum és -átalakítási** kattintson **tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon5d.png)
    
15. A **felhasználói létrehozáskor és frissítéskor** kattintson **Mentés & következő** beállításainak mentése.   
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon6a.png)
    
16. A **-beállítások tesztelése** kattintson **ugorja & manuális konfigurálása** ugorja most a felhasználó. Ez a következő szakaszban történik, és bizonyos beállítások az Azure Portalon igényel. 
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon6b.png)
    
17. Amely párbeszédpanel olvasási **a rendszer kihagyja a teszt azt jelenti, hogy...** , kattintson a **OK**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon6c.png)

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés való összefolyás felett az SAML SSO felbontása GmbH Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **felbontása GmbH való összefolyás felett az SAML SSO**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **felbontása GmbH való összefolyás felett az SAML SSO**.

    ![Az SAML egyszeri bejelentkezés az alkalmazásainak listájában feloldási GmbH csatlakozásonkénti való összefolyás felett](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>A feloldási GmbH teszt felhasználó való összefolyás felett SAML SSO létrehozása

Ahhoz, hogy az Azure AD-felhasználók által feloldási GmbH való összefolyás felett az SAML SSO jelentkezzen be, azok ki kell építeni az SAML SSO való összefolyás felett az GmbH felbontása.  
SAML egyszeri bejelentkezés az való összefolyás felett felbontása GmbH a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a feloldási GmbH vállalati hely való összefolyás felett az SAML SSO.

2. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **felhasználókezelés**.

    ![Alkalmazott hozzáadása](./media/samlssoconfluence-tutorial/user1.png) 

3. A felhasználók szakaszban kattintson a **felhasználók hozzáadása** fülre. Az a **"Egy felhasználó hozzáadása"** párbeszédpanel lapon, a következő lépésekkel:

    ![Alkalmazott hozzáadása](./media/samlssoconfluence-tutorial/user2.png) 

    a. Az a **felhasználónév** szövegmezőbe írja be a felhasználó Britta Simon például az e-mailt.

    b. Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be például a Britta Simon felhasználó teljes nevét.

    c. Az a **E-mail** szövegmezőbe írja be az e-mail-cím, felhasználó, például Brittasimon@contoso.com.

    d. Az a **jelszó** szövegmezőbe írja be a jelszót a Britta Simon.

    e. Kattintson a **jelszó megerősítése** írja be újból a jelszót.
    
    f. Kattintson a **Hozzáadás** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

A SAML egyszeri bejelentkezés az való összefolyás felett kattintva által feloldási GmbH csempe a hozzáférési panelen, meg kell automatikusan megtörténik a az SAML SSO-nak való összefolyás felett az feloldási GmbH, amelynek beállítása egyszeri bejelentkezés által. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

