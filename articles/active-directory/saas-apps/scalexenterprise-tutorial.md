---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a vállalati ScaleX |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés ScaleX nagyvállalati és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c2379a8d-a659-45f1-87db-9ba156d83183
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/20/2017
ms.author: jeedes
ms.openlocfilehash: c1847d268d7776fba2892257bf0992d0e17a727f
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152059"
---
# <a name="tutorial-azure-active-directory-integration-with-scalex-enterprise"></a>Oktatóanyag: Az Azure Active Directory-integráció a vállalati ScaleX

Ebben az oktatóanyagban elsajátíthatja, hogyan ScaleX vállalati integrálása az Azure Active Directory (Azure AD).

ScaleX vállalati integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá nagyvállalati ScaleX Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett ScaleX Enterprise (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd. Mi az alkalmazás-hozzáférés és egyszeri bejelentkezést az [Azure Active Directory](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a ScaleX vállalati, a következőkre van szükség:

- Azure AD-előfizetés
- Egy vállalati ScaleX egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, kivéve, ha erre szükség.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. ScaleX vállalati hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-scalex-enterprise-from-the-gallery"></a>ScaleX vállalati hozzáadása a katalógusból
ScaleX vállalat az Azure AD-integrációjának konfigurálásához hozzá kell ScaleX vállalati a galériából a felügyelt SaaS-alkalmazások listájára.

**ScaleX vállalati hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **ScaleX vállalati**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_search.png)

1. Az eredmények panelen válassza ki a **ScaleX vállalati**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és a egy "Britta Simon." nevű tesztelési felhasználó alapján ScaleX vállalat az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó ScaleX vállalati mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó ScaleX vállalati hivatkozás kapcsolatát kell létrehozni.

Ez a hivatkozás-kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** ScaleX vállalati.

Az Azure AD egyszeri bejelentkezés a vállalati ScaleX tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Teszt ScaleX vállalati felhasználó létrehozása](#creating-a-scalex-enterprise-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon ScaleX vállalat, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az ScaleX vállalati alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés a vállalati ScaleX, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **ScaleX vállalati** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédpanelen, **mód** válassza **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_samlbase.png)

1. Az a **ScaleX vállalati tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url1.png)

    a. Az a **azonosító** szövegmezőbe írja be az értéket a következő minta használatával: `https://platform.rescale.com/saml2/<company id>/`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://platform.rescale.com/saml2/<company id>/acs/`

1. Ellenőrizze **speciális URL-beállítások megjelenítése**, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_url2.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be az értéket a következő minta használatával: `https://platform.rescale.com/saml2/<company id>/sso/`
     
    > [!NOTE] 
    > Ezek eltérnek a valós értékeket. Frissítse a tényleges azonosítója, válasz URL-cím vagy a bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [ScaleX vállalati ügyfél-támogatási csapatának](https://info.rescale.com/contact_sales) beolvasni ezeket az értékeket. 

1. ScaleX alkalmazását a SAML helyességi feltételek vár egy megadott formátumban, amelyek megkövetelik a SAML-jogkivonat attribútumai konfiguráció egyéni attribútumleképezések módosítása. Kattintson a **megtekintése és egyéb felhasználói attribútumok szerkesztése** jelölőnégyzetet, nyissa meg az egyéni attribútumok beállításait.

    ![Egyszeri bejelentkezés konfigurálása](./media/scalexenterprise-tutorial/scalex_attributes.png)
    
    a. Kattintson a jobb gombbal az attribútum **neve** , és kattintson a Törlés gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/scalexenterprise-tutorial/delete_attribute_name.png)

    b. Kattintson a **emailaddress** attribútum szerkesztése attribútum ablak megnyitásához. Módosítsa az értéket **user.mail** való **user.userprincipalname** kattintson az OK gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/scalexenterprise-tutorial/edit_email_attribute.png) 
    
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/scalexenterprise-tutorial/tutorial_general_400.png)
    
