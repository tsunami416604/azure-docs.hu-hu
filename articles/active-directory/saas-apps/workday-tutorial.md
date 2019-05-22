---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a workdayjel képes |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Workday között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: e9da692e-4a65-4231-8ab3-bc9a87b10bca
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bb9c8d1fb234efd5df297082cfc1001f28ca1656
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990399"
---
# <a name="tutorial-integrate-workday-with-azure-active-directory"></a>Oktatóanyag: Workday integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a Workday integrálása az Azure Active Directory (Azure AD) lesz. Workday integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá a Workday az Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezett a Workday az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Munkanapi egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja a WORKDAY **SP** által kezdeményezett egyszeri bejelentkezés.

## <a name="adding-workday-from-the-gallery"></a>Workday hozzáadása a katalógusból

Az Azure AD integrálása a Workday konfigurálásához hozzá kell Workday a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Workday** kifejezést a keresőmezőbe.
1. Válassza ki **Workday** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata workdayjel képes **Britta Simon**. SSO működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolatot hozhat létre az WORKDAY kell.

Az Azure AD SSO Workday tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálja a Workday](#configure-workday)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Workday tesztfelhasználót](#create-workday-test-user)**  szeretné, hogy egy Britta Simon megfelelője a WORKDAY, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Workday** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** lap, adja meg az értékeket a következő mezőket:

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://impl.workday.com/<tenant>/login-saml2.flex`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://www.workday.com`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://impl.workday.com/<tenant>/login-saml.htmld`

    > [!NOTE]
    > Ezek az értékek nem a valós. Ezek az értékek frissítse a tényleges bejelentkezési URL-cím és a válasz URL-cím. A válasz URL-cím rendelkeznie kell egy altartomány például: www, wd2, wd3, wd3-impl, wd5, wd5-impl).
    > Vagy hasonló `http://www.myworkday.com` működik, de `http://myworkday.com` nem létezik. Kapcsolattartó [Workday ügyfél-támogatási csapatának](https://www.workday.com/partners-services/services/support.html) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

6. A Workday-alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg, hol **nameidentifier** le van képezve a **user.userprincipalname**. Munkanapi alkalmazás vár **nameidentifier** a leképezendő **user.mail**, **UPN**stb., így a attribútumleképezés szerkesztéséhez kattintson a szükséges  **Szerkesztés** ikonra, és módosítsa a attribútumleképezés.

    ![image](common/edit-attribute.png)

    > [!NOTE]
    > Itt azt leképezett az egyszerű felhasználónév (user.userprincipalname) a Névazonosító alapértelmezettként. Meg kell feleltetni a Névazonosító a tényleges felhasználói Azonosítót a Workday-fiókban (az e-mailben UPN, stb.) az egyszeri bejelentkezés sikeres használatához.

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **tanúsítvány (Base64)** válassza **letöltése** töltse le a tanúsítványt, és menti azt a számítógépet.

   ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. Módosíthatja a **aláírás** lehetőségek a követelmény megfelelően kattintson **szerkesztése** gombra kattintva nyissa meg a **SAML-aláíró tanúsítvány** párbeszédpanel.

    ![image](common/edit-certificate.png) 

    ![image](./media/workday-tutorial/signing-option.png)

    a. Válassza ki **bejelentkezési SAML-válasz és -előfeltétel** a **aláírási beállítás**.

    b. Kattintson a **Save** (Mentés) gombra

1. Az a **állítsa be a Workday** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-workday"></a>Workday konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Workday vállalati hely rendszergazdaként.

2. Az a **keresőmezőbe** nevű keresési **szerkesztése Bérlőbeállítást – biztonsági** bal oldalt a kezdőlap tetején található.

    ![Bérlői biztonsági szerkesztése](./media/workday-tutorial/IC782925.png "bérlői biztonsági szerkesztése")

3. Az a **átirányítási URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Redirection URLs](./media/workday-tutorial/IC7829581.png "Redirection URLs")

    a. Kattintson a **sort ad hozzá a**.

    b. Az a **bejelentkezés átirányítási URL-cím**, **időtúllépési átirányítási URL-cím** és **Mobile átirányítási URL-cím** szövegmezőjébe illessze be a **bejelentkezési URL-cím** másolta, amely az a **állítsa be a Workday** szakaszban az Azure Portalon.

    c. Az a **kijelentkezési átirányítási URL-cím** szövegmezőjébe illessze be a **kijelentkezési URL-címe** másolta amely a **állítsa be a Workday** szakaszban az Azure Portalon.

    d. A **környezetekben használt** szövegmezőben válassza ki a környezet nevét.  

   > [!NOTE]
   > A környezet attribútum vannak kötve, a bérlői URL-cím értékét:  
   > -Ha a tartomány neve a Workday-bérlői URL-cím kezdődik impl például: *https:\//impl.workday.com/\<bérlői\>/login-saml2.flex*), a **környezet**attribútum megvalósítási értékre kell állítani.  
   > -Ha a tartománynév valami mást kezdődik, lépjen kapcsolatba kell [Workday ügyfél-támogatási csapatának](https://www.workday.com/partners-services/services/support.html) beolvasni a megfelelő **környezet** érték.

4. Az a **SAML-telepítő** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SAML-telepítő](./media/workday-tutorial/IC782926.png "SAML-telepítő")

    a.  Válassza ki **SAML-hitelesítés engedélyezése**.

    b.  Kattintson a **sort ad hozzá a**.

