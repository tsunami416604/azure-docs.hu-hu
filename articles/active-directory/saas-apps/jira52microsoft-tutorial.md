---
title: 'Oktatóanyag: JIRA SAML SSO által a Microsoft (V5.2) az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a JIRA SAML SSO által a Microsoft (V5.2) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.openlocfilehash: 2ab9cf47d0ce2b0b6df263bbb9143702d4135a7a
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/24/2019
ms.locfileid: "54883415"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Oktatóanyag: JIRA SAML SSO által a Microsoft (V5.2) az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan JIRA SAML SSO által a Microsoft (V5.2) integrálása az Azure Active Directory (Azure AD).
JIRA SAML SSO által a Microsoft (V5.2) integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a JIRA SAML SSO által a Microsoft (V5.2) Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezve JIRA SAML SSO által a Microsoft (V5.2) (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="description"></a>Leírás

Egyszeri bejelentkezés engedélyezéséhez használja a Microsoft Azure Active Directory-fiók Atlassian JIRA-kiszolgálóval. Ezzel a módszerrel a munkahely összes felhasználója számára a bejelentkezés a JIRA alkalmazásba használhatja az Azure AD hitelesítő adatait. Ez a beépülő modul SAML 2.0 összevonási használ.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a JIRA SAML SSO által a Microsoft (V5.2), a következőkre van szükség:

- Azure AD-előfizetés
- JIRA Core és a szoftver 5.2 telepítenie kell, és a Windows 64 bites verzióját a következőn:
- JIRA-kiszolgáló a HTTPS-kompatibilis
- Vegye figyelembe, hogy a támogatott verziók JIRA beépülő modul szakasz alatt található szerepelnek.
- JIRA-kiszolgáló elérhető-e internetes különösen az Azure AD bejelentkezési oldal a hitelesítéshez, és tudja fogadni a jogkivonatot az Azure ad-ből kell
- JIRA-hibajegy rendszergazdai hitelesítő adatok beállítása
- WebSudo JIRA-hibajegy le van tiltva
- A JIRA kiszolgálói alkalmazásban létrehozott tesztfelhasználó számára

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem javasoljuk a JIRA éles környezetben. Az integráció először tesztelje fejlesztési vagy az alkalmazás átmeneti környezetben, majd az éles környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség: [Próbaverziós ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-jira"></a>JIRA támogatott verziói

* JIRA Core és a szoftvereket: 5.2
* JIRA 6.0 való 7.12 is támogatja. További részletekért kattintson [JIRA SAML egyszeri bejelentkezés a Microsoft által](jiramicrosoft-tutorial.md)

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a JIRA SAML SSO által a Microsoft (V5.2) **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>JIRA SAML SSO által a Microsoft (V5.2) hozzáadása a katalógusból

JIRA SAML SSO által a Microsoft (V5.2) integrálása az Azure AD beállításához szüksége JIRA SAML SSO által a Microsoft (V5.2) hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**JIRA SAML SSO által a Microsoft (V5.2) hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **JIRA SAML SSO által a Microsoft (V5.2)** válassza **JIRA SAML SSO által a Microsoft (V5.2)** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![JIRA SAML SSO által a Microsoft (V5.2) a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a JIRA SAML SSO által a Microsoft (V5.2) nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó a JIRA SAML SSO által a Microsoft (V5.2) közötti kapcsolat kapcsolatot kell hozható létre.

Az Azure AD egyszeri bejelentkezés a JIRA SAML SSO által a Microsoft (V5.2) tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[JIRA SAML SSO által a Microsoft (V5.2) egyszeri bejelentkezés konfigurálása](#configure-jira-saml-sso-by-microsoft-v52-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[JIRA SAML SSO által a Microsoft (V5.2) tesztfelhasználó létrehozása](#create-jira-saml-sso-by-microsoft-v52-test-user)**  – egy megfelelője a Britta Simon JIRA SAML SSO által a Microsoft (V5.2), amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés JIRA SAML SSO által a Microsoft (V5.2), hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **JIRA SAML SSO által a Microsoft (V5.2)** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![JIRA SAML SSO által a Microsoft (V5.2) tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier-reply.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Az a **azonosító** mezőbe írja be a következő minta használatával URL-cím: `https://<domain:port>/`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Abban az esetben azt egy elnevezett URL-cím, port nem kötelező. Ezek az értékek fogadásának Jira beépülő modul, az oktatóanyag későbbi részében ismertetett konfigurálása során.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** és mentse a számítógép.

    ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

### <a name="configure-jira-saml-sso-by-microsoft-v52-single-sign-on"></a>JIRA SAML SSO által a Microsoft (V5.2) egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a JIRA-példány rendszergazdaként.

2. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **bővítmények**.

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon1.png)

3. A bővítmények lapon szakasz alatt kattintson **bővítmények kezelése**.

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon7.png)

4. Töltse le a beépülő modult a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56521). A beépülő modul használatával a Microsoft által biztosított feltöltött **bővítmény feltöltése** menü. A beépülő modul letöltése hatálya alá tartozó [Microsoft szolgáltatási szerződése](https://www.microsoft.com/servicesagreement/).

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon12.png)

5. A beépülő modul telepítése után megjelenik a **felhasználó telepített** bővítmények szakaszban. Kattintson a **konfigurálása** a új beépülő modul konfigurálásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon13.png)

