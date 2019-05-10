---
title: 'Oktatóanyag: A Jira feloldási GmbH által a SAML SSO-val az Azure Active Directory-integráció |} A Microsoft Docs'
description: Ismerje meg, hogyan konfigurálása egyszeri bejelentkezéshez tartozó Jira SAML SSO és az Azure Active Directory közötti GmbH felbontása.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 943131bc746b5d2a1fd95a26a6a6c9f3bb6b9e57
ms.sourcegitcommit: 8fc5f676285020379304e3869f01de0653e39466
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/09/2019
ms.locfileid: "65509959"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Oktatóanyag: Feloldási GmbH által az Azure Active Directory-integráció a Jira SAML SSO-val

Ebben az oktatóanyagban elsajátíthatja, hogyan állítható be a Jira SAML SSO feloldási GmbH az Azure Active Directoryval (Azure AD) által.
A Jira SAML SSO integrálása feloldási GmbH az Azure AD által nyújt a következő előnyökkel jár:

* Szabályozhatja, aki bejelentkezhet Jira az SAML SSO beépülő modullal felbontása GmbH Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett jira szolgáltatásba való Azure AD-fiókjukat az SAML SSO használatával Jira felbontása GmbH (egyszeri bejelentkezés).
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Konfigurálja az Azure AD-integrációs és SAML SSO a Jira feloldási GmbH, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Jira felbontása GmbH egyszeri bejelentkezéshez a SAML SSO előfizetés engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Jira felbontása GmbH támogatja az SAML SSO **SP** és **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Az egyszeri bejelentkezés egy vállalati alkalmazás hozzáadása

Állítsa be egyszeri bejelentkezést az Azure ad-ben, adjon hozzá egy új vállalati alkalmazást kell. A katalógusban, van egy előre beállított, előre konfigurált alkalmazás **SAML SSO a Jira felbontása GmbH**.

