---
title: 'Oktatóanyag: Azure Active Directory-integráció adapterrel Five9 plusz (CTI, forduljon Center-ügynökökkel) |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeedes
ms.openlocfilehash: d5c6b2c658a899b3c4363803dc3858cc2b6bab46
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34345431"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Oktatóanyag: Azure Active Directory-integráció adapterrel Five9 plusz (CTI, forduljon Center-ügynökökkel)

Ebben az oktatóanyagban megismerheti, hogyan integrálhatja Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) az Azure Active Directoryval (Azure AD).

Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja az Azure AD, aki hozzáfér Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel)
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Five9 Plus adapterhez (CTI, forduljon Center-ügynökökkel) (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása adapterrel Five9 plusz (CTI, forduljon Center-ügynökökkel), a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió Itt kaphat: [próbaverzió ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>A gyűjteményből Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) hozzáadása
Konfigurálja Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) integrálása az Azure AD-be, meg kell hozzáadnia Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adjon hozzá Five9 Plus adaptert (CTI, forduljon Center-ügynökökkel) a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel)**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-five9-tutorial/tutorial_five9_search.png)

5. Az eredmények panelen válassza ki a **Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel)**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-five9-tutorial/tutorial_five9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD az egyszeri bejelentkezés adapterrel Five9 plusz (CTI, forduljon Center-ügynökökkel) "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Five9 Plus adaptert (CTI, forduljon Center-ügynökökkel) a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) közötti kapcsolat kapcsolatot kell létrehozni.

Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel), rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés adapterrel Five9 plusz (CTI, forduljon Center-ügynökökkel) tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) tesztfelhasználó létrehozása](#creating-a-five9-plus-adapter-cti-contact-center-agents-test-user)**  - való egy megfelelője a Britta Simon Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel), amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) alkalmazásban.

**Konfigurálja az Azure AD az egyszeri bejelentkezés adapterrel Five9 plusz (CTI, forduljon Center-ügynökökkel), hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel)** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-five9-tutorial/tutorial_five9_samlbase.png)

3. Az a **Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) tartományhoz és URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-five9-tutorial/tutorial_five9_url.png)
    
    a. Az a **azonosító** szövegmezőhöz URL-címet a következő minták használatával írja be:

    |    Környezet      |       URL-cím      |
    | :-- | :-- |
    | "Five9, valamint a Microsoft Dynamics CRM Adapter" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | A "Five9 plusz Zendesk-adaptert" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | "Five9, valamint az ügynök asztali eszközkészlet Adapter" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:

    |      Környezet     |      URL-cím      |
    | :--                  | :--           |
    | "Five9, valamint a Microsoft Dynamics CRM Adapter" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | A "Five9 plusz Zendesk-adaptert" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | "Five9, valamint az ügynök asztali eszközkészlet Adapter" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-five9-tutorial/tutorial_five9_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-five9-tutorial/tutorial_general_400.png)

6. A a **Five9 plusz (CTI, forduljon Center-ügynökökkel) adapterkonfiguráció** kattintson **konfigurálása Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel)** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-five9-tutorial/tutorial_five9_configure.png) 

7. Egyszeri bejelentkezés konfigurálása **Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel)** oldalon kell küldeniük a letöltött **Certificate(Base64), Sign-Out URL-címe, SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** való [Five9 plusz (CTI, forduljon Center-ügynökökkel) támogatási adaptercsoport](https://www.five9.com/about/contact). Ezen kívül is SSO további konfigurálásához kövesse a következő lépések alapján a adapter:

    a. "Five9 Plus ügynök asztali eszközkészlet adaptert" rendszergazdai útmutató: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. "Five9 Plus Adapter a Microsoft Dynamics CRM" rendszergazdai útmutató: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Five9 plusz Zendesk-adaptert" rendszergazdai útmutató: [http://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](http://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)


> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-five9-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-five9-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-five9-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-five9-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználó Britta Simon meghívta Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) hoz létre. Együttműködve [Five9 plusz (CTI, forduljon Center-ügynökökkel) támogatási adaptercsoport](https://www.five9.com/about/contact) a felhasználók hozzáadása a Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés által biztosított hozzáférés Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) használatára.

![Felhasználó hozzárendelése][200] 

**Rendeljen Britta Simon Five9 Plus adapterhez (CTI, forduljon Center-ügynökökkel), hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel)**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-five9-tutorial/tutorial_five9_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Five9 Plus Adapter (CTI, forduljon Center-ügynökökkel) alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/active-directory-saas-five9-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-five9-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-five9-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-five9-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-five9-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-five9-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-five9-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-five9-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-five9-tutorial/tutorial_general_203.png

