---
title: 'Oktatóanyag: Úgy tesszük az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a úgy tesszük között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/20/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: faa23f61e5a213c492a7fb51bfc5b108e5c77946
ms.sourcegitcommit: 5cb0b6645bd5dff9c1a4324793df3fdd776225e4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2019
ms.locfileid: "67310407"
---
# <a name="tutorial-integrate-way-we-do-with-azure-active-directory"></a>Oktatóanyag: Úgy tesszük az Azure Active Directory integrálása

Ebben az oktatóanyagban elsajátíthatja a úgy tesszük integrálása az Azure Active Directory (Azure AD) lesz. Integrálása úgy tesszük az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá, így még a nem Azure AD-ben.
* Engedélyezze a felhasználókat, hogy a rendszer automatikusan bejelentkezve úgy tesszük az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, beszerezheti a egyhónapos ingyenes próbaidőszakot [Itt](https://azure.microsoft.com/pricing/free-trial/).
* Úgy, hogy egyszeri bejelentkezési (SSO) az előfizetés engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben.

* Így még a nem támogatja a **SP** által kezdeményezett egyszeri bejelentkezés
* Így még a nem támogatja a **igény szerinti** felhasználók átadása

## <a name="adding-way-we-do-from-the-gallery"></a>Úgy tesszük hozzáadása a katalógusból

Az Azure AD-be, így még a nem az integráció konfigurálásához hozzá kell úgy tesszük a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **úgy tesszük** kifejezést a keresőmezőbe.
1. Válassza ki **úgy tesszük** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Úgy tesszük nevű tesztfelhasználó használata az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása **B.Simon**. Az SSO működjön kell úgy tesszük az Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre.

Úgy tesszük az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Így tudjuk tegye egyszeri bejelentkezést](#configure-way-we-do-sso)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre felhasználói teszteljük módon](#create-way-we-do-test-user)**  – így még a nem kapcsolódó felhasználói reprezentációja az Azure AD-megfelelője a Britta Simon van.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **úgy tesszük** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** lap, adja meg az értékeket a következő mezőket:

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [módon azt tegye ügyfél-támogatási csapatának](mailto:support@waywedo.com) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **tanúsítvány (Raw)** válassza **letöltése**töltse le a tanúsítványt, és menti azt a számítógépet.

   ![A tanúsítvány letöltési hivatkozás](common/certificateraw.png)

1. Az a **állítsa be úgy tesszük** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-way-we-do-sso"></a>Úgy tesszük az egyszeri bejelentkezés konfigurálása

1. Automatizálhatja a konfigurációra úgy tesszük, telepítenie kell **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** kattintva **a bővítmény telepítése**.

    ![Saját alkalmazások kiterjesztése](common/install-myappssecure-extension.png)

1. A felvett bővítmény a böngészőre, kattintson a **a telepítő úgy tesszük** fog irányítja át a úgy tesszük alkalmazás. Itt adja meg a rendszergazdai hitelesítő adataival jelentkezhetnek be, így még a nem. A webböngésző-bővítmény automatikusan konfigurálja az alkalmazást, és 3 – 6. lépések automatizálásához.

    ![Konfiguráció beállítása](common/setup-sso.png)

1. Ha szeretné manuálisan beállítani a úgy tesszük, nyisson meg egy új böngészőablakban, és jelentkezzen be a rendszergazda úgy tesszük céges webhelyet, és hajtsa végre az alábbi lépéseket:

1. Kattintson a **személy ikon** az bármely módon végzünk az oldal jobb felső sarkában kattintson a **fiók** a legördülő menüből.

    ![Úgy tesszük fiók](./media/waywedo-tutorial/tutorial_waywedo_account.png)

1. Kattintson a **menüikonra** nyissa meg a leküldéses navigációs menüben, majd kattintson a **az egyszeri bejelentkezést**.

    ![Úgy tesszük egyetlen](./media/waywedo-tutorial/tutorial_waywedo_single.png)

1. Az a **egyszeri bejelentkezés beállítása** lapon, a következő lépésekkel:

    ![Mentés végzünk módja](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Kattintson a **kapcsolja be az egyszeri bejelentkezés** kapcsolót **Igen** egyszeri bejelentkezés engedélyezéséhez.

    b. Az a **egyetlen bejelentkezési nevet** szövegmezőbe írja be a nevét.

    c. A a **Entitásazonosító** szövegmezőjébe illessze be az értéket, **az Azure AD-azonosító**, az Azure Portalról másolt.

    d. Az a **SAML egyszeri bejelentkezési URL-cím** szövegmező, illessze be az értéket a **bejelentkezési URL-cím**, az Azure Portalról másolt.

    e. Gombra kattintva töltse fel a tanúsítványt a **kiválasztása** megjelenítő gombra **tanúsítvány**.

    f. **Választható beállítások** -
    
    * Engedélyezi a jelszavak – Ha ez a beállítás le van tiltva, a rendszeres jelszó függvények a úgy tesszük, hogy a felhasználók csak használhatják az egyszeri bejelentkezés.

    * Engedélyezze az Automatikus kiépítés – Ha ez engedélyezve van, a bejelentkezéshez használt e-mail-cím lesz automatikusan összehasonlítva úgy tesszük a felhasználók listája. Ha az e-mail-cím nem egyezik meg az aktív felhasználók az úgy tesszük, automatikusan hozzáadja egy új felhasználói fiók jelentkezik be, a hiányzó információit kérő személy.

      > [!NOTE]
      > Egyszeri bejelentkezés során hozzáadott felhasználók általános felhasználói is hozzáadja, és nincsenek hozzárendelve egy szerepkörhöz a rendszerben. Rendszergazdaként nyissa meg, és módosíthatja az ő biztonsági szerepkörük-szerkesztőben vagy rendszergazdaként, és szerepkörök is hozzárendelhet egy vagy több szervezeti diagram.

    g. Kattintson a **mentése** megőrizni a beállításokat.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon B.Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban B.Simon alapján ad hozzáférést, így még a nem Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **úgy tesszük**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **B.Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-way-we-do-test-user"></a>Úgy tesszük tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó a úgy tesszük jön létre. Így még a nem támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik a úgy tesszük, egy új jön létre a hitelesítés után.

> [!Note]
> Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [módon azt tegye ügyfél-támogatási csapatának](mailto:support@waywedo.com).

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A úgy tesszük csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a úgy tesszük, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)