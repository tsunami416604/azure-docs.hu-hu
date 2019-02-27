---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a Microsoft által való összefolyás felett SAML SSO-val |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés és való összefolyás felett SAML egyszeri bejelentkezés a Microsoft Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/31/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f04f42d2a17c8694a59e388d090a48ea45840979
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/26/2019
ms.locfileid: "56868170"
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Oktatóanyag: Az Azure Active Directory-integráció a Microsoft által való összefolyás felett SAML SSO-val

Ebben az oktatóanyagban elsajátíthatja a Microsoft által való összefolyás felett SAML SSO integrálása az Azure Active Directory (Azure AD).
Microsoft való összefolyás felett SAML SSO integrálása az Azure AD nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá való összefolyás felett SAML egyszeri bejelentkezés a Microsoft Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve való összefolyás felett SAML SSO (egyszeri bejelentkezés) a Microsoft által az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="description"></a>Leírás:

A Microsoft Azure Active Directory-fiók használata Atlassian való összefolyás felett kiszolgáló egyszeri bejelentkezés engedélyezéséhez. Ezzel a módszerrel a munkahely összes felhasználója számára a bejelentkezés alkalmazásba való összefolyás felett használhatja az Azure AD hitelesítő adatait. Ez a beépülő modul SAML 2.0 összevonási használ.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Microsoft által való összefolyás felett SAML SSO-val, a következőkre van szükség:

- Azure AD-előfizetés
- A Windows 64 bites kiszolgálóra telepített való összefolyás felett kiszolgálói alkalmazás (helyszíni vagy a felhőbeli IaaS-infrastruktúra)
- Kiszolgáló való összefolyás felett HTTPS-kompatibilis
- Vegye figyelembe, hogy a támogatott verziók való összefolyás felett beépülő modul szakasz alatt található szerepelnek.
- Való összefolyás felett kiszolgáló elérhető-e internetes különösen az Azure AD bejelentkezési oldal a hitelesítéshez, és meg tudja fogadni a jogkivonatot az Azure ad-ből
- Rendszergazdai hitelesítő adatok beállítása való összefolyás felett
- WebSudo való összefolyás felett le van tiltva
- A való összefolyás felett kiszolgálói alkalmazásban létrehozott tesztfelhasználó számára

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem javasoljuk egy éles környezetbe való összefolyás felett. Az integráció először tesztelje fejlesztési vagy az alkalmazás átmeneti környezetben, majd az éles környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség: [Próbaverziós ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-confluence"></a>Támogatott verzióiról való összefolyás felett

Jelen pillanatban való összefolyás felett következő verziói támogatottak:

- Való összefolyás felett: 5.0-s az 5.10.
- Való összefolyás felett: 6.0.1
- Való összefolyás felett: 6.1.1
- Való összefolyás felett: 6.2.1
- Való összefolyás felett: 6.3.4
- Való összefolyás felett: 6.4.0
- Való összefolyás felett: 6.5.0
- Való összefolyás felett: 6.6.2
- Való összefolyás felett: 6.7.0
- Való összefolyás felett: 6.8.1
- Való összefolyás felett: 6.9.0
- Való összefolyás felett: 6.10.0
- Való összefolyás felett: 6.11.0
- Való összefolyás felett: 6.12.0

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Microsoft által való összefolyás felett SAML SSO **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>A Microsoft által való összefolyás felett SAML SSO hozzáadása a katalógusból

Az Azure AD-be való összefolyás felett SAML SSO a Microsoft-integráció konfigurálásához, szeretne hozzáadni a katalógusból való összefolyás felett SAML egyszeri bejelentkezés a Microsoft által a felügyelt SaaS-alkalmazások listájában.

**A Microsoft által való összefolyás felett SAML SSO hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **való összefolyás felett SAML egyszeri bejelentkezés a Microsoft**, jelölje be **való összefolyás felett SAML egyszeri bejelentkezés a Microsoft** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![A Microsoft a találatok listájában való összefolyás felett SAML egyszeri bejelentkezés](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Microsoft a teszt nevű felhasználó való összefolyás felett SAML SSO-val **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó való összefolyás felett SAML egyszeri bejelentkezés a Microsoft által a hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a Microsoft által való összefolyás felett SAML SSO-val tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálása által a Microsoft egyszeri bejelentkezéssel való összefolyás felett SAML SSO](#configure-confluence-saml-sso-by-microsoft-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre a Microsoft a teszt felhasználó való összefolyás felett SAML SSO](#create-confluence-saml-sso-by-microsoft-test-user)**  – egy megfelelője a Britta Simon való összefolyás felett SAML SSO, amely kapcsolódik az Azure AD felhasználói ábrázolása a Microsoft által van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása a Microsoft által való összefolyás felett SAML SSO-val, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **való összefolyás felett SAML egyszeri bejelentkezés a Microsoft** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Való összefolyás felett SAML SSO által a Microsoft-Domain és URL-címek egyetlen bejelentkezési adatait](common/sp-identifier-reply.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Az a **azonosító** mezőbe írja be a következő minta használatával URL-cím: `https://<domain:port>/`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Abban az esetben azt egy elnevezett URL-cím, port nem kötelező. Ezek az értékek fogadásának való összefolyás felett beépülő modul, az oktatóanyag későbbi részében ismertetett konfigurálása során.

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** és mentse a számítógép.

    ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

### <a name="configure-confluence-saml-sso-by-microsoft-single-sign-on"></a>A Microsoft Single Sign-On való összefolyás felett SAML egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a való összefolyás felett példány rendszergazdaként.

2. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **bővítmények**.

    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/addon1.png)

3. Töltse le a beépülő modult a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=56503). A beépülő modul használatával a Microsoft által biztosított feltöltött **bővítmény feltöltése** menü. A beépülő modul letöltése hatálya alá tartozó [Microsoft szolgáltatási szerződése](https://www.microsoft.com/servicesagreement/).

    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/addon12.png)

