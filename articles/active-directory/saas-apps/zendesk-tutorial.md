---
title: 'Oktatóanyag: Azure Active Directory-integráció az Zendesk |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Zendesk és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 37d20eabfc8fb883cda346dc8b55a17b8b959218
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48268174"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Oktatóanyag: Zendesk-Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Zendesk integrálása az Azure Active Directory (Azure AD).

Zendesk integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a Zendeskhez Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Zendeskhez (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Zendesk, a következőkre van szükség:

- Azure AD-előfizetés
- Egy Zendesk egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.
Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Zendesk hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-zendesk-from-the-gallery"></a>Zendesk hozzáadása a katalógusból

Adja meg Zendesk integrációja az Azure AD-be, szüksége Zendesk hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Adja hozzá a Zendeskhez a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![image](./media/zendesk-tutorial/selectazuread.png)

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![image](./media/zendesk-tutorial/a_select_app.png)
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![image](./media/zendesk-tutorial/a_new_app.png)

4. A Keresés mezőbe írja be a **Zendesk**válassza **Zendesk** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![image](./media/zendesk-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Zendesk, a teszt "Britta Simon" nevű felhasználó.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó a Zendeskben mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Zendeskben hivatkozás kapcsolatát kell létrehozni.

Zendesk, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés Zendesk-tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Zendesk-tesztfelhasználót](#create-a-zendesk-test-user)**  – a felhasználó Azure ad-ben reprezentációja kapcsolódó Zendeskben egy megfelelője a Britta Simon rendelkeznie.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Zendesk-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Zendesk, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com/), az a **Zendesk** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![image](./media/zendesk-tutorial/b1_b2_select_sso.png)

2. Kattintson a **módosítása egyszeri bejelentkezési mód** felett válassza ki a képernyő a **SAML** mód.

      ![image](./media/zendesk-tutorial/b1_b2_saml_ssso.png)

3. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![image](./media/zendesk-tutorial/b1_b2_saml_sso.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![image](./media/zendesk-tutorial/b1-domains_and_urlsedit.png)

5. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címet a következő mintával: `https://<subdomain>.zendesk.com`.

    b. Az a **azonosító** szövegmezőbe írja be egy URL-címet a következő mintával: `<subdomain>.zendesk.com`.

    ![image](./media/zendesk-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [Zendesk-ügyfél-támogatási csapatának](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) beolvasni ezeket az értékeket.

6. Zendesk a SAML helyességi feltételek vár egy megadott formátumban. SAML attribútum sem kötelező, de igény szerint hozzáadhat egy attribútumot **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](./media/zendesk-tutorial/i4-attribute.png)

7. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](./media/zendesk-tutorial/i2-attribute.png)

    ![image](./media/zendesk-tutorial/i3-attribute.png)
    
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.
    
    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > A bővítményattribútumok használatával ad hozzá az attribútumokat, amelyek nem szerepelnek az Azure AD alapértelmezés szerint. Kattintson a [felhasználói attribútumok SAML-beállítható](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) teljes listájának beolvasásához SAML attribútumok, amelyek **Zendesk** fogad el.

8. A SAML-aláíró tanúsítvány szakaszában, a a **SAML-aláíró tanúsítvány** területén másolja a **ujjlenyomat**, majd menti azt a számítógépet.

    ![image](./media/zendesk-tutorial/C3_certificate.png)

    a. Válassza ki a megfelelő beállítást **aláírási beállítás** szükség esetén.

    b. Válassza ki a megfelelő beállítást **aláírási algoritmus** szükség esetén.

    c. Kattintson a **Mentés** gombra.

9. Az a **Zendesk beállítása** területén kattintson **részletes útmutató megtekintéséhez** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás az alábbi URL-címek, a a **gyors útmutató szakaszban.**

    Vegye figyelembe, hogy az URL-cím lehet, hogy tegyük fel, hogy a következő:

    a. SAML egyszeri bejelentkezési szolgáltatás URL-címe

    b. Entitásazonosító

    c. Kijelentkezési URL-cím

    ![image](./media/zendesk-tutorial/d1_saml.png) 

10. Két módszer van, amelyben konfigurálható a Zendesk - automatikus és kézi.
  
11. Automatizálhatja a Zendesk konfigurációra, telepítenie kell **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** kattintva **a bővítmény telepítése**.

    ![image](./media/zendesk-tutorial/install_extension.png)

12. A felvett bővítmény a böngészőre, kattintson a **telepítő Zendesk** fogja irányítani, a Zendesk-alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatok a Zendesk bejelentkezik. A webböngésző-bővítmény automatikusan az Ön számára az alkalmazás konfigurálása és automatizálhatja a 13. lépés.

     ![image](./media/zendesk-tutorial/d2_saml.png) 

13. Ha szeretné manuálisan beállítani a Zendesk, nyissa meg egy új böngészőablakban, és jelentkezzen be rendszergazdaként, a Zendesk vállalati hely, és hajtsa végre az alábbi lépéseket:

    * Kattintson a **rendszergazdai**.

    * Kattintson a bal oldali navigációs ablaktáblában **beállítások**, és kattintson a **biztonsági**.

    * Az a **biztonsági** lapon, a következő lépésekkel:

      ![Biztonsági](././media/zendesk-tutorial/ic773089.png "biztonsági")

      ![Egyszeri bejelentkezés](././media/zendesk-tutorial/ic773090.png "egyszeri bejelentkezés")

      a. Kattintson a **rendszergazda & ügynökök** fülre.

      b. Válassza ki **egyszeri bejelentkezés (SSO) és az SAML**, majd válassza ki **SAML**.

      c. A **SAML egyszeri bejelentkezési URL-cím** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.

      d. A **távoli kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket a **kijelentkezéses URL-cím** Azure Portalról másolt.

      e. A **tanúsítvány-ujjlenyomat** szövegmezőjébe illessze be a **ujjlenyomat** tanúsítvány, amely az Azure Portalról másolt érték.

      f. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![image](./media/zendesk-tutorial/d_users_and_groups.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![image](./media/zendesk-tutorial/d_adduser.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![image](./media/zendesk-tutorial/d_userproperties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

### <a name="create-a-zendesk-test-user"></a>Zendesk tesztfelhasználó létrehozása

Ez a szakasz célja a Zendeskben Britta Simon nevű felhasználó létrehozásához. Zendesk támogatja a felhasználók automatikus átadása, amely alapértelmezés szerint van engedélyezve. További részleteket talál [Itt](Zendesk-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

**Ha szeretné manuálisan létrehozni a felhasználói, hajtson végre a következő lépéseket:**

> [!NOTE]
> **A végfelhasználói** automatikusan fiókokat bejelentkezéskor. **Az ügynök** és **rendszergazdai** fiókokat kell manuálisan ki kell építeni **Zendesk** bejelentkezés előtt.

1. Jelentkezzen be a **Zendesk** bérlő.

2. Válassza ki a **Ügyféllistára** fülre.

3. Válassza ki a **felhasználói** fülre, majd **Hozzáadás**.

    ![Felhasználó hozzáadása](././media/zendesk-tutorial/ic773632.png "felhasználó hozzáadása")
4. Írja be a **neve** és **E-mail** egy meglévő Azure AD-fiókot kíván üzembe helyezni, és kattintson a **mentése**.

    ![Új felhasználó](././media/zendesk-tutorial/ic773633.png "új felhasználó")

> [!NOTE]
> Bármely más Zendesk felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését Zendesk által biztosított API-k.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával a Zendeskhez Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![image](./media/zendesk-tutorial/d_all_applications.png)

2. Az alkalmazások listájában jelölje ki a **Zendesk**.

    ![image](./media/zendesk-tutorial/d_all_zendeskapplications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![image](./media/zendesk-tutorial/d_leftpaneusers.png)

4. Válassza ki a **Hozzáadás** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![image](./media/zendesk-tutorial/d_assign_user.png)

4. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

5. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Zendesk-csempére kattint, meg kell lekérése automatikusan bejelentkezett a Zendesk-alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Felhasználók átadásának konfigurálása](zendesk-provisioning-tutorial.md)