**Adja hozzá a Jira SAML SSO GmbH felbontása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások**, és kattintson a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **SAML SSO a Jira felbontása GmbH**, jelölje be **SAML SSO a Jira felbontása GmbH** az eredmény panelen, majd kattintson a a **hozzáadása** gombra kattintva adhat hozzá a az alkalmazás. A vállalati alkalmazás nevét is módosíthatja.

     ![SAML SSO a Jira felbontása GmbH, a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Egyszeri bejelentkezés a SAML SSO beépülő modulja, és az Azure ad-ben tesztelése és konfigurálása

Ebben a szakaszban fog tesztelése és konfigurálása egyszeri bejelentkezéshez jira szolgáltatásba való az Azure AD-felhasználót. Erre azért van szükség a tesztfelhasználó számára nevű **Britta Simon**.
Az egyszeri bejelentkezés működéséhez egy Azure AD-felhasználót és a kapcsolódó felhasználó a SAML SSO a Jira felbontása hivatkozás kapcsolata GmbH kell létrehozni.

Egyszeri bejelentkezés tesztelése és konfigurálása, meg kell a következő lépéseket:

1. **[Az Azure AD vállalati alkalmazást, az egyszeri bejelentkezés konfigurálása](#configure-the-azure-ad-enterprise-application-for-single-sign-on)**  – az Azure AD vállalati alkalmazás számára az egyszeri bejelentkezés konfigurálása
2. **[A SAML SSO beépülő modulja a Jira-példány konfigurálása](#configure-the-saml-sso-plugin-of-your-jira-instance)**  – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure ad-ben hozzon létre egy tesztfelhasználót.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  -engedélyezése a tesztfelhasználó használata az egyszeri bejelentkezés az Azure oldaláról.
1. **[A tesztfelhasználó létrehozása Jira-hibajegy](#create-the-test-user-also-in-jira)**  -partner tesztfelhasználó létrehozása Jira-hibajegy az Azure ad-ben tesztfelhasználó számára.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Az Azure AD vállalati alkalmazást, az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban állíthatja be az egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az egyszeri bejelentkezést a Jira SAML SSO-val feloldási GmbH, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az az imént létrehozott **SAML SSO a Jira felbontása GmbH** vállalati alkalmazást, válassza **egyszeri bejelentkezési** a bal oldali panelen.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. A **egyszeri bejelentkezési módszer**, jelölje be a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Ezt követően kattintson a **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszt, ha az alkalmazás konfigurálása a **Identitásszolgáltató** kezdeményezett módot, majd hajtsa végre az alábbi lépéseket:

    ![SAML SSO a Jira felbontása GmbH tartomány és URL-címek egyszeri bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<server-base-url>/plugins/servlet/samlsso`

    c. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt a **SP** kezdeményezett mód:

    ![SAML SSO a Jira felbontása GmbH tartomány és URL-címek egyszeri bejelentkezési adatait](common/metadata-upload-additional-signon.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Az azonosító, a válasz URL-cím és a bejelentkezési URL-, helyettesítse be  **\<– base-URL-címe >** az alap URL-címmel a Jira-példány. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon. Ha probléma van, írjon nekünk az [Jira feloldási GmbH ügyfél által a SAML SSO támogatási csoportjának](https://www.resolution.de/go/support).

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** szakaszban, töltse le a **összevonási metaadatainak XML** , és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>A SAML SSO beépülő modulja a Jira-példány konfigurálása 

1. Egy másik böngészőablakban jelentkezzen be a Jira-példány rendszergazdaként.

2. A fogaskerék alakú ikonjára a jobb oldali mutasson, és kattintson a **Alkalmazásfelügyelet**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon1.png)

3. Ha a rendszer átirányítja a rendszergazdai hozzáférés lapra, adja meg a **jelszó** , és kattintson a **megerősítése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon2.png)

4. Jira általában a rendszer átirányítja Önt a Atlassian Marketplace-en. Ha nem, kattintson a **új alkalmazásokat kereshet** a bal oldali panelen. Keresse meg **SAML egyszeri bejelentkezés (SSO) a JIRA** , és kattintson a **telepítése** gomb a SAML beépülő modul telepítéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/store.png)

5. A beépülő modul telepítése elindul. Amikor kész van, kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/store-2.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/store-3.png)

6. Kattintson a **kezelés**.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/store-4.png)
    
8. Ezt követően kattintson a **konfigurálása** az imént telepített beépülő modul konfigurálásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/store-5.png)

9. Az a **SAML egyszeri bejelentkezés beépülő modul konfigurációs** varázslóban kattintson a **adja hozzá az új identitásszolgáltató** Azure ad-ben egy új identitásszolgáltató konfigurálása.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon4.png) 

10. Az a **válassza ki a SAML-identitásszolgáltató** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon5a.png)
 
    a. Állítsa be **Azure ad-ben** az identitásszolgáltató típusa.
    
    b. Adja hozzá a **neve** az identitásszolgáltató (például: Azure ad-ben).
    
    c. Adjon hozzá egy (nem kötelező) **leírás** az identitásszolgáltató (például: Azure ad-ben).
    
    d. Kattintson a **tovább**.
    
11. Az a a **identitás-szolgáltató konfigurációjának** kattintson **tovább**.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon5b.png)

12. A **SAML-identitásszolgáltató metaadatok importálása** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon5c.png)

    a. Kattintson a **metaadatainak XML-fájl kiválasztása** gombra, és válassza ki a **összevonási metaadatainak XML** előzőleg letöltött fájl.

    b. Kattintson a **importálás** gombra.
     
    c. Várjon rövid ideig, amíg az importálás sikeres volt.  
     
    d. Kattintson a **tovább** gombra.
    
13. A **felhasználói azonosító attribútum és -átalakítási** lap, kattintson a **tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon5d.png)
    
14. Az a **felhasználó létrehozása és frissítése** kattintson **Mentés & következő** a beállítások mentéséhez.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon6a.png)
    
15. Az a **-beállítások tesztelése** kattintson **ugorja & manuális konfigurálása** ugorja most a felhasználó. Ez a következő szakaszban történik, és van szükség az egyes beállítások az Azure Portalon.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon6b.png)
    
16. Kattintson a **OK** kihagyja a figyelmeztetést.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót. A felhasználóval teszteli egyszeri bejelentkezést.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válasszon **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. Az a **felhasználói tulajdonságok**, hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** írja be a következőt **Britta Simon**.
  
    b. Az a **felhasználónév** írja be a következőt <b> BrittaSimon@contoso.com </b>.

    c. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban hozzá Britta Simon a vállalati alkalmazás, amely lehetővé teszi, hogy egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**. 

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában keressen rá a létrehozott Ez az oktatóanyag elején a vállalati alkalmazás. Ha az oktatóanyag lépéseit követi, nevezzük **SAML SSO a Jira felbontása GmbH**. Ha adott azt egy másik nevet, keresse meg ezt a nevet.

    ![Az SAML egyszeri bejelentkezés az alkalmazásainak listájában feloldási GmbH csatlakozásonkénti Jira](common/all-applications.png)

3. Kattintson a bal oldali panel **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Válassza ki **felhasználó hozzáadása**, majd válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában, és kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha vár semmilyen szerepkör értéke a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján .

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-the-test-user-also-in-jira"></a>Jira-hibajegy is hozzon létre a tesztfelhasználó számára

Ahhoz, hogy a SAML SSO a Jira feloldási GmbH jelentkezzen be az Azure AD-felhasználók, akkor ki kell építeni az SAML SSO a Jira felbontása GmbH. Ebben az oktatóanyagban az esetet tegye a kiépítés kézzel kell. Azonban érhetők el is egyéb kiépítési minták a SAML SSO beépülő modulja által feloldási, például **igény szerinti** kiépítése. A dokumentációban található [SAML SSO felbontása GmbH](https://wiki.resolution.de/doc/saml-sso/latest/all). Ha olyan kérdése van, forduljon a támogatási szolgálathoz [felbontás támogatása](https://www.resolution.de/go/support).

**Manuálisan üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként egy Jira-példány.

2. Vigye a kurzort a fogaskerék alakú ikonjára, és válasszon **felhasználókezelés**.

   ![Alkalmazott hozzáadása](./media/samlssojira-tutorial/user1.png)

3. Ha a rendszer átirányítja a rendszergazdai hozzáférés lap, adja meg a **jelszó** , és kattintson a **megerősítése** gombra.

    ![Alkalmazott hozzáadása](./media/samlssojira-tutorial/user2.png) 

4. Alatt a **felhasználókezelés** szakasz lapra, majd **felhasználó létrehozása**.

    ![Alkalmazott hozzáadása](./media/samlssojira-tutorial/user3-new.png) 

5. Az a **"Új felhasználó létrehozása"** párbeszédpanel lapon, a következő lépésekkel. A felhasználó pontosan létrehozása az Azure ad-ben, például:

    ![Alkalmazott hozzáadása](./media/samlssojira-tutorial/user4-new.png) 

    a. Az a **E-mail-cím** szövegmezőbe írja be a felhasználó e-mail-címe: <b> BrittaSimon@contoso.com </b>.

    b. Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be a felhasználó teljes neve: **Britta Simon**.

    c. Az a **felhasználónév** szövegmezőbe írja be a felhasználó e-mail-címe: <b> BrittaSimon@contoso.com </b>. 

    d. Az a **jelszó** szövegmezőbe írja be a jelszót a felhasználó.

    e. Kattintson a **felhasználó létrehozása** felhasználó létrehozásának befejezéséhez.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Az SAML SSO a Jira kattintva által feloldási GmbH csempe a hozzáférési panelen, meg kell automatikusan megtörténik a az SAML SSO-nak a Jira feloldási GmbH, amelynek beállítása egyszeri bejelentkezés által. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

Is tesztelheti egyszeri bejelentkezés, ha visszalép [https://\<kiszolgáló – alap-URL-cím >/plugins/servlet/samlsso](https://\<server-base-url>/plugins/servlet/samlsso). A helyettesítő  **\<– base-URL-címe >** az alap URL-címmel a Jira-példány.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Egyszeri bejelentkezés átirányítás Jira engedélyezése

Mielőtt szakaszban feljegyzett jelenleg két módon elindítani az egyszeri bejelentkezést. Használatával a **az Azure portal** vagy **mutató a Jira-példány**. A SAML SSO beépülő modulja által feloldási GmbH is lehetővé egyszerűen aktiválja az egyszeri bejelentkezés által **hozzáférő bármely URL-t, a Jira-példány**.

Lényegében minden felhasználó fér hozzá a Jira átirányítjuk az egyszeri bejelentkezést a beépülő modul beállítás aktiválása után.

Egyszeri bejelentkezés átirányítási aktiválásához hajtsa végre a következőket **a Jira-példány**:

1. A SAML SSO beépülő modulja Jira-hibajegy konfiguráció lapján eléréséhez.
1. Kattintson a **átirányítás** a bal oldali panelen.
![](./media/samlssojira-tutorial/ssore1.png)

1. Osztásjelek **engedélyezése az egyszeri bejelentkezés átirányítási**.
![](./media/samlssojira-tutorial/ssore2.png) 

1. Nyomja le az **beállítások mentése** gombra a jobb felső sarokban.

A beállítás aktiválása, után továbbra is elérheti a felhasználónév/jelszó kérése, ha a **nosso engedélyezése** lehetőség van jelölőnégyzet lépve [https://\<– base-URL-címe > /login.jsp?nosso](https://\<server-base-url>/login.jsp?nosso). Mint mindig, helyettesítse  **\<– base-URL-címe >** az alap URL-címmel.


## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

