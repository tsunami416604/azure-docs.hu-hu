---
title: 'Oktatóanyag: Azure Active Directory-integráció ON24 virtuális környezet SAML-kapcsolattal rendelkező |} A Microsoft Docs'
description: Ismerje meg, hogyan konfigurálása egyszeri bejelentkezéshez ON24 virtuális környezet SAML kapcsolat és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d4028fb5-b2ad-4c5d-b123-7b675c509d64
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/08/2018
ms.author: jeedes
ms.openlocfilehash: 1ec18f0013a7fa640395a8b8bedd9df8b0924c3a
ms.sourcegitcommit: 7b0778a1488e8fd70ee57e55bde783a69521c912
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49071243"
---
# <a name="tutorial-azure-active-directory-integration-with-on24-virtual-environment-saml-connection"></a>Oktatóanyag: Azure Active Directory-integráció ON24 virtuális környezet SAML-kapcsolattal

Ebben az oktatóanyagban elsajátíthatja, hogyan ON24 virtuális környezet SAML kapcsolat integrálható az Azure Active Directory (Azure AD).

ON24 virtuális környezet SAML csatlakozási integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a virtuális környezet SAML csatlakozási ON24 Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett ON24 virtuális környezet SAML kapcsolatra (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

ON24 virtuális környezet SAML-kapcsolattal az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- A virtuális környezet SAML csatlakozási ON24 egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. ON24 virtuális környezet SAML kapcsolat hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-on24-virtual-environment-saml-connection-from-the-gallery"></a>ON24 virtuális környezet SAML kapcsolat hozzáadása a katalógusból
Az Azure AD integrálása a ON24 virtuális környezet SAML-kapcsolat konfigurálásához szüksége ON24 virtuális környezet SAML kapcsolat hozzáadása a katalógusból a felügyelt SaaS-alkalmazások listájában.

**Virtuális környezet SAML ON24 kapcsolat hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![image](./media/on24-tutorial/selectazuread.png)

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![image](./media/on24-tutorial/a_select_app.png)
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![image](./media/on24-tutorial/a_new_app.png)

4. A Keresés mezőbe írja be a **ON24 virtuális környezet SAML csatlakozási**, jelölje be **ON24 virtuális környezet SAML csatlakozási** eredmény panelen kattintson a **Hozzáadás** gombra kattintva adhat hozzá az alkalmazás.

     ![image](./media/on24-tutorial/tutorial_on24_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés ON24 virtuális környezet SAML kapcsolatot a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó ON24 virtuális környezet SAML kapcsolat mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó ON24 virtuális környezet SAML kapcsolat hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés ON24 virtuális környezet SAML-kapcsolat tesztelése és konfigurálása, meg kell hajtsa végre a következő építőelemeit:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy virtuális környezet SAML csatlakozási ON24 tesztfelhasználót](#create-an-on24-virtual-environment-saml-connection-test-user)**  – egy megfelelője a Britta Simon ON24 virtuális környezet SAML kapcsolat, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és ON24 virtuális környezet SAML kapcsolat alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása ON24 virtuális környezet SAML-kapcsolattal, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com/), a a **ON24 virtuális környezet SAML csatlakozási** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![image](./media/on24-tutorial/B1_B2_Select_SSO.png)

2. Kattintson a **egyszeri bejelentkezési mód megváltoztatása** felett válassza ki a képernyő a **SAML** mód.

      ![image](./media/on24-tutorial/b1_b2_saml_ssso.png)

3. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![image](./media/on24-tutorial/b1_b2_saml_sso.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![image](./media/on24-tutorial/b1-domains_and_urlsedit.png)

5. Az a **alapszintű SAML-konfigurációja** szakaszban a következő lépésekkel, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** intiated mód:

    ![image](./media/on24-tutorial/tutorial_on24_url.png)

    a. Az a **azonosító** szövegmezőbe írja be egy URL-címe:

     **Éles környezet URL-címe**
    
    `SAML-VSHOW.on24.com`

    `SAML-Gateway.on24.com` 

    `SAP PROD SAML-EliteAudience.on24.com` 
                
     **QA-környezet URL-címe**
    
    `SAMLQA-VSHOW.on24.com` 

    `SAMLQA-Gateway.on24.com` 

    `SAMLQA-EliteAudience.on24.com`
 
    b. Az a **válasz URL-cím** szövegmezőbe írja be egy URL-címe:
    
     **Éles környezet URL-címe**
    
    `https://federation.on24.com/sp/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1WU2hvdy5vbjI0LmNvbSJ9/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1HYXRld2F5Lm9uMjQuY29tIn0/ACS.saml2`

    `https://federation.on24.com/sp/eyJ2c2lkIjoiU0FNTC1FbGl0ZUF1ZGllbmNlLm9uMjQuY29tIn0/ACS.saml2`

     **QA-környezet URL-címe**
    
    `https://qafederation.on24.com/sp/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLVZzaG93Lm9uMjQuY29tIn0/ACS.saml2`

    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUdhdGV3YXkub24yNC5jb20ifQ/ACS.saml2`
     
    `https://qafederation.on24.com/sp/eyJ2c2lkIjoiU0FNTFFBLUVsaXRlQXVkaWVuY2Uub24yNC5jb20ifQ/ACS.saml2` 

    c. Kattintson a **további URL-címet beállítani**. 

    d. Az a **továbbítási állapot** szövegmezőbe írja be egy URL-címe: `https://vshow.on24.com/vshow/ms_azure_saml_test?r=<ID>`

    e. Ha az alkalmazás a konfigurálni kívánt **SP** intiated mód, a a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címe: `https://vshow.on24.com/vshow/<INSTANCENAME>`

6. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** megfelelően a megfelelő tanúsítvány letöltéséhez a követelmény, és mentse a számítógépre.

    ![image](./media/on24-tutorial/tutorial_on24_certificate.png) 

7. Az egyszeri bejelentkezés konfigurálása **ON24 virtuális környezet SAML csatlakozási** oldalon kell küldenie a tanúsítvány/metaadatokat, amelyek az Azure Portalról letöltött [ON24 virtuális környezet SAML-kapcsolat támogatási csoport](https://www.on24.com/about-us/support/). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![image](./media/on24-tutorial/d_users_and_groups.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![image](./media/on24-tutorial/d_adduser.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![image](./media/on24-tutorial/d_userproperties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-an-on24-virtual-environment-saml-connection-test-user"></a>Hozzon létre egy virtuális környezet SAML csatlakozási ON24 tesztfelhasználót

Ebben a szakaszban egy felhasználói Britta Simon nevű ON24 virtuális környezet SAML kapcsolatot hoz létre. Együttműködve [ON24 virtuális környezet SAML csatlakozási támogatási csapatának](https://www.on24.com/about-us/support/) a felhasználók hozzáadása a virtuális környezet SAML csatlakozási ON24 platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés ON24 virtuális környezet SAML-kapcsolat Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![image](./media/on24-tutorial/d_all_applications.png)

2. Az alkalmazások listájában jelölje ki a **ON24 virtuális környezet SAML csatlakozási**.

    ![image](./media/on24-tutorial/tutorial_on24_app.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![image](./media/on24-tutorial/d_leftpaneusers.png)

4. Válassza ki a **Hozzáadás** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![image](./media/on24-tutorial/d_assign_user.png)

4. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

5. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a ON24 virtuális környezet SAML csatlakozási csempére kattint, kell lekérése automatikusan bejelentkezett a virtuális környezet SAML csatlakozási ON24 alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

