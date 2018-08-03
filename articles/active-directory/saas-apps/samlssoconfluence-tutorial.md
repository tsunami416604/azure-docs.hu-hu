---
title: 'Oktatóanyag: Azure Active Directory-integráció való összefolyás felett az SAML SSO-val felbontása GmbH |} A Microsoft Docs'
description: Ismerje meg, hogy egyszeri bejelentkezéssel való összefolyás felett az SAML SSO és az Azure Active Directory közötti felbontása GmbH konfigurálása.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 6b47d483-d3a3-442d-b123-171e3f0f7486
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/18/2017
ms.author: jeedes
ms.openlocfilehash: c1a1126026f3d2618a0669e4bd69a84cc1c6c54c
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431623"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-confluence-by-resolution-gmbh"></a>Oktatóanyag: Azure Active Directory-integráció való összefolyás felett az SAML SSO-val GmbH felbontása

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálhatja való összefolyás felett az SAML SSO GmbH az Azure Active Directoryval (Azure AD-) megoldás.

Való összefolyás felett az SAML SSO integrálása az Azure ad-vel GmbH felbontása nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá az SAML SSO való összefolyás felett a felbontása GmbH Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a SAML SSO való összefolyás felett a felbontása GmbH (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs konfigurálni való összefolyás felett az SAML SSO-feloldási GmbH, a következő elemek szükségesek:

- Az Azure AD-előfizetéshez
- A SAML egyszeri bejelentkezés az felbontása GmbH egyszeri bejelentkezés engedélyezve van az előfizetésben való összefolyás felett

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Feloldási GmbH való összefolyás felett az SAML SSO hozzáadását a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-saml-sso-for-confluence-by-resolution-gmbh-from-the-gallery"></a>Feloldási GmbH való összefolyás felett az SAML SSO hozzáadását a katalógusból

Integráció konfigurálásához való összefolyás felett az SAML SSO-feloldási GmbH által az Azure AD-be, hozzá kell való összefolyás felett az SAML SSO GmbH felbontása a galériából a felügyelt SaaS-alkalmazások listájára.

**Feloldási GmbH való összefolyás felett az SAML SSO hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **felbontása GmbH való összefolyás felett az SAML SSO**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samlssoconfluence-tutorial/tutorial_samlssoconfluence_search.png)

1. Az eredmények panelen válassza ki a **felbontása GmbH való összefolyás felett az SAML SSO**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samlssoconfluence-tutorial/tutorial_samlssoconfluence_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés való összefolyás felett az SAML SSO-val egy "Britta Simon." nevű tesztelési felhasználó alapján GmbH felbontása

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, milyen megfelelőjére lévő felhasználó számára való összefolyás felett SAML SSO felbontása GmbH, hogy egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a SAML SSO való összefolyás felett a megoldás által hivatkozás kapcsolatának GmbH kell létrehozni.

SAML egyszeri bejelentkezés az felbontása GmbH való összefolyás felett, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés való összefolyás felett az SAML SSO-val felbontása GmbH tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Egy SAML egyszeri bejelentkezés az való összefolyás felett által feloldási GmbH tesztfelhasználó létrehozása](#creating-a-saml-sso-for-confluence-by-resolution-gmbh-test-user)**  – való összefolyás felett az SAML SSO-megfelelője a Britta Simon van megoldás, amely kapcsolódik az Azure AD felhasználói ábrázolása GmbH szerint.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, majd állítsa egyszeri bejelentkezés a SAML egyszeri bejelentkezés az való összefolyás felett felbontása GmbH alkalmazás.

**Konfigurálja az Azure AD egyszeri bejelentkezéssel való összefolyás felett az SAML SSO-val feloldási GmbH, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **felbontása GmbH való összefolyás felett az SAML SSO** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/tutorial_samlssoconfluence_samlbase.png)

1. Az a **SAML SSO való összefolyás felett felbontása GmbH tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/tutorial_samlssoconfluence_url_1.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/samlsso`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/samlsso`

