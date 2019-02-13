---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Five9 Plus-adaptert (PBX, ügyfél-Center-ügynökök) |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 88dc82ab-be0b-4017-8335-c47d00775d7b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 124bc858e9950962d3ae0f69db8fb962a3725f87
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56206514"
---
# <a name="tutorial-azure-active-directory-integration-with-five9-plus-adapter-cti-contact-center-agents"></a>Oktatóanyag: Az Azure Active Directory-integráció Five9 Plus-adaptert (PBX, ügyfél-Center-ügynökök)

Ebben az oktatóanyagban megismerheti, hogyan Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) integrálható az Azure Active Directoryval (Azure AD).

Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Five9 plusz adapterhez (PBX, ügyfél-Center-ügynökök) (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Five9 Plus-adaptert (PBX, ügyfél-Center-ügynökök), a következőkre van szükség:

- Azure AD-előfizetés
- Egy Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség: [Próbaverziós ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-five9-plus-adapter-cti-contact-center-agents-from-the-gallery"></a>Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) hozzáadása a katalógusból
Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) integrálásának konfigurálása az Azure AD-be, hozzá kell Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) a galériából a felügyelt SaaS-alkalmazások listájára.

**Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök)**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/five9-tutorial/tutorial_five9_search.png)

1. Az eredmények panelen válassza ki a **Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök)**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/five9-tutorial/tutorial_five9_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés adapterrel Five9 plusz (PBX, ügyfél-Center-ügynökök) nevű, "Britta Simon" tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) hivatkozás kapcsolata kell létrehozni.

A Five9 plusz (PBX, ügyfél-Center-ügynökök) hozzárendelése az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** hivatkozás viszony.

Az Azure AD egyszeri bejelentkezés adapterrel Five9 plusz (PBX, ügyfél-Center-ügynökök) tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) tesztfelhasználó létrehozása](#creating-a-five9-plus-adapter-cti-contact-center-agents-test-user)**  – egy megfelelője a Britta Simon Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök), amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) alkalmazásában egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés adapterrel Five9 plusz (PBX, ügyfél-Center-ügynökök), hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök)** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/five9-tutorial/tutorial_five9_samlbase.png)

1. Az a **Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/five9-tutorial/tutorial_five9_url.png)
    
    a. Az a **azonosító** szövegmezőbe írja be a következő minták használatával URL-címe:

    |    Környezet      |       URL-cím      |
    | :-- | :-- |
    | "Five9, valamint a Microsoft Dynamics CRM Adapter" | `https://app.five9.com/appsvcs/saml/metadata/alias/msdc` |
    | "Five9 plusz Zendesk adaptert" | `https://app.five9.com/appsvcs/saml/metadata/alias/zd` |
    | "Five9, valamint az ügynök asztali eszközkészlet Adapter" | `https://app.five9.com/appsvcs/saml/metadata/alias/adt` |

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe:

    |      Környezet     |      URL-cím      |
    | :--                  | :--           |
    | "Five9, valamint a Microsoft Dynamics CRM Adapter" | `https://app.five9.com/appsvcs/saml/SSO/alias/msdc` |
    | "Five9 plusz Zendesk adaptert" | `https://app.five9.com/appsvcs/saml/SSO/alias/zd` |
    | "Five9, valamint az ügynök asztali eszközkészlet Adapter" | `https://app.five9.com/appsvcs/saml/SSO/alias/adt` |

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/five9-tutorial/tutorial_five9_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/five9-tutorial/tutorial_general_400.png)

1. Az a **Five9 plusz (PBX, ügyfél-Center-ügynökök) adapterkonfiguráció** területén kattintson **konfigurálása Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök)** megnyitásához **bejelentkezéskonfigurálása**ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/five9-tutorial/tutorial_five9_configure.png) 

1. Az egyszeri bejelentkezés konfigurálása **Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök)** oldalon kell küldenie a letöltött **Certificate(Base64), kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím**való [Five9 plusz (PBX, ügyfél-Center-ügynökök) támogatás adaptercsoporton](https://www.five9.com/about/contact). Is ezenkívül SSO további konfigurálásához kövesse az alábbi lépéseket a csatoló megfelelően:

    a. "Five9 plusz ügynök asztali eszközkészlet adaptert" rendszergazdai útmutató: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/agent-desktop-toolkit/plus-agent-desktop-toolkit-administrators-guide.pdf)
    
    b. "Five9 és a Microsoft Dynamics CRM Adapter" rendszergazdai útmutató: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/microsoft/microsoft-administrators-guide.pdf)
    
    c. "Five9 plusz Zendesk adaptert" rendszergazdai útmutató: [https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf](https://webapps.five9.com/assets/files/for_customers/documentation/integrations/zendesk/zendesk-plus-administrators-guide.pdf)


> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/five9-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/five9-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/five9-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/five9-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-five9-plus-adapter-cti-contact-center-agents-test-user"></a>Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) a felhasználó hoz létre. Együttműködve [Five9 plusz (PBX, ügyfél-Center-ügynökök) támogatás adaptercsoporton](https://www.five9.com/about/contact) a felhasználók hozzáadása a Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) platform. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használandó Azure egyszeri bejelentkezést a hozzáférés biztosításával Five9 plusz adapterhez (PBX, ügyfél-Center-ügynökök).

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök), hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök)**.

    ![Egyszeri bejelentkezés konfigurálása](./media/five9-tutorial/tutorial_five9_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) csempére kattint, meg kell lekérése automatikusan bejelentkezett az Five9 plusz Adapter (PBX, ügyfél-Center-ügynökök) alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/five9-tutorial/tutorial_general_01.png
[2]: ./media/five9-tutorial/tutorial_general_02.png
[3]: ./media/five9-tutorial/tutorial_general_03.png
[4]: ./media/five9-tutorial/tutorial_general_04.png

[100]: ./media/five9-tutorial/tutorial_general_100.png

[200]: ./media/five9-tutorial/tutorial_general_200.png
[201]: ./media/five9-tutorial/tutorial_general_201.png
[202]: ./media/five9-tutorial/tutorial_general_202.png
[203]: ./media/five9-tutorial/tutorial_general_203.png

