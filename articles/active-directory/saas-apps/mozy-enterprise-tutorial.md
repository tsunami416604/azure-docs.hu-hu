---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a vállalati Mozy |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Mozy nagyvállalati és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: jeedes
ms.openlocfilehash: 5bbaa90554e09d27a3c521d4a13eda44021721c8
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55176896"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Oktatóanyag: Az Azure Active Directory-integráció a vállalati Mozy

Ebben az oktatóanyagban elsajátíthatja, hogyan Mozy vállalati integrálása az Azure Active Directory (Azure AD).

Mozy vállalati integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá nagyvállalati Mozy Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Mozy Enterprise (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Mozy vállalati, a következőkre van szükség:

- Azure AD-előfizetés
- Egy vállalati Mozy egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Mozy vállalati hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-mozy-enterprise-from-the-gallery"></a>Mozy vállalati hozzáadása a katalógusból
Az Azure AD-be Mozy vállalati integráció konfigurálásához, hozzá kell Mozy vállalati a galériából a felügyelt SaaS-alkalmazások listájára.

**Mozy vállalati hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Mozy vállalati**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_search.png)

1. Az eredmények panelen válassza ki a **Mozy vállalati**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálja, és az Azure AD egyszeri bejelentkezés tesztelése a Mozy vállalati "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Mozy vállalati mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Mozy vállalati hivatkozás kapcsolatát kell létrehozni.

Mozy Enterprise, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a vállalati Mozy tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Teszt Mozy vállalati felhasználó létrehozása](#creating-a-mozy-enterprise-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon Mozy vállalat, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Mozy vállalati alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés a vállalati Mozy, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Mozy vállalati** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_samlbase.png)

1. Az a **Mozy vállalati tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_url.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Mozy vállalati ügyfél-támogatási csapatának](http://support.mozy.com/) lekérni ezt az értéket.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/mozy-enterprise-tutorial/tutorial_general_400.png)

1. Az a **Mozy vállalati konfiguráció** területén kattintson **konfigurálása Mozy vállalati** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a vállalati Mozy vállalati hely rendszergazdaként.

1. Az a **konfigurációs** területén kattintson **hitelesítési házirend**.
   
   ![Hitelesítési házirend](./media/mozy-enterprise-tutorial/ic777314.png "hitelesítési házirend")

1. Az a **hitelesítési házirend** szakaszban, hajtsa végre az alábbi lépéseket:
   
   ![Hitelesítési házirend](./media/mozy-enterprise-tutorial/ic777315.png "hitelesítési házirend")
   
   a. Válassza ki **címtárszolgáltatás** , **szolgáltató**.
   
   b. Válassza ki **LDAP leküldéses használatához**.
   
   c. Kattintson a **SAML-hitelesítés** fülre.
   
   d. Beillesztés **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, azokat az Azure Portalról másolt a **hitelesítési URL-cím** szövegmezőbe.
   
   e. Beillesztés **SAML Entitásazonosító**, azokat az Azure Portalról másolt a **SAML-végpont** szövegmezőbe.
   
   f. A Jegyzettömb alkalmazásban nyissa meg a letöltött base-64 kódolású tanúsítvány, a tartalmát a vágólapra másolja és illessze be a teljes tanúsítványt **SAML-tanúsítvány** szövegmezőbe.
   
   g. Válassza ki **SSO engedélyezése rendszergazdák számára a hálózati hitelesítő adataikkal jelentkezzenek be**.
   
   h. Kattintson a **módosítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mozy-enterprise-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mozy-enterprise-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mozy-enterprise-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/mozy-enterprise-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-mozy-enterprise-test-user"></a>Teszt Mozy vállalati felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Mozy Enterprise-ba való bejelentkezéshez, akkor ki kell építeni Mozy vállalati. Esetén Mozy Enterprise a manuális tevékenység kiépítése.

>[!NOTE]
>Bármely más Mozy vállalati felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését Mozy vállalati által biztosított API-k.

**A felhasználói fiókok kiépítése, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Mozy vállalati** bérlő.

1. Kattintson a **felhasználók**, és kattintson a **új felhasználó hozzáadása**.
   
   ![Felhasználók](./media/mozy-enterprise-tutorial/ic777317.png "felhasználók")
   
   >[!NOTE]
   >A **új felhasználó hozzáadása** beállítás csak akkor látható, csak ha **Mozy** alapján a szolgáltató van kiválasztva **hitelesítési házirend**. Ha SAML-hitelesítés van konfigurálva, majd a felhasználók automatikusan hozzáadja az egyszeri bejelentkezés révén az első bejelentkezésnél a.
    
1. Az új felhasználó párbeszédpanelen hajtsa végre a következő lépéseket:
   
   ![Felhasználók hozzáadása](./media/mozy-enterprise-tutorial/ic777318.png "felhasználók hozzáadása")
   
   a. Az a **válasszon egy csoportot** listában, válasszon ki egy csoportot.
   
   b. Az a **milyen típusú felhasználói** listában, válasszon egy típust.
   
   c. Az a **felhasználónév** szövegmezőbe írja be az Azure AD-felhasználó nevét.
   
   d. Az a **E-mail** szövegmezőbe írja be az Azure AD-felhasználó e-mail-címét.
   
   e. Válassza ki **felhasználói utasítás e-mailt**.
   
   f. Kattintson a **felhasználó(k) hozzáadása**.

     >[!NOTE]
     > Miután létrehozta a felhasználó, egy e-mailt kapnak, amelyben a hivatkozást, mielőtt aktívvá válik, győződjön meg arról, hogy a fiók az Azure AD-felhasználót.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Mozy vállalati Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Mozy Enterprise, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Mozy vállalati**.

    ![Egyszeri bejelentkezés konfigurálása](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Mozy vállalati csempére kattint, Mozy vállalati alkalmazás bejelentkezési oldalának szerezheti be.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mozy-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/mozy-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/mozy-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/mozy-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/mozy-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/mozy-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/mozy-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/mozy-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/mozy-enterprise-tutorial/tutorial_general_203.png

