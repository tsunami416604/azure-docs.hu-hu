---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Zscaler privát hozzáférés rendszergazdája |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Zscaler privát hozzáférés rendszergazdája és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: c87392a7-e7fe-4cdc-a8e6-afe1ed975172
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2018
ms.author: jeedes
ms.openlocfilehash: 6eca534f933742e4797ca164637e7d536d194ba3
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55181503"
---
# <a name="tutorial-azure-active-directory-integration-with-zscaler-private-access-administrator"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Zscaler privát hozzáférés rendszergazdája

Ebben az oktatóanyagban elsajátíthatja, hogyan Zscaler privát hozzáférés rendszergazdája integrálása az Azure Active Directory (Azure AD).

Zscaler privát hozzáférés rendszergazdája integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Az Azure ad-ben a Zscaler privát hozzáférés rendszergazdai hozzáféréssel rendelkező szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Zscaler privát hozzáférés rendszergazdája (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Zscaler privát hozzáférés adminisztrátora, a következőkre van szükség:

- Azure AD-előfizetés
- A Zscaler privát hozzáférés rendszergazdai egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Zscaler privát hozzáférést rendszergazda hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-zscaler-private-access-administrator-from-the-gallery"></a>Zscaler privát hozzáférést rendszergazda hozzáadása a katalógusból
Az Azure AD-be Zscaler privát hozzáférést rendszergazda-integráció konfigurálása szüksége Zscaler privát hozzáférést rendszergazda hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Zscaler privát hozzáférést rendszergazda hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Zscaler privát hozzáférés rendszergazdája**, jelölje be **Zscaler privát hozzáférés rendszergazdája** eredmény panelen kattintson a **Hozzáadás** gombra kattintva adhat hozzá a az alkalmazás.

    ![Az eredmények listájában Zscaler privát hozzáférésű rendszergazda](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Zscaler privát hozzáférés rendszergazdája a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Zscaler privát hozzáférés rendszergazdája mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Zscaler privát hozzáférés rendszergazdája hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés Zscaler privát hozzáférés rendszergazdája tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Zscaler privát hozzáférés rendszergazdája tesztfelhasználót](#create-a-zscaler-private-access-administrator-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon a Zscaler privát hozzáférés rendszergazdája, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Zscaler privát hozzáférés rendszergazdája alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása Zscaler privát hozzáférés rendszergazdája, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Zscaler privát hozzáférés rendszergazdája** alkalmazás integrációs oldalán kattintson **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_samlbase.png)

1. Az a **Zscaler privát hozzáférés rendszergazdai tartomány és URL-címek** szakaszt, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Zscaler privát hozzáférés rendszergazdai tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.private.zscaler.com/auth/metadata`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.private.zscaler.com/auth/sso`

    c. Ellenőrizze **speciális URL-beállítások megjelenítése**

    d. Az a **paramétert** szövegmezőbe írja be egy értéket: `idpadminsso`

1.  Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett módban a következő lépésekkel:

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.private.zscaler.com/auth/sso`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-címet. Kapcsolattartó [Zscaler privát hozzáférés rendszergazdai támogatási csapatának](https://help.zscaler.com/zpa-submit-ticket) beolvasni ezeket az értékeket.
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_400.png)

1. Egy másik böngészőablakban, bejelentkezési Zscaler privát hozzáférés rendszergazdája rendszergazdaként.

1. A képernyő felső részén kattintson **felügyeleti** , és keresse meg **hitelesítési** szakaszban kattintson **identitásszolgáltató konfigurációja**.

    ![Zscaler Private Access Administrator admin](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

1. Kattintson a jobb felső sarokban **adja hozzá az identitásszolgáltató konfigurációja**. 

    ![Zscaler Private Access Administrator addidp](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addpidp.png)

1. Az a **adja hozzá az identitásszolgáltató konfigurációja** oldalon tegye a következőket:
 
    ![Zscaler privát hozzáférés rendszergazdája idpselect](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_idpselect.png)

    a. Kattintson a **fájl kiválasztása** feltölteni a letöltött metaadat-fájlt az Azure AD-ből a **IdP-metaadatok fájlfeltöltés** mező.

    b. Olvassa be, hogy a **identitásszolgáltató metaadatok** Azure AD-ből és tölti fel a mezők adatai az alább látható módon.

    ![Zscaler Private Access Administrator idpconfig](./media/zscalerprivateaccessadministrator-tutorial/idpconfig.png)

    c. Válassza ki **egyszeri bejelentkezés** , **rendszergazda**.

    d. Válassza ki a tartományt a **tartományok** mező.
    
    e. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/zscalerprivateaccessadministrator-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-zscaler-private-access-administrator-test-user"></a>Zscaler privát hozzáférés rendszergazdája tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Zscaler privát hozzáférés rendszergazdai bejelentkezéshez, akkor ki kell építeni be Zscaler privát hozzáférés rendszergazdája. Esetén Zscaler privát hozzáférés adminisztrátora, a manuális tevékenység kiépítése.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a Zscaler privát hozzáférés rendszergazdája vállalati webhely.

1. A képernyő felső részén kattintson **felügyeleti** , és keresse meg **hitelesítési** szakaszban kattintson **identitásszolgáltató konfigurációja**.

    ![Zscaler Private Access Administrator admin](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_admin.png)

1. Kattintson a **rendszergazdák** a bal oldali menüben.

    ![Zscaler Private Access Administrator administrator](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_adminstrator.png)

1. Kattintson a jobb felső sarokban **-rendszergazda felvétele**:

    ![Zscaler Private Access Administrator add admin](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_addadmin.png)

1. Az a **-rendszergazda felvétele** lapon, a következő lépésekkel:

    ![Zscaler privát hozzáférést rendszergazda felhasználó felügyeleti](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_useradmin.png)

    a. Az a **felhasználónév** szövegmezőben adja meg az e-mail címét, például a felhasználó **BrittaSimon@contoso.com**.

    b. Az a **jelszó** szövegmezőbe írja be a jelszót.

    c. Az a **jelszó megerősítése** szövegmezőbe írja be a jelszót.

    d. Válassza ki **szerepkör** , **Zscaler privát hozzáférés rendszergazdája**.

    e. Az a **E-mail** szövegmezőben adja meg az e-mail címét, például a felhasználó **BrittaSimon@contoso.com**.

    f. Az a **Phone** szövegmezőbe írja be a telefonszámot.

    g. Az a **időzóna** szövegmezőben válassza ki az időzónát.

    h. Kattintson a **Save** (Mentés) gombra.  

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Zscaler privát hozzáférés adminisztrátora, a hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon Zscaler privát hozzáférés rendszergazdája való hozzárendeléséhez végezze el az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Zscaler privát hozzáférés rendszergazdája**.

    ![A Zscaler privát hozzáférés rendszergazdája hivatkozásra az alkalmazások listáját](./media/zscalerprivateaccessadministrator-tutorial/tutorial_zscalerprivateaccessadministrator_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Zscaler privát hozzáférés rendszergazdája csempére kattint, meg kell beolvasása automatikusan bejelentkezett a Zscaler privát hozzáférés rendszergazdája alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_01.png
[2]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_02.png
[3]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_03.png
[4]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_04.png

[100]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_100.png

[200]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_200.png
[201]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_201.png
[202]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_202.png
[203]: ./media/zscalerprivateaccessadministrator-tutorial/tutorial_general_203.png