4. A beépülő modul telepítése után megjelenik a **felhasználó telepített** bővítmények szakaszában **kiegészítő szolgáltatás kezelése** szakaszban. Kattintson a **konfigurálása** a új beépülő modul konfigurálásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/addon13.png)

5. Hajtsa végre a következő lépéseket a konfigurációs lapon:

    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Győződjön meg arról, hogy nincs-e rendelve az alkalmazást, hogy nincs hiba feloldása a metaadatokat a csak egy tanúsítványt. Ha több tanúsítványt, a rendszergazdai hibát feloldása a metaadatok beolvasása.

    a. Az a **metaadatok URL-címe** szövegmezőjébe illessze be **alkalmazás összevonási metaadatainak URL-címe** érték, amely az Azure Portalon, majd kattintson a másolta a **megoldásához** gombra. Ez olvassa be az identitásszolgáltató metaadatok URL-címet, és tölti fel a mezők adatai.

    b. Másolás a **azonosítóját, a válasz URL-cím és a bejelentkezési URL** értéket, majd illessze be őket a **azonosítóját, a válasz URL-cím és a bejelentkezési URL** rendre a szövegmezőből **alapszintű SAML-konfigurációja** a szakasz az Azure Portalon.

    c. A **bejelentkezési gomb neve** írja be a szervezet azt szeretné, tekintse meg a bejelentkezési képernyőn a felhasználók gomb felirata.

    d. A **SAML felhasználói azonosító helyek**, válassza **felhasználói Azonosítóját a tulajdonos utasítás NameIdentifier elemében van** vagy **felhasználói azonosító szerepel egy attribútumelem**.  Ez az azonosító nem lehet való összefolyás felett felhasználói azonosító. Ha a felhasználói azonosító nem egyezik, majd rendszer nem engedélyezi felhasználók bejelentkezésének. 

    > [!Note]
    > Alapértelmezett SAML Felhasználóazonosító helye alkalmazásnév-azonosító. Egy attribútum beállítást módosíthatja, és adja meg a megfelelő attribútum nevét.
    
    e. Ha **felhasználói azonosító szerepel egy attribútumelem** lehetőség, ezt a **attribútum neve** szövegmezőbe írja be a nevét, ahol a felhasználói azonosító várható. 

    f. Ha az összevont tartományt (például AD FS stb.) az Azure ad-vel használ, majd kattintson a a **engedélyezése a Kezdőtartomány felderítésének** lehetőséget, majd konfigurálja a **tartománynév**.
    
    g. A **tartománynév** írja be a tartomány nevét itt az AD FS-alapú bejelentkezés esetén.

    h. Ellenőrizze **meg az egyszeri bejelentkezés engedélyezése** való jelentkezzen ki, amikor egy felhasználó bejelentkezik való összefolyás felett az Azure AD-ből szeretné. 

    i. Kattintson a **mentése** gombra kattintva mentse a beállításokat.

    > [!NOTE]
    > Telepítési és hibaelhárítási kapcsolatos további információkért látogasson el [MS való összefolyás felett SSO összekötő rendszergazdai útmutató](../ms-confluence-jira-plugin-adminguide.md) , és nincs is [– gyakori kérdések](../ms-confluence-jira-plugin-faq.md) a segítségért

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

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés való összefolyás felett SAML egyszeri bejelentkezés a Microsoft Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **való összefolyás felett SAML egyszeri bejelentkezés a Microsoft**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **való összefolyás felett SAML SSO Microsoft**.

    ![Az alkalmazások listáját a Microsoft csatlakozásonkénti való összefolyás felett SAML egyszeri bejelentkezés](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>A Microsoft a teszt felhasználó való összefolyás felett SAML SSO létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be a helyi kiszolgálóra való összefolyás felett, akkor ki kell építeni való összefolyás felett SAML SSO, a Microsoft által. Való összefolyás felett SAML egyszeri bejelentkezés a Microsoft a kiépítés a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a való összefolyás felett a helyszíni kiszolgálón rendszergazdaként.

2. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **felhasználókezelés**.

    ![Alkalmazott hozzáadása](./media/confluencemicrosoft-tutorial/user1.png) 

3. A felhasználók szakaszban kattintson a **felhasználók hozzáadása** fülre. Az a **felhasználó hozzáadása** párbeszédpanel lapon, a következő lépésekkel:

    ![Alkalmazott hozzáadása](./media/confluencemicrosoft-tutorial/user2.png) 

    a. Az a **felhasználónév** szövegmezőbe írja be a felhasználó Britta Simon például az e-mailt.

    b. Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be például a Britta Simon felhasználó teljes nevét.

    c. Az a **E-mail** szövegmezőbe írja be az e-mail-cím, felhasználó, például Brittasimon@contoso.com.

    d. Az a **jelszó** szövegmezőbe írja be a jelszót a Britta Simon.

    e. Kattintson a **jelszó megerősítése** írja be újból a jelszót.

    f. Kattintson a **Hozzáadás** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

A való összefolyás felett SAML SSO által a hozzáférési panelen Microsoft csempére kattint, amikor meg kell lehet automatikusan bejelentkezett a való összefolyás felett SAML SSO, amelynek beállítása egyszeri bejelentkezés a Microsoft által. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