1. Ellenőrizze **speciális URL-beállítások megjelenítése**. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/tutorial_samlssoconfluence_url_2.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<server-base-url>/plugins/servlet/samlsso`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [való összefolyás felett feloldási GmbH ügyfél által a SAML SSO támogatási csoportjának](https://www.resolution.de/go/support) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/tutorial_samlssoconfluence_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/tutorial_general_400.png)    
    
1. Egy másik böngészőablakban, jelentkezzen be a **való összefolyás felett feloldási GmbH felügyeleti portál által a SAML SSO** rendszergazdaként.

1. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **bővítmények**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon1.png)

1. Ekkor megnyílik a rendszergazdai hozzáférés lap. Adja meg a jelszót, és kattintson a **megerősítése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon2.png)

1. A **ATLASSIAN MARKETPLACE** lapra, majd **új bővítmények keresése**. 

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon.png)

1. Keresés **SAML egyszeri bejelentkezés (SSO) az való összefolyás felett** kattintson **telepítése** gombra az új SAML beépülő modul telepítéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon7.png)

1. A beépülő modul telepítése elindul. Kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon8.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon9.png)

1.  Kattintson a **Kezelés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon10.png)
    
1. Kattintson a **konfigurálása** a új beépülő modul konfigurálásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon11.png)

1. Az új beépülő modult is területen található **felhasználók és biztonsági** fülre.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon3.png)
    
1. A **SAML egyszeri bejelentkezés beépülő modul konfigurációs** kattintson **adja hozzá az új identitásszolgáltató** gombra kattintva adja meg a beállításokat az identitásszolgáltató.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon4.png)

1. A **válassza ki a SAML-identitásszolgáltató** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon5a.png)
 
    a. Állítsa be **Azure ad-ben** az identitásszolgáltató típusa.
    
    b. Adjon hozzá **neve** az identitásszolgáltató (például: Azure ad-ben).
    
    c. Adjon hozzá **leírás** az identitásszolgáltató (például: Azure ad-ben).
    
    d. Kattintson a **Tovább** gombra.
    
1. A **identitás-szolgáltató konfigurációjának** kattintson **tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon5b.png)

1. A **SAML-identitásszolgáltató metaadatok importálása** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon5c.png)

    a. Kattintson a **fájl betöltése** gombra, és 5. lépésében letöltött metaadatainak XML-fájlt válasszon.

    b. Kattintson a **importálás** gombra.
    
    c. Várjon rövid ideig, amíg importálása sikeres.
    
    d. Kattintson a **tovább** gombra.
    
1. A **felhasználói azonosító attribútum és -átalakítási** kattintson **tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon5d.png)
    
1. A **felhasználói létrehozáskor és frissítéskor** kattintson **Mentés & következő** beállításainak mentése.    
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon6a.png)
    
1. A **-beállítások tesztelése** kattintson **ugorja & manuális konfigurálása** ugorja most a felhasználó. Ez a következő szakaszban történik, és bizonyos beállítások az Azure Portalon igényel. 
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon6b.png)
    
1. Apprearing párbeszédpanel olvasási **a rendszer kihagyja a teszt azt jelenti, hogy...** , kattintson a **OK**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/addon6c.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samlssoconfluence-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samlssoconfluence-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samlssoconfluence-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/samlssoconfluence-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-saml-sso-for-confluence-by-resolution-gmbh-test-user"></a>Egy SAML egyszeri bejelentkezés az való összefolyás felett feloldási GmbH teszt felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók által feloldási GmbH való összefolyás felett az SAML SSO jelentkezzen be, azok ki kell építeni az SAML SSO való összefolyás felett az GmbH felbontása.  
SAML egyszeri bejelentkezés az való összefolyás felett felbontása GmbH a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a feloldási GmbH vállalati hely való összefolyás felett az SAML SSO.

1. Vigye a mutatót a fogaskerék alakú ikonjára, majd kattintson a **felhasználókezelés**.

    ![Alkalmazott hozzáadása](./media/samlssoconfluence-tutorial/user1.png) 

1. A felhasználók szakaszban kattintson a **felhasználók hozzáadása** fülre. Az a **"Egy felhasználó hozzáadása"** párbeszédpanel lapon, a következő lépésekkel:

    ![Alkalmazott hozzáadása](./media/samlssoconfluence-tutorial/user2.png) 

    a. Az a **felhasználónév** szövegmezőbe írja be a felhasználó Britta Simon például az e-mailt.

    b. Az a **teljes fájlvisszaállítási név** szövegmezőbe írja be például a Britta Simon felhasználó teljes nevét.

    c. Az a **E-mail** szövegmezőbe írja be az e-mail-cím, felhasználó, például Brittasimon@contoso.com.

    d. Az a **jelszó** szövegmezőbe írja be a jelszót a Britta Simon.

    e. Kattintson a **jelszó megerősítése** írja be újból a jelszót.
    
    f. Kattintson a **Hozzáadás** gombra.    

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés való összefolyás felett az SAML SSO felbontása GmbH Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel való összefolyás felett az SAML SSO felbontása GmbH, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **felbontása GmbH való összefolyás felett az SAML SSO**.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssoconfluence-tutorial/tutorial_samlssoconfluence_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Az SAML egyszeri bejelentkezés az való összefolyás felett kattintva által feloldási GmbH csempe a hozzáférési panelen, kell lekérése automatikusan bejelentkezett a SAML egyszeri bejelentkezés az való összefolyás felett, felbontása GmbH alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/samlssoconfluence-tutorial/tutorial_general_01.png
[2]: ./media/samlssoconfluence-tutorial/tutorial_general_02.png
[3]: ./media/samlssoconfluence-tutorial/tutorial_general_03.png
[4]: ./media/samlssoconfluence-tutorial/tutorial_general_04.png

[100]: ./media/samlssoconfluence-tutorial/tutorial_general_100.png

[200]: ./media/samlssoconfluence-tutorial/tutorial_general_200.png
[201]: ./media/samlssoconfluence-tutorial/tutorial_general_201.png
[202]: ./media/samlssoconfluence-tutorial/tutorial_general_202.png
[203]: ./media/samlssoconfluence-tutorial/tutorial_general_203.png

