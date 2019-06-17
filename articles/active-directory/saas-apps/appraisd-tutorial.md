---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Appraisd |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Appraisd között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: db063306-4d0d-43ca-aae0-09f0426e7429
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 90a484224c35abd492db2409e6ec8e91c398d71a
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106782"
---
# <a name="tutorial-integrate-appraisd-with-azure-active-directory"></a>Oktatóanyag: Appraisd integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a Appraisd integrálása az Azure Active Directory (Azure AD) lesz. Appraisd integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá Appraisd Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezve Appraisd az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Appraisd egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja a Appraisd **SP és IDP** által kezdeményezett egyszeri bejelentkezés.

## <a name="adding-appraisd-from-the-gallery"></a>Appraisd hozzáadása a katalógusból

Az Azure AD integrálása a Appraisd konfigurálásához hozzá kell Appraisd a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Appraisd** kifejezést a keresőmezőbe.
1. Válassza ki **Appraisd** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata Appraisd **b Simon**. Az SSO működjön kell Appraisd az Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre.

Az Azure AD SSO Appraisd tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálja a Appraisd](#configure-appraisd)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés a b Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  b Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Appraisd tesztfelhasználót](#create-appraisd-test-user)**  van egy megfelelője a b Simon Appraisd, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Appraisd** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban az alkalmazás előre konfigurálva, és a szükséges URL-címek vannak már előre fel van töltve az Azure-ral. A felhasználónak a konfiguráció mentéséhez kattintson a Mentés gombra, és hajtsa végre az alábbi lépéseket:

    a. Kattintson a **további URL-címet beállítani**.

    b. Az a **továbbítási állapot** szövegmezőbe írja be egy URL-címe: `<TENANTCODE>`

    c. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód, a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://app.appraisd.com/saml/<TENANTCODE>`

    > [!NOTE]
    > A tényleges bejelentkezési URL- és a továbbítási állapot értéke a Appraisd egyszeri bejelentkezési konfiguráció lapon, az oktatóanyag későbbi részében ismertetett kap.

1. Appraisd alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg, hol **nameidentifier** le van képezve a **user.userprincipalname**. Appraisd alkalmazás vár **nameidentifier** a leképezendő **user.mail**, így kell szerkesztenie a attribútumleképezés kattintva **szerkesztése**  ikonra, és módosítsa a attribútumleképezés.

    ![image](common/edit-attribute.png)

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **tanúsítvány (Base64)** válassza **letöltése** töltse le a tanúsítványt, és menti azt a számítógépet.

   ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. Az a **Appraisd beállítása** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-appraisd"></a>Appraisd konfigurálása

1. Automatizálhatja a Appraisd konfigurációra, telepítenie kell **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** kattintva **a bővítmény telepítése**.

    ![Saját alkalmazások kiterjesztése](common/install-myappssecure-extension.png)

2. A felvett bővítmény a böngészőre, kattintson a **telepítő Appraisd** fog irányítja át a Appraisd alkalmazás. Itt adja meg a rendszergazdai hitelesítő adataival bejelentkezni Appraisd. A webböngésző-bővítmény automatikusan konfigurálja az alkalmazást, és 3-7 lépések automatizálásához.

    ![Konfiguráció beállítása](common/setup-sso.png)

3. Ha azt szeretné, manuálisan állíthatja be az Appraisd, nyisson meg egy új böngészőablakban, és jelentkezzen be rendszergazdaként vállalati Appraisd webhelyét, és hajtsa végre az alábbi lépéseket:

4. A felső, az oldal jobb kattintson a **beállítások** ikonra, majd keresse meg a **konfigurációs**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_sett.png)

5. A menüt a bal oldali menüjében kattintson a **SAML egyszeri bejelentkezés**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_single.png)

6. Az a **SAML 2.0 egyszeri bejelentkezés konfigurációja** lapon, a következő lépésekkel:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_saml.png)

    a. Másolás a **alapértelmezett továbbítási állapot** értékét, és illessze be a **továbbítási állapot** szövegmezőjébe **alapszintű SAML-konfigurációja** az Azure Portalon.

    b. Másolás a **szolgáltatás által kezdeményezett bejelentkezési URL-cím** értékét, és illessze be a **bejelentkezési URL-** szövegmezőjébe **alapszintű SAML-konfigurációja** az Azure Portalon.

7. Görgessen lefelé az oldalon, amelyek ugyanazt a **felhasználók azonosítása**, hajtsa végre az alábbi lépéseket:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_identifying.png)

    a. Az a **Identity Provider egyszeri bejelentkezési URL-** szövegmezőbe, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalon, majd kattintson a másolt **mentése**.

    b. Az a **Identity Provider kiállítójának URL-címe** szövegmezőbe, illessze be az értéket a **az Azure AD-azonosító**, az Azure Portalon, majd kattintson a másolt **mentése**.

    c. A Jegyzettömbben, nyissa meg az Azure Portalról letöltött base-64 kódolású tanúsítványt, másolja a tartalmat, és illessze be azt a **X.509-tanúsítvány** mezőbe, majd kattintson a **mentése**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon b Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B. Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B. Simon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban b Simon által biztosított hozzáférés Appraisd Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Appraisd**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **b Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-appraisd-test-user"></a>Appraisd tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD felhasználói bejelentkeznek a Appraisd, akkor ki kell építeni Appraisd be. Appraisd a kiépítés manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be egy biztonsági-rendszergazdaként Appraisd.

2. A felül, jobb a lapon kattintson a **beállítások** ikonra, majd keresse meg a **felügyeleti központ**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_admin.png)

3. Kattintson az eszköztáron az oldal tetején lévő **személyek**, majd keresse meg a **új felhasználó hozzáadása**.

    ![image](./media/appraisd-tutorial/tutorial_appraisd_user.png)

4. Az a **új felhasználó hozzáadása** lapon, a következő lépésekkel:

    ![image](./media/appraisd-tutorial/tutorial_appraisd_newuser.png)

    a. A **Utónév** szöveget adja meg például a felhasználó utónevét **Britta**.

    b. A **Vezetéknév** szöveget adja meg például a felhasználó vezetékneve **simon**.

    c. A **E-mail** szöveget adja meg az e-mailt, például a felhasználó `B. Simon@contoso.com`.

    d. Kattintson a **Felhasználó hozzáadása** parancsra.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A Appraisd csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a Appraisd, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)