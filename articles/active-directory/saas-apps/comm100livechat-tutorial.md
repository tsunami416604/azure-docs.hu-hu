---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az Comm100 élő csevegés |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Comm100 élő csevegés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0340d7f3-ab54-49ef-b77c-62a0efd5d49c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 203c082275dc75a7dcf948eb42a383300955f355
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57858099"
---
# <a name="tutorial-azure-active-directory-integration-with-comm100-live-chat"></a>Oktatóanyag: Az Azure Active Directory-integráció az Comm100 élő csevegés

Ebben az oktatóanyagban elsajátíthatja, hogyan Comm100 élő csevegés integrálása az Azure Active Directoryval (Azure AD).
Az Azure AD integrálása a Comm100 élő csevegés nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Comm100 élő csevegés az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett Comm100 élő csevegés (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Comm100 élő csevegés az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Comm100 élő csevegés egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja a Comm100 élő csevegés **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-comm100-live-chat-from-the-gallery"></a>Hozzáadás a katalógusból, Comm100 élő csevegés

Az Azure AD integrálása a Comm100 élő csevegés konfigurálásához hozzá kell Comm100 élő csevegés a galériából a felügyelt SaaS-alkalmazások listájára.

**Élő csevegés a Comm100 hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Comm100 élő csevegés**válassza **Comm100 élő csevegés** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Comm100 az eredménylistában élő csevegés](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Comm100 élő csevegés nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működéséhez egy Azure AD-felhasználót és a kapcsolódó felhasználó Comm100 élő csevegés hivatkozás kapcsolatának kell létrehozni.

Az Azure AD egyszeri bejelentkezés az Comm100 élő csevegés tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Comm100 élő csevegés egyszeri bejelentkezés konfigurálása](#configure-comm100-live-chat-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre tesztfelhasználót Comm100 élő csevegés](#create-comm100-live-chat-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon Comm100 élő csevegés, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása Comm100 élő csevegés, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Comm100 élő csevegés** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Comm100 élő csevegés tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-signonurl.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<SUBDOMAIN>.comm100.com/AdminManage/LoginSSO.aspx?siteId=<SITEID>`

    > [!NOTE] 
    > A bejelentkezési URL-érték nem valódi. A tényleges bejelentkezési URL-címet, az oktatóanyag későbbi részében ismertetett frissíteni a bejelentkezési URL-érték.

5. Comm100 élő csevegőalkalmazás létrehozása a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

6. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen, a jogcímek szerkesztése használatával **Szerkesztés ikon** , vagy adja hozzá a jogcímek használatával **hozzáadása új jogcímet**SAML-jogkivonat attribútum beállítása, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket: 

    | Name (Név) |  Adatforrás-attribútum|
    | ---------------| --------------- |
    |   e-mail    | user.mail |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **Comm100 élő csevegés beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-comm100-live-chat-single-sign-on"></a>Élő csevegés Comm100 egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban, jelentkezzen be Comm100 élő csevegés egy biztonsági-rendszergazdaként.

1. Kattintson a lap felső jobb oldalán, **My Account**.

   ![Myaccount Comm100 élő csevegés](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

1. A menüt a bal oldali menüjében kattintson az **biztonsági** majd **ügynök egyszeri bejelentkezés**.

   ![Biztonsági Comm100 élő csevegés](./media/comm100livechat-tutorial/tutorial_comm100livechat_security.png)

1. Az a **ügynök egyszeri bejelentkezés** lapon, a következő lépésekkel:

   ![Biztonsági Comm100 élő csevegés](./media/comm100livechat-tutorial/tutorial_comm100livechat_singlesignon.png)

   a. Az első kiemelt hivatkozásra másolja és illessze be azt a **bejelentkezési URL-** szövegmezőjébe **Comm100 élő csevegés tartomány és URL-címek** szakaszban az Azure Portalon.

   b. Az a **SAML egyszeri bejelentkezési URL-cím** szövegmező, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.

   c. Az a **távoli kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket a **kijelentkezési URL-címe**, az Azure Portalról másolt.

   d. Kattintson a **válasszon ki egy fájlt** feltölteni a base-64 kódolású tanúsítványt, amelyet már letöltötte az Azure Portalról, a **tanúsítvány**.

   e. Kattintson a **módosítások mentése**

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be **brittasimon\@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával Comm100 élő csevegés Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Comm100 élő csevegés**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Comm100 élő csevegés**.

    ![Az alkalmazások listáját a Comm100 élő csevegés hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-comm100-live-chat-test-user"></a>Hozzon létre tesztfelhasználót Comm100 élő csevegés

Ahhoz, hogy az Azure AD-felhasználók Comm100 élő csevegés jelentkezzen be, akkor ki kell építeni Comm100 élő csevegés be. Élő csevegés Comm100 kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Élő csevegés Comm100 egy biztonsági-rendszergazdaként jelentkezzen be.

2. Kattintson a lap felső jobb oldalán, **My Account**.

    ![Myaccount Comm100 élő csevegés](./media/comm100livechat-tutorial/tutorial_comm100livechat_account.png)

3. A menüt a bal oldali menüjében kattintson az **ügynökök** majd **új ügynök**.

    ![Az ügynök Comm100 élő csevegés](./media/comm100livechat-tutorial/tutorial_comm100livechat_agent.png)

4. Az a **új ügynök** lapon, a következő lépésekkel:

    ![Új ügynök Comm100 élő csevegés](./media/comm100livechat-tutorial/tutorial_comm100livechat_newagent.png)

    a. a. A **E-mail** szöveget adja meg az e-mailt, például a felhasználó **Brittasimon\@contoso.com**.

    b. A **Utónév** szöveget adja meg például a felhasználó utónevét **Britta**.

    c. A **Vezetéknév** szöveget adja meg például a felhasználó vezetékneve **simon**.

    d. Az a **megjelenítendő név** szövegmezőbe írja be például a felhasználó megjelenített neve **Britta simon**

    e. Az a **jelszó** szövegmezőbe írja be a jelszavát.

    f. Kattintson a **Save** (Mentés) gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen Comm100 élő csevegés csempére kattint, akkor kell automatikusan megtörténik a a Comm100 élő csevegés, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