1. Az a **ScaleX vállalati konfiguráció** területén kattintson **konfigurálása ScaleX vállalati** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító** és **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **ScaleX vállalati** ügyféloldali, jelentkezzen be rendszergazdaként a ScaleX vállalati vállalati webhely.

1. Kattintson a jobb oldali, és válassza a felső menüben **Contoso felügyeleti**.

    > [!NOTE] 
    > Contoso csak egy példa. Ez a tényleges vállalat neve legyen. 

    ![Egyszeri bejelentkezés konfigurálása](./media/scalexenterprise-tutorial/Test_Admin.png) 

1. Válassza ki **Integrációk** a felső menüben, majd válassza **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása](./media/scalexenterprise-tutorial/admin_sso.png) 

1. Töltse ki az űrlapot a következőképpen:

    ![Egyszeri bejelentkezés konfigurálása](./media/scalexenterprise-tutorial/scalex_admin_save.png) 
    
    a. Válassza ki **"Hozzon létre egyszeri bejelentkezés bármely felhasználó, aki hitelesítheti."**

    b. **Szolgáltató saml**: Illessze be az értéket ***urn: oasis: nevek: tc: SAML:2.0:nameid-formátum: állandó***

    c. **Az ACS-válaszban identitásszolgáltató e-mail mező neve**: Illessze be az értéket `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. **Identity Provider EntityDescriptor entitás azonosítója:** Illessze be a **SAML Entitásazonosító** az Azure Portalról másolt érték.

    e. **Identitásszolgáltató szolgáltató SingleSignOnService URL-címe:** Illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** az Azure Portalról.

    f. **Identitás-szolgáltató nyilvános X509 tanúsítvány:** Nyissa meg a X509 tanúsítvány a Jegyzettömbben az Azure-ból letöltött, és illessze be a tartalmakat ebbe a mezőbe. Győződjön meg róla, nincsenek a nincs sortöréseket a tanúsítvány tartalma közepén.
    
    g. Ellenőrizze a következő jelölőnégyzeteket: **NameID engedélyezve van, titkosítására és AuthnRequests jelentkezzen be.**

    h. Kattintson a **frissítés egyszeri bejelentkezési beállításainak** a beállítások mentéséhez.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/scalexenterprise-tutorial/create_aaduser_01.png) 

1. Lépjen a **felhasználók és csoportok** kattintson **minden felhasználó** felhasználók listájának megjelenítéséhez.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/scalexenterprise-tutorial/create_aaduser_02.png) 

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/scalexenterprise-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/scalexenterprise-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-scalex-enterprise-test-user"></a>Teszt ScaleX vállalati felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók ScaleX vállalati jelentkezzen be, akkor ki kell építeni a ScaleX Enterprise. Esetén ScaleX Enterprise kiépítés egy automatikus feladat, és nem manuális lépések szükségesek. Bármely felhasználó, akik sikeresen hitelesítik magukat az egyszeri bejelentkezési hitelesítő adatok automatikusan kiépítheti ScaleX oldalán.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés ScaleX vállalati felhasználó hozzáférésének engedélyezésére használja.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel ScaleX Enterprise, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **ScaleX vállalati**.

    ![Egyszeri bejelentkezés konfigurálása](./media/scalexenterprise-tutorial/tutorial_scalexenterprise_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

A vállalati ScaleX csempére a hozzáférési panelen, meg fog lekérése automatikusan bejelentkezett a ScaleX vállalati alkalmazáshoz. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).


## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/scalexenterprise-tutorial/tutorial_general_01.png
[2]: ./media/scalexenterprise-tutorial/tutorial_general_02.png
[3]: ./media/scalexenterprise-tutorial/tutorial_general_03.png
[4]: ./media/scalexenterprise-tutorial/tutorial_general_04.png

[100]: ./media/scalexenterprise-tutorial/tutorial_general_100.png

[200]: ./media/scalexenterprise-tutorial/tutorial_general_200.png
[201]: ./media/scalexenterprise-tutorial/tutorial_general_201.png
[202]: ./media/scalexenterprise-tutorial/tutorial_general_202.png
[203]: ./media/scalexenterprise-tutorial/tutorial_general_203.png

