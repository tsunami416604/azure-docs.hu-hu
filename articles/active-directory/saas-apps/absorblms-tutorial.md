---
title: 'Oktatóanyag: Azure Active Directory-integráció az LMS elháríthatók |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az LMS elháríthatók között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: ba9f1b3d-a4a0-4ff7-b0e7-428e0ed92142
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: jeedes
ms.openlocfilehash: 066ae92056e4b80b6627b371d6ebeb3235b2781d
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39043778"
---
# <a name="tutorial-azure-active-directory-integration-with-absorb-lms"></a>Oktatóanyag: Azure Active Directory-integráció az LMS számára

Ebben az oktatóanyagban elsajátíthatja, hogyan elháríthatók LMS integrálása az Azure Active Directory (Azure AD).

Számára a LMS integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá az LMS számára a Azure AD-ben.
- Engedélyezheti a felhasználók automatikusan jelentkezhetnek be számára a LMS (keresztül egyszeri) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen, az Azure Portalon kezelheti.

Ha szeretne többet is megtudni szoftverek az Azure ad-vel szoftverként (saas biztosított) alkalmazás integrációja, [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

LMS elháríthatók az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Az LMS elháríthatók az egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Azt javasoljuk, hogy ne használjon az éles környezetben ehhez az oktatóanyaghoz.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

* LMS számára a Hozzáadás a katalógusból
* Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="add-absorb-lms-from-the-gallery"></a>Számára a LMS hozzáadása a katalógusból
Elháríthatók LMS integrálása az Azure AD beállítása, adja hozzá a katalógus számára a segítségével a felügyelt SaaS-alkalmazások listájában.

A katalógusból elháríthatók LMS hozzáadásához tegye a következőket:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Lépjen a **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Egy alkalmazás hozzáadásához válassza a **új alkalmazás** gombra.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **elháríthatók LMS**, jelölje be **LMS számára a** az eredmény panelen, és válassza ki a **hozzáadása** gombra.

    ![Az eredmények listájában LMS számára](./media/absorblms-tutorial/tutorial_absorblms_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés a Britta Simon nevű teszt felhasználó számára a segítségével.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a számára a LMS tartozó felhasználó Mi az Azure AD-ben. Más szóval LMS számára a megfelelő felhasználói, valamint az Azure ad-ben a felhasználó közötti kapcsolat kapcsolatot kell létesítenie.

A hivatkozás kapcsolatot hozhat létre, rendelhet a *felhasználónév* érték az Azure ad-ben, a *felhasználónév* LMS elháríthatók az értéket.

Az Azure AD egyszeri bejelentkezés az LMS elháríthatók tesztelése és konfigurálása, hajtsa végre a következő öt szakaszokban építőelemeket.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az LMS elháríthatók alkalmazás egyszeri bejelentkezés konfigurálása.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés segítségével elháríthatók, tegye a következőket:

1. Az Azure Portalon a a **elháríthatók LMS** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédpanel a **mód** jelölje ki **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/absorblms-tutorial/tutorial_absorblms_samlbase.png)

3. Az a **elháríthatók LMS-tartomány és URL-címek** területén tegye a következőket:

    ![Elháríthatók az LMS-tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/absorblms-tutorial/tutorial_absorblms_url.png)

    a. Az a **azonosító** mezőbe írja be egy URL-címet, a következő szintaxist: `https://<subdomain>.myabsorb.com/Account/SAML`.

    b. Az a **válasz URL-cím** mezőbe írja be egy URL-címet, a következő szintaxist: `https://<subdomain>.myabsorb.com/Account/SAML`.
     
    > [!NOTE] 
    > Az URL-címek nem a valódi értékek állnak. Frissítse azokat a tényleges azonosítóját és a válasz URL-címek. Ezek az értékek beszerzéséhez forduljon a [elháríthatók LMS ügyfél-támogatási csapatával](https://www.absorblms.com/support). 

4. Az a **SAML-aláíró tanúsítvány** részben, a a **letöltése** oszlopból válassza ki **metaadatainak XML**, majd mentse a metaadat-fájlt a számítógépre.

    ![Az aláíró tanúsítvány letöltési hivatkozás](./media/absorblms-tutorial/tutorial_absorblms_certificate.png) 

5. Kattintson a **Mentés** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/absorblms-tutorial/tutorial_general_400.png)
    
6. Az a **LMS-konfiguráció számára a** szakaszban jelölje be **elháríthatók LMS konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablakban, majd másolja a **kijelentkezéses URL-cím** a a **gyors útmutató szakaszban.**

    ![Az LMS-konfiguráció számára a panelen](./media/absorblms-tutorial/tutorial_absorblms_configure.png) 

7. Egy új böngészőablakban jelentkezzen be az LMS számára a vállalati webhely rendszergazdaként.

8. Válassza ki a **fiók** gomb jobb felső sarokban. 

    ![A fiók gomb](./media/absorblms-tutorial/1.png)

9. A fiók panelen válassza ki **portál beállításait**.

    ![A portál beállításait hivatkozás](./media/absorblms-tutorial/2.png)
    
10. Válassza a **Felhasználók** lapot.

    ![A felhasználók lap](./media/absorblms-tutorial/3.png)

11. Az egyszeri bejelentkezés beállításainak oldalon tegye a következőket:

    ![Az egyszeri bejelentkezés konfigurálása lap](./media/absorblms-tutorial/4.png)

    a. Az a **mód** jelölje ki **identitás szolgáltató által kezdeményezett**.

    b. A Jegyzettömbben nyissa meg a tanúsítványt, amely az Azure Portalról letöltött. Távolítsa el a **---BEGIN CERTIFICATE---** és **---END CERTIFICATE---** címkék. Ezt követően a a **kulcs** mezőbe illessze be a maradék tartalmat.
    
    c. Az a **Id tulajdonsága** jelölje ki az Azure AD-ben konfigurált, a felhasználói azonosító attribútum. Például ha *userPrincipalName* van kiválasztva, az Azure AD-ben válassza **felhasználónév**.

    d. Az a **bejelentkezési URL-cím** mezőbe illessze be a **felhasználói hozzáférési URL-címe** az alkalmazás **tulajdonságok** az Azure portál oldalán.

    e. Az a **kijelentkezési URL-címe**, illessze be a **kijelentkezéses URL-cím** fájlból másolt érték a **bejelentkezés konfigurálása** ablakot, az Azure Portalon.

12. Váltógomb **csak az Egyszeri bejelentkezés engedélyezése** való **a**.

    ![Az Egyszeri bejelentkezés csak engedélyezése be-vagy kikapcsolása](./media/absorblms-tutorial/5.png)

13. Válassza ki **mentéséhez.**

> [!TIP]
> Tudjon meg ezeket az utasításokat a tömör verzióját a [az Azure portal](https://portal.azure.com) közben állítja be az alkalmazást. Az alkalmazás hozzáadása után a **Active Directory** > **vállalati alkalmazások** szakaszban jelölje be a **egyszeri bejelentkezés** lapra, és a beágyazott eléréséhez dokumentáció a **konfigurációs** alul található szakaszában. További információkért lásd: [Azure ad-ben a beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon Britta Simon tesztfelhasználót hoz létre.

![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

Az Azure ad-ben hozzon létre egy tesztfelhasználót, tegye a következőket:

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](./media/absorblms-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez válassza **felhasználók és csoportok** > **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/absorblms-tutorial/create_aaduser_02.png) 

3. A párbeszédpanel tetején válassza **Hozzáadás**.
 
    ![A Hozzáadás gombra.](./media/absorblms-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanelen tegye a következőket:
 
    ![A felhasználó párbeszédpanel](./media/absorblms-tutorial/create_aaduser_04.png) 

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel az értéket a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.

### <a name="create-an-absorb-lms-test-user"></a>Hozzon létre egy LMS számára a tesztfelhasználó számára

Az Azure AD-felhasználók számára a LMS bejelentkezni azok kell beállítani az LMS számára.  

A telepítő LMS számára a manuális tevékenység.

Felhasználói fiók beállítása, tegye a következőket:

1. Jelentkezzen be rendszergazdaként az LMS számára a vállalati webhely.

2. A bal oldali panelen válassza ki a **felhasználók**.

    ![Az LMS-felhasználók számára a hivatkozás](./media/absorblms-tutorial/absorblms_users.png)

3. Az a **felhasználók** ablaktáblán válassza előbb **felhasználók**.

    ![A felhasználók hivatkozás](./media/absorblms-tutorial/absorblms_userssub.png)

4. Az a **új hozzáadása** legördülő listában válassza **felhasználói**.

    ![Az új hozzáadása legördülő lista](./media/absorblms-tutorial/absorblms_createuser.png)

5. Az a **felhasználó hozzáadása** lapon, tegye a következőket:

    ![A felhasználó hozzáadása oldalon](./media/absorblms-tutorial/user.png)

    a. Az a **Utónév** mezőbe írja be például a Keresztnév **Britta**.

    b. Az a **Vezetéknév** mezőbe írja be például a Vezetéknév **Simon**.
    
    c. Az a **felhasználónév** mezőbe írjon be egy teljes nevet, például **Britta Simon**.

    d. Az a **jelszó** Britta Simon jelszót írja be.

    e. Az a **jelszó megerősítése** mezőbe írja be ismét a jelszót.
    
    f. Állítsa be a **aktív** kapcsolót **aktív**.  

6. Válassza ki **mentéséhez.**
 
### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze felhasználói Britta Simon által biztosított hozzáférés LMS számára a Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200]

Britta Simon felhasználó hozzárendelése LMS elháríthatók, tegye a következőket:

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, nyissa meg a könyvtár nézet, és válassza **vállalati alkalmazások** > **minden alkalmazás**.

    ![Az "Összes alkalmazás" hivatkozásra][201] 

2. Az a **alkalmazások** listáról válassza ki **elháríthatók LMS**.

    ![Az alkalmazások listáját az LMS számára a hivatkozásra](./media/absorblms-tutorial/tutorial_absorblms_app.png) 

3. A bal oldali panelen válassza ki a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

4. Válassza ki **Hozzáadás** , majd a **hozzárendelés hozzáadása** ablaktáblán válassza **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panel][203]

5. Az a **felhasználók és csoportok** párbeszédpanel a **felhasználók** listáról válassza ki **Britta Simon**.

6. Az a **felhasználók és csoportok** párbeszédpanelen válassza ki a **kiválasztása** gombra.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza ki a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

A hozzáférési panelen válassza a **elháríthatók LMS** csempe automatikusan bejelentkezik, az LMS elháríthatók alkalmazás. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/absorblms-tutorial/tutorial_general_01.png
[2]: ./media/absorblms-tutorial/tutorial_general_02.png
[3]: ./media/absorblms-tutorial/tutorial_general_03.png
[4]: ./media/absorblms-tutorial/tutorial_general_04.png

[100]: ./media/absorblms-tutorial/tutorial_general_100.png

[200]: ./media/absorblms-tutorial/tutorial_general_200.png
[201]: ./media/absorblms-tutorial/tutorial_general_201.png
[202]: ./media/absorblms-tutorial/tutorial_general_202.png
[203]: ./media/absorblms-tutorial/tutorial_general_203.png
