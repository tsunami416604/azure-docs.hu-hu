---
title: 'Oktatóanyag: Azure Active Directory-integráció az helyettes |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a helyettes között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 5665c3ac-5689-4201-80fe-fcc677d4430d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: d4cf47dba0501694c5ed000c087f16d36b0dcdde
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39427146"
---
# <a name="tutorial-azure-active-directory-integration-with-deputy"></a>Oktatóanyag: Azure Active Directory-integráció az helyettes

Ebben az oktatóanyagban elsajátíthatja, hogyan helyettes integrálása az Azure Active Directory (Azure AD).

Az Azure AD integrálása a helyettes nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a helyettes Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett, helyettes (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a helyettes, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A helyettes egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Helyettes hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-deputy-from-the-gallery"></a>Helyettes hozzáadása a katalógusból
Az Azure AD integrálása a helyettes konfigurálásához hozzá kell helyettes a galériából a felügyelt SaaS-alkalmazások listájára.

**Helyettes hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **helyettes**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/deputy-tutorial/tutorial_deputy_search.png)

1. Az eredmények panelen válassza ki a **helyettes**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/deputy-tutorial/tutorial_deputy_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a helyettes egy "Britta Simon." nevű tesztelési felhasználó alapján

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó helyettes mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a helyettes hivatkozás kapcsolata kell létrehozni.

