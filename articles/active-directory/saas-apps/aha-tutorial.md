---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Aha! | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Aha!.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ad955d3d-896a-41bb-800d-68e8cb5ff48d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/09/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a63872680d28664c6d5a7ff109f6de72817173d5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68989650"
---
# <a name="tutorial-integrate-aha-with-azure-active-directory"></a>Bemutató: Integrálja Aha! az Azure Active Directoryval

Ebben a tutorial, megtudhatja, hogyan kell integrálni Aha! az Azure Active Directory (Azure AD) használatával. Ha integrálja Aha! az Azure AD-vel a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az Aha!.
* Engedélyezze a felhasználók számára, hogy automatikusan bejelentkeznek az Aha-ba! az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Aha! egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Aha! támogatja az **SP** által kezdeményezett SSO-t
* Aha! támogatja a **Just In Time** felhasználói kiépítést

## <a name="adding-aha-from-the-gallery"></a>Hozzáadása Aha! a galériából

Konfigurálása az integráció Aha! az Azure AD-be, hozzá kell adnia Aha!-t! a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a galéria szakaszban** írja be **az Aha!** a keresőmezőbe.
1. Válassza **az Aha!** az eredménypanelről, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-aha"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezést az Aha!-hoz!

Konfigurálja és tesztelje az Azure AD SSO-t az Aha!-val! **b.simon**nevű tesztfelhasználót használ. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a Aha!.

Az Azure AD SSO konfigurálásához és teszteléséhez az Aha!-val, hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
2. **[Konfigurálja Aha! Egyszeri bejelentkezés](#configure-aha-sso)** – az egyszeri bejelentkezés i beállításainak konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre Aha! teszt felhasználó](#create-aha-test-user)** -, hogy egy megfelelője B.Simon Aha! amely kapcsolódik az Azure AD felhasználói ábrázolása.
3. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure portalon](https://portal.azure.com/), az **Aha!** alkalmazásintegrációs lapon keresse meg a **Manage szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.aha.io/session/new`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.aha.io`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Kapcsolat [Aha! Az ügyféltámogatási csapat](https://www.aha.io/company/contact) ezeket az értékeket kapja. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Felállítanak Aha!-n!** szakaszt, másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
    1. A **Név** mezőbe írja a következőt: `B.Simon`.  
    1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
    1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
    1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a hozzáférés biztosításával Aha!.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **Aha!** lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-aha-sso"></a>Konfigurálja Aha! SSO

1. Az Aha! konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Hozzáadása után kiterjesztés a böngésző, kattintson a **Setup Aha!** majd közvetlen, hogy az Aha! Alkalmazás. Onnan adja meg az admin hitelesítő adatokat, hogy jelentkezzen be Aha!. A böngészőbővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-8.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha azt szeretnénk, hogy beállít Aha! manuálisan, nyisson meg egy új böngésző ablakot, és jelentkezzen be a Aha! a vállalati webhelyet rendszergazdaként, és hajtsa végre a következő lépéseket:

4. A felső menüben kattintson a **Beállítások gombra.**

    ![Beállítások](./media/aha-tutorial/IC798950.png "Beállítások")

5. Kattintson **a Fiók gombra.**
  
    ![Profil](./media/aha-tutorial/IC798951.png "Profil")

6. Kattintson **a Biztonság és egyszeri bejelentkezés gombra.**

    ![Biztonság és egyszeri bejelentkezés](./media/aha-tutorial/IC798952.png "Biztonság és egyszeri bejelentkezés")

7. Az Egyszeri bejelentkezés **szakaszidentitás-szolgáltatóként**válassza az **SAML2.0**lehetőséget. **Single Sign-On**

    ![Biztonság és egyszeri bejelentkezés](./media/aha-tutorial/IC798953.png "Biztonság és egyszeri bejelentkezés")

8. Az **Egyszeri bejelentkezés** konfigurációs lapon hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés](./media/aha-tutorial/IC798954.png "Egyszeri bejelentkezés")

    a. A **Név** mezőbe írja be a konfiguráció nevét.

    b. A **Konfigurálás használata esetén**válassza a **Metaadat-fájl lehetőséget.**

    c. A letöltött metaadatfájl feltöltéséhez kattintson a **Tallózás gombra.**

    d. Kattintson a **Frissítés** parancsra.

### <a name="create-aha-test-user"></a>Hozd létre Aha! teszt felhasználó

Ebben a szakaszban egy B.Simon nevű felhasználó jön létre az Aha!. Aha! támogatja a just-in-time felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik az Aha!, egy új jön létre a hitelesítés után.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha rákattint az Aha! csempét a hozzáférési panelen, akkor automatikusan be kell jelentkeznie az Aha! amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

