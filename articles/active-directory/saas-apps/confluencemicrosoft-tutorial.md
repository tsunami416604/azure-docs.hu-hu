---
title: 'Oktatóanyag: Azure Active Directory-integráció a Microsoft által való összefolyás felett SAML SSO-val |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés és való összefolyás felett SAML egyszeri bejelentkezés a Microsoft Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/16/2018
ms.author: jeedes
ms.openlocfilehash: 97b6507cb8c5a66b8b6ccbfccd2c838b4b3fb53a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39053301"
---
# <a name="tutorial-azure-active-directory-integration-with-confluence-saml-sso-by-microsoft"></a>Oktatóanyag: Azure Active Directory-integráció a Microsoft által való összefolyás felett SAML SSO-val

Ebben az oktatóanyagban elsajátíthatja a Microsoft által való összefolyás felett SAML SSO integrálása az Azure Active Directory (Azure AD).

Microsoft való összefolyás felett SAML SSO integrálása az Azure AD nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá való összefolyás felett SAML egyszeri bejelentkezés a Microsoft Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett való összefolyás felett SAML SSO, a Microsoft (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="description"></a>Leírás:

A Microsoft Azure Active Directory-fiók használata Atlassian való összefolyás felett kiszolgáló egyszeri bejelentkezés engedélyezéséhez. Ezzel a módszerrel a munkahely összes felhasználója számára a bejelentkezés alkalmazásba való összefolyás felett használhatja az Azure AD hitelesítő adatait. Ez a beépülő modul SAML 2.0 összevonási használ.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Microsoft által való összefolyás felett SAML SSO-val, a következőkre van szükség:

- Az Azure AD-előfizetéshez
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
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség: [próba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="supported-versions-of-confluence"></a>Támogatott verzióiról való összefolyás felett 

Jelen pillanatban való összefolyás felett következő verziói támogatottak:

- Való összefolyás felett: 5.0-s 5.10

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A Microsoft által való összefolyás felett SAML SSO hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>A Microsoft által való összefolyás felett SAML SSO hozzáadása a katalógusból
Az Azure AD-be való összefolyás felett SAML SSO a Microsoft-integráció konfigurálásához, szeretne hozzáadni a katalógusból való összefolyás felett SAML egyszeri bejelentkezés a Microsoft által a felügyelt SaaS-alkalmazások listájában.

**A Microsoft által való összefolyás felett SAML SSO hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **való összefolyás felett SAML SSO Microsoft**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_search.png)

5. Az eredmények panelen válassza ki a **való összefolyás felett SAML SSO Microsoft**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Microsoft a teszt "Britta Simon" nevű felhasználó való összefolyás felett SAML SSO-val.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó való összefolyás felett SAML egyszeri bejelentkezés a Microsoft által a mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó való összefolyás felett SAML egyszeri bejelentkezés a Microsoft által a hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés a Microsoft által való összefolyás felett SAML SSO-val tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Egy való összefolyás felett SAML egyszeri bejelentkezés a Microsoft a teszt felhasználó létrehozása](#creating-a-confluence-saml-sso-by-microsoft-test-user)**  – egy megfelelője a Britta Simon való összefolyás felett SAML SSO, amely kapcsolódik az Azure AD felhasználói ábrázolása a Microsoft által van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és egyszeri bejelentkezés Microsoft-alkalmazáshoz az való összefolyás felett SAML SSO beállításához.

**Az Azure AD egyszeri bejelentkezés konfigurálása a Microsoft által való összefolyás felett SAML SSO-val, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **való összefolyás felett SAML egyszeri bejelentkezés a Microsoft** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_samlbase.png)

3. Az a **való összefolyás felett SAML egyszeri bejelentkezés Microsoft-Domain és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<domain:port>/plugins/servlet/saml/auth`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<domain:port>/`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Abban az esetben azt egy elnevezett URL-cím, port nem kötelező. Ezek az értékek fogadásának való összefolyás felett beépülő modul, az oktatóanyag későbbi részében ismertetett konfigurálása során.

4. Az a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** , és illessze be a Jegyzettömbbe.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/tutorial_metadataurl.png)
     
5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/tutorial_general_400.png)

6. Egy másik böngészőablakban jelentkezzen be a való összefolyás felett példány rendszergazdaként.

7. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **bővítmények**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/addon1.png)

8. Töltse le a beépülő modult a [Microsoft Download Center](https://www.microsoft.com/en-us/download/details.aspx?id=56503). A beépülő modul használatával a Microsoft által biztosított feltöltött **bővítmény feltöltése** menü. A beépülő modul letöltése hatálya alá tartozó [Microsoft szolgáltatási szerződése](https://www.microsoft.com/en-us/servicesagreement/). 
    
    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/addon12.png)

9. A beépülő modul telepítése után megjelenik a **felhasználó telepített** bővítmények szakaszában **kiegészítő szolgáltatás kezelése** szakaszban. Kattintson a **konfigurálása** a új beépülő modul konfigurálásához.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/addon13.png)

10. Hajtsa végre a következő lépéseket a konfigurációs lapon:

    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/addon52.png)

    > [!TIP]
    > Győződjön meg arról, hogy nincs-e rendelve az alkalmazást, hogy nincs hiba feloldása a metaadatokat a csak egy tanúsítványt. Ha több tanúsítványt, a rendszergazdai hibát feloldása a metaadatok beolvasása.

    a. Az a **metaadatok URL-címe** szövegmezőjébe illessze be **alkalmazás összevonási metaadatainak URL-címe** érték, amely az Azure Portalon, majd kattintson a másolta a **megoldásához** gombra. Ez olvassa be az identitásszolgáltató metaadatok URL-címet, és tölti fel a mezők adatai.

    b. Másolás a **azonosítóját, a válasz URL-cím és a bejelentkezési URL** értéket, majd illessze be őket a **azonosítóját, a válasz URL-cím és a bejelentkezési URL** rendre a szövegmezőből **való összefolyás felett SAML egyszeri bejelentkezés Microsoft-Domain és URL-címek**  szakaszban az Azure Portalon.

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

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/confluencemicrosoft-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/confluencemicrosoft-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/confluencemicrosoft-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/confluencemicrosoft-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-confluence-saml-sso-by-microsoft-test-user"></a>Egy való összefolyás felett SAML egyszeri bejelentkezés a Microsoft a teszt felhasználó létrehozása

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

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés való összefolyás felett SAML egyszeri bejelentkezés a Microsoft Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése a Microsoft által való összefolyás felett SAML SSO, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **való összefolyás felett SAML SSO Microsoft**.

    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/tutorial_confluencemicrosoft_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

A való összefolyás felett SAML SSO által a hozzáférési panelen Microsoft csempére kattint, amikor, kell lekérése automatikusan bejelentkezett a való összefolyás felett SAML SSO, Microsoft-alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/confluencemicrosoft-tutorial/tutorial_general_01.png
[2]: ./media/confluencemicrosoft-tutorial/tutorial_general_02.png
[3]: ./media/confluencemicrosoft-tutorial/tutorial_general_03.png
[4]: ./media/confluencemicrosoft-tutorial/tutorial_general_04.png

[100]: ./media/confluencemicrosoft-tutorial/tutorial_general_100.png

[200]: ./media/confluencemicrosoft-tutorial/tutorial_general_200.png
[201]: ./media/confluencemicrosoft-tutorial/tutorial_general_201.png
[202]: ./media/confluencemicrosoft-tutorial/tutorial_general_202.png
[203]: ./media/confluencemicrosoft-tutorial/tutorial_general_203.png