6. Hajtsa végre a következő lépéseket a konfigurációs lapon:

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Győződjön meg arról, hogy nincs-e rendelve az alkalmazást, hogy nincs hiba feloldása a metaadatokat a csak egy tanúsítványt. Ha több tanúsítványnak, a metaadatok feloldása után a rendszergazda lekérdezi hiba.

    a. A **metaadatok URL-címe** szövegmezőjébe illessze be **alkalmazás összevonási metaadatainak URL-címe** érték, amely az Azure Portalon, majd kattintson a másolta a **megoldásához** gombra. Ez olvassa be az identitásszolgáltató metaadatok URL-címet, és tölti fel a mezők adatai.

    b. Másolás a **azonosítóját, a válasz URL-cím és a bejelentkezési URL** értéket, majd illessze be őket a **azonosítóját, a válasz URL-cím és a bejelentkezési URL** rendre a szövegmezőből **alapszintű SAML-konfigurációja** a szakasz az Azure Portalon.

    c. A **bejelentkezési gomb neve** írja be a szervezet azt szeretné, tekintse meg a bejelentkezési képernyőn a felhasználók gomb felirata.

    d. A **SAML felhasználói azonosító helyek** válassza **felhasználói Azonosítóját a tulajdonos utasítás NameIdentifier elemében van** vagy **felhasználói azonosító szerepel egy attribútumelem**.  Ez az azonosító nem lehet a JIRA felhasználói azonosító. Ha a felhasználói azonosító nem egyezik, majd rendszer nem engedélyezi felhasználók bejelentkezésének.

    > [!Note]
    > Alapértelmezett SAML Felhasználóazonosító helye alkalmazásnév-azonosító. Egy attribútum beállítást módosíthatja, és adja meg a megfelelő attribútum nevét.

    e. Ha **felhasználói azonosító szerepel egy attribútumelem** lehetőség, ezt a **attribútum neve** szövegmezőbe írja be a nevét, ahol a felhasználói azonosító várható. 

    f. Ha az összevont tartományt (például AD FS stb.) az Azure ad-vel használ, majd kattintson a a **engedélyezése a Kezdőtartomány felderítésének** lehetőséget, majd konfigurálja a **tartománynév**.

    g. A **tartománynév** írja be a tartomány nevét itt az AD FS-alapú bejelentkezés esetén.

    h. Ellenőrizze **meg az egyszeri bejelentkezés engedélyezése** pedig jelentkezzen ki az Azure ad-ben, amikor egy felhasználó bejelentkezik a JIRA kíván. 

    i. Kattintson a **mentése** gombra kattintva mentse a beállításokat.

    > [!NOTE]
    > Telepítési és hibaelhárítási kapcsolatos további információkért látogasson el [MS JIRA SSO összekötő rendszergazdai útmutató](../ms-confluence-jira-plugin-adminguide.md) , és nincs is [– gyakori kérdések](../ms-confluence-jira-plugin-faq.md) a segítségért

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés JIRA SAML SSO által a Microsoft (V5.2) Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **JIRA SAML SSO által a Microsoft (V5.2)**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **JIRA SAML SSO által a Microsoft (V5.2)**.

    ![A JIRA SAML SSO által a Microsoft (V5.2) hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>JIRA SAML SSO által a Microsoft (V5.2) tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók JIRA helyszíni kiszolgálón jelentkezzen be, akkor ki kell építeni JIRA a helyi kiszolgálóra.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a JIRA helyileg működtetett kiszolgálón.

2. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **felhasználókezelés**.

    ![Alkalmazott hozzáadása](./media/jira52microsoft-tutorial/user1.png)

3. Rendszergazdai hozzáférés lapon adja meg a rendszer átirányítja **jelszó** kattintson **megerősítése** gombra.

    ![Alkalmazott hozzáadása](./media/jira52microsoft-tutorial/user2.png)

4. A **felhasználókezelés** szakasz lapra, majd **felhasználó létrehozása**.

    ![Alkalmazott hozzáadása](./media/jira52microsoft-tutorial/user3.png) 

5. Az a **"Új felhasználó létrehozása"** párbeszédpanel lapon, a következő lépésekkel:

    ![Alkalmazott hozzáadása](./media/jira52microsoft-tutorial/user4.png)

    a. Az a **E-mail-cím** szövegmezőbe írja be az e-mail-cím, felhasználó, például Brittasimon@contoso.com.

    b. Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be a felhasználó például Britta Simon teljes neve.

    c. Az a **felhasználónév** szövegmezőbe írja be az e-mailt, felhasználó, például Brittasimon@contoso.com.

    d. Az a **jelszó** szövegmezőbe írja be a felhasználó jelszavát.

    e. Kattintson a **felhasználó létrehozása**.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Által a hozzáférési panelen Microsoft (V5.2) csempére kattintva a JIRA SAML SSO, meg kell lehet automatikusan bejelentkezett a JIRA SAML SSO által a Microsoft (V5.2), amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)