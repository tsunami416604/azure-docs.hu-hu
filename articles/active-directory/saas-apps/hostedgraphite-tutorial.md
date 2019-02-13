---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az üzemeltetett grafit |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés grafit üzemeltetett és Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: a1ac4d7f-d079-4f3c-b6da-0f520d427ceb
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ce0ae55fdc43704670f1d74e52b55db91a20cceb
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56179246"
---
# <a name="tutorial-azure-active-directory-integration-with-hosted-graphite"></a>Oktatóanyag: Az Azure Active Directory integrációja az üzemeltetett grafit

Ebben az oktatóanyagban elsajátíthatja, hogyan üzemeltetett grafit integrálása az Azure Active Directory (Azure AD).

Üzemeltetett grafit integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá az üzemeltetett grafit Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az üzemeltetett grafit (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az üzemeltetett grafit, a következőkre van szükség:

- Azure AD-előfizetés
- Egy üzemeltetett grafit egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Üzemeltetett grafit hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-hosted-graphite-from-the-gallery"></a>Üzemeltetett grafit hozzáadása a katalógusból
Azure ad-ben üzemeltetett grafit integráció konfigurálásához, hozzá kell üzemeltetett grafit a galériából a felügyelt SaaS-alkalmazások listájára.

**Üzemeltetett grafit hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **üzemeltetett grafit**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_search.png)

1. Az eredmények panelen válassza ki a **üzemeltetett grafit**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés üzemeltetett grafit a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó lévő üzemeltetett grafit mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó lévő üzemeltetett grafit hivatkozás kapcsolata kell létrehozni.

Üzemeltetett grafit rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az üzemeltetett grafit tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Üzemeltetett grafit tesztfelhasználó létrehozása](#creating-a-hosted-graphite-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon lévő üzemeltetett grafit, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az grafit üzemeltetett alkalmazás egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés grafit üzemeltetett, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **üzemeltetett grafit** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_samlbase.png)

1. Az a **grafit tartományok vagy URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató által kezdeményezett mód**, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_url.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.hostedgraphite.com/metadata/<user id>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.hostedgraphite.com/complete/saml/<user id>`

1. Az a **grafit tartományok vagy URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **SP által kezdeményezett mód**, hajtsa végre az alábbi lépéseket:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_10.png)
  
    a. Kattintson a **speciális URL-beállítások megjelenítése** lehetőség

    b. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://www.hostedgraphite.com/login/saml/<user id>/`   

    > [!NOTE] 
    > Vegye figyelembe, hogy ezek nem állnak a valós értékeket. A tényleges azonosítója, válasz URL-cím és bejelentkezési URL-ezeket az értékeket frissítenie kell. Ezek az értékek lekéréséhez hozzáférés megnyithatja az alkalmazás oldalán, vagy forduljon a SAML beállítása -> [üzemeltetett grafit támogatási csapatával](mailto:help@hostedgraphite.com).
    >
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/hostedgraphite-tutorial/tutorial_general_400.png)

1. Az a **üzemeltetett grafit konfigurációs** területén kattintson **üzemeltetett grafit konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_configure.png) 

1. Bejelentkezés az üzemeltetett grafit bérlői rendszergazdaként.

1. Nyissa meg a **SAML beállítása lapon** az oldalsávon (**hozzáférés SAML beállítása ->**).
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_000.png)

1. Erősítse meg az alábbi URL-címek a kész a konfigurációnak megfelelően a **grafit tartományok vagy URL-címek** szakaszában az Azure Portalon.
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_001.png)

1. A **entitást vagy a kiállító azonosító** és **Egyszeri bejelentkezési URL-cím** szövegmezőből, illessze be az értékét **SAML Entitásazonosító** és **SAML egyszeri bejelentkezési szolgáltatás URL-cím** amely az Azure Portalról másolta. 
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_002.png)
   

1. Jelölje be "**csak olvasható**" as **felhasználói szerepkör alapértelmezett**.
    
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_004.png)

1. A base-64 kódolású tanúsítvány megnyitása a Jegyzettömbben az Azure-portálról letöltött, a tartalmát a vágólapra másolja és illessze be azt a **X.509-tanúsítvány** szövegmezőbe.
    
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_005.png)

1. Kattintson a **mentése** gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hostedgraphite-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hostedgraphite-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hostedgraphite-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/hostedgraphite-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-hosted-graphite-test-user"></a>Üzemeltetett grafit tesztfelhasználó létrehozása

Ez a szakasz célja egy lévő üzemeltetett grafit Britta Simon nevű felhasználó létrehozásához. Üzemeltetett grafit támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van.

Nincs meg ebben a szakaszban a művelet elem. Egy új felhasználót hoz létre az üzemeltetett grafit elérésére, ha még nem létezik tett kísérlet során.

>[!NOTE]
>Ha szeretné manuálisan hozzon létre egy felhasználót, szeretné-e a üzemeltetett grafit támogatási csapat <mailto:help@hostedgraphite.com>. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által üzemeltetett grafit való hozzáférés biztosítása az Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel grafit üzemeltetett, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **üzemeltetett grafit**.

    ![Egyszeri bejelentkezés konfigurálása](./media/hostedgraphite-tutorial/tutorial_hostedgraphite_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a a hozzáférési Panel használatával az Azure AD egyszeri bejelentkezési konfiguráció tesztelése.

Ha a hozzáférési panelen a üzemeltetett grafit csempére kattint, akkor kell lekérése automatikusan bejelentkezett az grafit üzemeltetett alkalmazás.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/hostedgraphite-tutorial/tutorial_general_01.png
[2]: ./media/hostedgraphite-tutorial/tutorial_general_02.png
[3]: ./media/hostedgraphite-tutorial/tutorial_general_03.png
[4]: ./media/hostedgraphite-tutorial/tutorial_general_04.png

[100]: ./media/hostedgraphite-tutorial/tutorial_general_100.png

[200]: ./media/hostedgraphite-tutorial/tutorial_general_200.png
[201]: ./media/hostedgraphite-tutorial/tutorial_general_201.png
[202]: ./media/hostedgraphite-tutorial/tutorial_general_202.png
[203]: ./media/hostedgraphite-tutorial/tutorial_general_203.png

