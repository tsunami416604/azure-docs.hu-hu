---
title: 'Oktatóanyag: Azure Active Directory-integráció az SAML SSO a Jira felbontása GmbH |} A Microsoft Docs'
description: Ismerje meg, hogyan konfigurálása egyszeri bejelentkezéshez tartozó Jira SAML SSO és az Azure Active Directory közötti GmbH felbontása.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: 05a91e66d046bb7869179175c3a7d0b13b1942e4
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39042190"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Oktatóanyag: Azure Active Directory-integráció az SAML SSO a Jira GmbH felbontása

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja a Jira SAML SSO GmbH az Azure Active Directoryval (Azure AD-) megoldás.

A Jira SAML SSO integrálása feloldási GmbH az Azure AD által nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá az SAML SSO a Jira felbontása GmbH Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a SAML SSO a Jira felbontása GmbH (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálni az Azure AD-integrációs SAML SSO a Jira-feloldási GmbH, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A SAML SSO a Jira feloldási GmbH egyszeri bejelentkezés engedélyezve előfizetés szerint

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Feloldási GmbH SAML SSO a Jira hozzáadását a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-saml-sso-for-jira-by-resolution-gmbh-from-the-gallery"></a>Feloldási GmbH SAML SSO a Jira hozzáadását a katalógusból
Integráció konfigurálásához a Jira SAML SSO-feloldási GmbH által az Azure AD-be, hozzá kell SAML SSO a Jira felbontása GmbH a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Jira SAML SSO GmbH felbontása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **SAML SSO a Jira felbontása GmbH**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samlssojira-tutorial/tutorial_samlssojira_search.png)

5. Az eredmények panelen válassza ki a **SAML SSO a Jira felbontása GmbH**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samlssojira-tutorial/tutorial_samlssojira_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Jira SAML SSO-val egy "Britta Simon." nevű tesztelési felhasználó alapján GmbH felbontása

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó a SAML SSO a Jira felbontása GmbH, hogy egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a SAML SSO a Jira felbontása hivatkozás kapcsolata GmbH kell létrehozni.

SAML egyszeri bejelentkezés az Jira felbontása GmbH, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a Jira SAML SSO-val felbontása GmbH tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[A Jira feloldási GmbH teszt felhasználó által a SAML SSO létrehozása](#creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user)**  – SAML SSO a Jira-megfelelője a Britta Simon van megoldás, amely kapcsolódik az Azure AD felhasználói ábrázolása GmbH szerint.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, majd állítsa egyszeri bejelentkezéshez a SAML SSO a Jira felbontása GmbH alkalmazás.

**Konfigurálja az Azure AD egyszeri bejelentkezés a Jira SAML SSO-val feloldási GmbH, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon a a **SAML SSO a Jira felbontása GmbH** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/tutorial_samlssojira_samlbase.png)

3. Az a **SAML SSO Jira felbontása GmbH tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/tutorial_samlssojira_url_1.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/samlsso`

4. Ellenőrizze **speciális URL-beállítások megjelenítése**. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/tutorial_samlssojira_url_2.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [Jira feloldási GmbH ügyfél által a SAML SSO támogatási csoportjának](https://www.resolution.de/go/support) beolvasni ezeket az értékeket. 

5. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/tutorial_samlssojira_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/tutorial_general_400.png)
    
7. Egy másik böngészőablakban, jelentkezzen be a **Jira feloldási GmbH felügyeleti portál által a SAML SSO** rendszergazdaként.

8. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **bővítmények**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon1.png)

9. Ekkor megnyílik a rendszergazdai hozzáférés lap. Adja meg a **jelszó** kattintson **megerősítése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon2.png)

10. A bővítmények lapon szakasz alatt kattintson **új bővítmények keresése**. Keresés **SAML egyszeri bejelentkezés (SSO) a JIRA** kattintson **telepítése** gombra az új SAML beépülő modul telepítéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon7.png)

11. A beépülő modul telepítése elindul. Kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon8.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon9.png)

12. Kattintson a **Kezelés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon10.png)
    
13. Kattintson a **konfigurálása** a új beépülő modul konfigurálásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon11.png)

14. A **SAML egyszeri bejelentkezés beépülő modul konfigurációs** kattintson **adja hozzá az új identitásszolgáltató** gombra kattintva adja meg a beállításokat az identitásszolgáltató.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon4.png)

15. A **válassza ki a SAML-identitásszolgáltató** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon5a.png)
 
    a. Állítsa be **Azure ad-ben** az identitásszolgáltató típusa.
    
    b. Adjon hozzá **neve** az identitásszolgáltató (például: Azure ad-ben).
    
    c. Adjon hozzá **leírás** az identitásszolgáltató (például: Azure ad-ben).
    
    d. Kattintson a **Tovább** gombra.
    
16. A **identitás-szolgáltató konfigurációjának** kattintson **tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon5b.png)

17. A **SAML-identitásszolgáltató metaadatok importálása** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon5c.png)

    a. Kattintson a **fájl betöltése** gombra, és 5. lépésében letöltött metaadatainak XML-fájlt válasszon.

    b. Kattintson a **importálás** gombra.
    
    c. Várjon rövid ideig, amíg importálása sikeres.
    
    d. Kattintson a **tovább** gombra.
    
18. A **felhasználói azonosító attribútum és -átalakítási** kattintson **tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon5d.png)
    
19. A **felhasználói létrehozáskor és frissítéskor** kattintson **Mentés & következő** beállításainak mentése.   
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon6a.png)
    
20. A **-beállítások tesztelése** kattintson **ugorja & manuális konfigurálása** ugorja most a felhasználó. Ez a következő szakaszban történik, és bizonyos beállítások az Azure Portalon igényel. 
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon6b.png)
    
21. Apprearing párbeszédpanel olvasási **a rendszer kihagyja a teszt azt jelenti, hogy...** , kattintson a **OK**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon6c.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samlssojira-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samlssojira-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samlssojira-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samlssojira-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-saml-sso-for-jira-by-resolution-gmbh-test-user"></a>A SAML SSO a Jira feloldási GmbH teszt felhasználó létrehozása

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

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SAML SSO a Jira felbontása GmbH Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel a Jira SAML SSO felbontása GmbH, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **SAML SSO a Jira felbontása GmbH**.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/tutorial_samlssojira_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Kattintás után a SAML SSO a Jira által feloldási GmbH csempe a hozzáférési panelen, meg kell lekérése automatikusan bejelentkezett, a SAML SSO a Jira felbontása GmbH alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/samlssojira-tutorial/tutorial_general_01.png
[2]: ./media/samlssojira-tutorial/tutorial_general_02.png
[3]: ./media/samlssojira-tutorial/tutorial_general_03.png
[4]: ./media/samlssojira-tutorial/tutorial_general_04.png

[100]: ./media/samlssojira-tutorial/tutorial_general_100.png

[200]: ./media/samlssojira-tutorial/tutorial_general_200.png
[201]: ./media/samlssojira-tutorial/tutorial_general_201.png
[202]: ./media/samlssojira-tutorial/tutorial_general_202.png
[203]: ./media/samlssojira-tutorial/tutorial_general_203.png

