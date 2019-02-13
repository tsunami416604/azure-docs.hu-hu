---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ServiceChannel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és ServiceChannel között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: c3546eab-96b5-489b-a309-b895eb428053
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/3/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7d8f42b49fe3dbdecec163ab47579346b71f0bf
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56190500"
---
# <a name="tutorial-azure-active-directory-integration-with-servicechannel"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező ServiceChannel

Ebben az oktatóanyagban elsajátíthatja, hogyan ServiceChannel integrálása az Azure Active Directory (Azure AD).

ServiceChannel integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá ServiceChannel Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett ServiceChannel (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- A fiókok egyetlen központi helyen – az Azure felügyeleti portálján kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

ServiceChannel az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy ServiceChannel egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Az éles környezetben ne használjon, ha erre szükség.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. ServiceChannel hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-servicechannel-from-the-gallery"></a>ServiceChannel hozzáadása a katalógusból
Adja meg Zahájena integrációja az Azure AD-be, szüksége ServiceChannel hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**ServiceChannel hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálján](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **ServiceChannel**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/servicechannel-tutorial/tutorial-servicechannel_000.png)

1. Az eredmények panelen válassza ki a **ServiceChannel**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/servicechannel-tutorial/tutorial-servicechannel_2.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés a teszt "Britta Simon" nevű felhasználó ServiceChannel.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó ServiceChannel mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó ServiceChannel hivatkozás kapcsolata kell létrehozni.

A hivatkozás kapcsolat létesítéséhez értéket rendeli az **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a ServiceChannel.

Az Azure AD egyszeri bejelentkezés az ServiceChannel tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[ServiceChannel tesztfelhasználó létrehozása](#creating-a-servicechannel-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure felügyeleti portálon, és ServiceChannel alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés ServiceChannel, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon a a **ServiceChannel** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédpanelen, **mód** kiválasztása **SAML-alapú bejelentkezés** való egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/servicechannel-tutorial/tutorial-servicechannel_01.png)

1. Az a **ServiceChannel tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/servicechannel-tutorial/tutorial-servicechannel_urls.png)

    a. Az a **azonosító** szövegmezőbe írja be az értéket, mint: `http://adfs.<domain>.com/adfs/service/trust`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<customer domain>.servicechannel.com/saml/acs`

    > [!NOTE] 
    > Vegye figyelembe, hogy ezek nem állnak a valós értékeket. Az értékeket módosítsa a tényleges azonosítója és a válasz URL-címet kell. Itt javasoljuk, hogy az azonosító egyedi karakterlánc értékét használhatja. Kapcsolattartó [ServiceChannel támogatási csapatának](https://servicechannel.zendesk.com/hc/en-us) beolvasni ezeket az értékeket.

1. ServiceChannel alkalmazását a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőfelvételen látható erre egy példa látható. **(Felhasználóazonosító) NameIdentifier** a csak a következő kötelező jogcímet, és az alapértelmezett érték **user.userprincipalname** ServiceChannel ezt a képezhető le a vár, de **user.mail**. Ha azt tervezi, igény szerinti felhasználók létrehozásának engedélyezéséhez, akkor hozzá kell adnia a következő jogcímek alább látható módon. **Szerepkör** jogcím van szüksége, le kell képezni **user.assignedroles** , amely tartalmazza a felhasználó szerepkörét.  

    Tekintse meg Zahájena útmutató [Itt](https://servicechannel.zendesk.com/hc/en-us/articles/217514326-Azure-AD-Configuration-Example) jogcímek további útmutatást.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/servicechannel-tutorial/tutorial_servicechannel_attribute.png)

    > [!NOTE] 
    > Lásd: [rbac-RÓL és az Azure portal-hozzáférés kezelése](../../role-based-access-control/role-assignments-portal.md) megtudhatja, hogyan konfigurálhatja **szerepkör** az Azure ad-ben.

1. A **felhasználói attribútumok** területén kattintson **megtekintése és egyéb felhasználói attribútumok szerkesztése** és attribútumainak beállítása.

    | Attribútum neve | Attribútum értéke |
    | --- | --- |    
    | Szerepkör| user.assignedroles |

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicechannel-tutorial/tutorial_servicechannel_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/servicechannel-tutorial/tutorial_servicechannel_05.png)
    
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson a **Ok**
    
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicechannel-tutorial/tutorial-servicechannel_05.png) 

1. Kattintson a **Save** (Mentés) gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicechannel-tutorial/tutorial_general_400.png)

1. Az a **ServiceChannel konfigurációs** területén kattintson **konfigurálása ServiceChannel** megnyitásához **bejelentkezés konfigurálása** ablak. Vegye figyelembe, hogy a **SAML Enitity azonosító** származó a **rövid összefoglaló** szakaszban.

1. Az egyszeri bejelentkezés konfigurálása **ServiceChannel** oldalon kell küldenie a letöltött **tanúsítvány (Base64)** és **SAML Entitásazonosító** való [ServiceChannel támogatási csoport](https://servicechannel.zendesk.com/hc/en-us). Ezek beállítja ezt annak érdekében, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure felügyeleti portálján, Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/servicechannel-tutorial/create_aaduser_01.png) 

1. Lépjen a **felhasználók és csoportok** kattintson **minden felhasználó** felhasználók listájának megjelenítéséhez.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/servicechannel-tutorial/create_aaduser_02.png) 

1. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/servicechannel-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/servicechannel-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra. 

### <a name="creating-a-servicechannel-test-user"></a>ServiceChannel tesztfelhasználó létrehozása

Alkalmazás támogatja a csak az idő felhasználókiépítés, miután a felhasználók hitelesítésére, az alkalmazás automatikusan létrejön. Teljes jogú felhasználók kiépítése, lépjen kapcsolatba [ServiceChannel támogatási csoport](https://servicechannel.zendesk.com/hc/en-us)

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés ServiceChannel saját hozzáférésének engedélyezésére használja.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel ServiceChannel, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **ServiceChannel**.

    ![Egyszeri bejelentkezés konfigurálása](./media/servicechannel-tutorial/tutorial-servicechannel_app01.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a ServiceChannel csempére kattint, meg kell beolvasása automatikusan bejelentkezett az ServiceChannel alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/servicechannel-tutorial/tutorial_general_01.png
[2]: ./media/servicechannel-tutorial/tutorial_general_02.png
[3]: ./media/servicechannel-tutorial/tutorial_general_03.png
[4]: ./media/servicechannel-tutorial/tutorial_general_04.png

[100]: ./media/servicechannel-tutorial/tutorial_general_100.png

[200]: ./media/servicechannel-tutorial/tutorial_general_200.png
[201]: ./media/servicechannel-tutorial/tutorial_general_201.png
[202]: ./media/servicechannel-tutorial/tutorial_general_202.png
[203]: ./media/servicechannel-tutorial/tutorial_general_203.png