Helyettes, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a helyettes tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Helyettes tesztfelhasználó létrehozása](#creating-a-deputy-test-user)**  – egy megfelelője a Britta Simon helyettes, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és helyettes alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés helyettes, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **helyettes** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/deputy-tutorial/tutorial_deputy_samlbase.png)

1. Az a **helyettes tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/deputy-tutorial/tutorial_deputy_url1.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe:
    |  |
    | ----|
    | `https://<subdomain>.<region>.au.deputy.com` |
    | `https://<subdomain>.<region>.ent-au.deputy.com` |
    | `https://<subdomain>.<region>.na.deputy.com`|
    | `https://<subdomain>.<region>.ent-na.deputy.com`|
    | `https://<subdomain>.<region>.eu.deputy.com` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com` |
    | `https://<subdomain>.<region>.as.deputy.com` |
    | `https://<subdomain>.<region>.ent-as.deputy.com` |
    | `https://<subdomain>.<region>.la.deputy.com` |
    | `https://<subdomain>.<region>.ent-la.deputy.com` |
    | `https://<subdomain>.<region>.af.deputy.com` |
    | `https://<subdomain>.<region>.ent-af.deputy.com` |
    | `https://<subdomain>.<region>.an.deputy.com` |
    | `https://<subdomain>.<region>.ent-an.deputy.com` |
    | `https://<subdomain>.<region>.deputy.com` |

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe:
    | |
    |----|
    | `https://<subdomain>.<region>.au.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-au.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.na.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-na.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.eu.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-eu.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.as.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-as.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.la.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-la.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.af.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-af.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.an.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.ent-an.deputy.com/exec/devapp/samlacs.` |
    | `https://<subdomain>.<region>.deputy.com/exec/devapp/samlacs.` |

1. Ellenőrizze **speciális URL-beállítások megjelenítése**. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/deputy-tutorial/tutorial_deputy_url2.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<your-subdomain>.<region>.deputy.com`
    
    >[!NOTE]
    > Helyettes régió utótag nem kötelező, vagy ezek egyikét kell használnia: au |} nA |} az EU |}, |} la |} af |} egy |} ent – Ausztrália |} ent-na |} ent EU-n |} ent-, |} ügyfélta-la |} ügyfélta-af |} ügyfélta egy

    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [helyettes támogatási csapatának](https://www.deputy.com/call-centers-customer-support-scheduling-software) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/deputy-tutorial/tutorial_deputy_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/deputy-tutorial/tutorial_general_400.png)
    
1. Az a **helyettes konfigurációs** területén kattintson **konfigurálása helyettes** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/deputy-tutorial/tutorial_deputy_configure.png) 

1. Keresse meg a következő URL-cím:[https://(your-subdomain).deputy.com/exec/config/system_config]( https://(your-subdomain).deputy.com/exec/config/system_config). Lépjen a **biztonsági beállítások** kattintson **szerkesztése**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/deputy-tutorial/tutorial_deputy_004.png)

1. Ez a **biztonsági beállítások** lapon, hajtsa végre az alábbi lépéseket.

    ![Egyszeri bejelentkezés konfigurálása](./media/deputy-tutorial/tutorial_deputy_005.png)
    
    a. Engedélyezése **közösségi bejelentkezés**.
   
    b. Nyissa meg a Jegyzettömbben az Azure portálról letöltött Base64-kódolású tanúsítvány, a tartalmát a vágólapra másolja és illessze be azt a **tanúsítvány OpenSSL** szövegmezőbe.
   
    c. A SAML egyszeri bejelentkezési URL-címe szövegmezőbe írja be `https://<your subdomain>.deputy.com/exec/devapp/samlacs?dpLoginTo=<saml sso url>`
    
    d. Cserélje le a SAML egyszeri bejelentkezési URL-címe szövegmezőbe `<your subdomain>` , a altartománnyal rendelkező.
   
    e. Cserélje le a SAML egyszeri bejelentkezési URL-címe szövegmezőbe `<saml sso url>` együtt a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** másolta az Azure Portalról.
   
    f. Kattintson a **beállítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/deputy-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/deputy-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/deputy-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/deputy-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-deputy-test-user"></a>Helyettes tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók helyettes jelentkezzen be, akkor ki kell építeni, helyettes. Helyettes, esetén kiépítése a manuális feladat.

#### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:
1. Jelentkezzen be rendszergazdaként a helyettes vállalati webhely.

1. A felső navigációs panelen kattintson **személyek**.
   
   ![Személyek](./media/deputy-tutorial/tutorial_deputy_001.png "személyek")

1. Kattintson a **személyek hozzáadása** gombra, és kattintson a **adjon hozzá egy személy**.
   
   ![Személyek hozzáadása](./media/deputy-tutorial/tutorial_deputy_002.png "személyek hozzáadása")

1. A következő lépésekkel, és kattintson a **mentse, és meghívhat**.
   
   ![Új felhasználó](./media/deputy-tutorial/tutorial_deputy_003.png "új felhasználó")

   a. Az a **neve** szövegmezőbe írja be felhasználónevét, például **BrittaSimon**.
   
   b. Az a **E-mail** szövegmezőbe írja be kíván üzembe helyezni egy Azure AD-fiók e-mail címét.
   
   c. Az a **valamikor** szövegmezőbe írja be a vállalati nevét.
   
   d. Kattintson a **mentse, és meghívhat** gombra.

1. Az AAD-fiók tulajdonosa kap egy e-mailt, és egy hivatkozást, mielőtt aktívvá válik, győződjön meg arról, hogy fiókjuk követi. Bármely más helyettes felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését helyettes által biztosított API-k.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés helyettes Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel helyettes, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **helyettes**.

    ![Egyszeri bejelentkezés konfigurálása](./media/deputy-tutorial/tutorial_deputy_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a a hozzáférési Panel használatával az Azure AD egyszeri bejelentkezési konfiguráció tesztelése.

Ha a hozzáférési panelen a helyettes csempére kattint, meg kell lekérése automatikusan bejelentkezett a helyettes alkalmazásba.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/deputy-tutorial/tutorial_general_01.png
[2]: ./media/deputy-tutorial/tutorial_general_02.png
[3]: ./media/deputy-tutorial/tutorial_general_03.png
[4]: ./media/deputy-tutorial/tutorial_general_04.png

[100]: ./media/deputy-tutorial/tutorial_general_100.png

[200]: ./media/deputy-tutorial/tutorial_general_200.png
[201]: ./media/deputy-tutorial/tutorial_general_201.png
[202]: ./media/deputy-tutorial/tutorial_general_202.png
[203]: ./media/deputy-tutorial/tutorial_general_203.png