5. Az a **SAML Identitásszolgáltatók** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SAML-Identitásszolgáltatók](./media/workday-tutorial/IC7829271.png "SAML Identitásszolgáltatók")

    a. Az a **identitásszolgáltató neve** szövegmezőbe írja be a szolgáltató nevét (például: *SPInitiatedSSO*).

    b. Az Azure Portalon az a **állítsa be a Workday** területén másolja a **az Azure AD-azonosító** értéket, és illessze be azt a **kibocsátó** szövegmezőbe.

    ![SAML-Identitásszolgáltatók](./media/workday-tutorial/IC7829272.png "SAML Identitásszolgáltatók")

    c. Az Azure Portalon az a **állítsa be a Workday** területén másolja a **kijelentkezési URL-címe** értéket, és illessze be azt a **kijelentkezési válasz URL-címe** szövegmezőbe.

    d. Az Azure Portalon az a **állítsa be a Workday** területén másolja a **bejelentkezési URL-cím** értéket, és illessze be azt a **identitásszolgáltató egyszeri bejelentkezési szolgáltatás URL-címe** szövegmezőbe.

    e. A **környezetekben használt** szövegmezőben válassza ki a környezet nevét.

    f. Kattintson a **szolgáltató nyilvános kulcs Identitástanúsítványt**, és kattintson a **létrehozás**.

    ![Hozzon létre](./media/workday-tutorial/IC782928.png "létrehozása")

    g. Kattintson a **x509 létrehozása nyilvános kulcs**.

    ![Hozzon létre](./media/workday-tutorial/IC782929.png "létrehozása")

6. Az a **nézet x509 nyilvános kulcs** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Nyilvános kulcs nézet x509](./media/workday-tutorial/IC782930.png "nézet x509 nyilvános kulcs")

    a. Az a **neve** szövegmezőbe írja be a tanúsítvány nevét (például: *PPE\_SP*).

    b. Az a **érvényes a** szövegmezőbe írja be az attribútum értékét a tanúsítvány érvényes.

    c.  Az a **érvényes való** szövegmezőbe írja be a tanúsítvány attribútum értéke érvénytelen.

    > [!NOTE]
    > Kérheti az érvényes dátumot és az érvényes, ha duplán kattint a letöltött tanúsítvány az a dátum a.  A dátumok vannak felsorolva a **részletek** fülre.
    >
    >

    d.  Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, és másolja a tartalmát.

    e.  Az a **tanúsítvány** szövegmezőjébe illessze be a vágólap tartalmát.

    f.  Kattintson az **OK** gombra.

7. Hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurációja](./media/workday-tutorial/WorkdaySSOConfiguratio.png "egyszeri bejelentkezés konfigurálása")

    a.  Az a **szolgáltatás Szolgáltatóazonosító** szövegmezőbe írja be **https://www.workday.com**.

    b. Válassza ki **nem Deflate hitelesítési Szolgáltató által kezdeményezett kérelem**.

    c. Mint **hitelesítési kérelmek aláírását módszer**válassza **SHA256**.

    ![Hitelesítési kérelem-aláírás módszer](./media/workday-tutorial/WorkdaySSOConfiguration.png "hitelesítési kérelmek aláírását módja") 

    d. Kattintson az **OK** gombra.

    ![OK](./media/workday-tutorial/IC782933.png "OK")

    > [!NOTE]
    > Ellenőrizze, hogy megfelelően állítsa be egyszeri bejelentkezést. Abban az esetben, ha egyszeri bejelentkezést helytelen telepítés engedélyezi, nem lehet kizárva, és adja meg az alkalmazás a hitelesítő adataival. Ebben a helyzetben a Workday biztosít egy biztonsági mentési bejelentkezési URL-cím, a felhasználók is be a normál felhasználónevükkel és jelszavukkal használatával a következő formátumban: [a Workday URL]/login.flex?redirect=n

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon Britta Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `BrittaSimon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a hozzáférés biztosításával workdaybe Azure egyszeri bejelentkezés használatára Britta Simon engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Workday**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-workday-test-user"></a>Workday tesztfelhasználó létrehozása

Ebben a szakaszban egy WORKDAY Britta Simon nevű felhasználó létrehozásához. Együttműködve [Workday ügyfél-támogatási csapatának](https://www.workday.com/en-us/partners-services/services/support.html) a felhasználók hozzáadása a Workday-platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A Workday csempe kiválasztásakor a hozzáférési panelen, érdemes lehet automatikusan bejelentkezett a Workday, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
