---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Zoommal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Nagyítás között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5f9d727154adf0a2099d7a9144c109cef9c91238
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "70743966"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-zoom"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a nagyítással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Nagyítást az Azure Active Directoryval (Azure AD). Ha integrálja a Nagyítást az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Zoomhoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve nagyítás az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Egyetlen bejelentkezéssel (SSO) engedélyezett előfizetés nagyításával.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A zoom támogatja az **SP** által kezdeményezett SSO-t és 
* A nagyítás támogatja az [ **automatikus** felhasználói kiépítést.](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial)

## <a name="adding-zoom-from-the-gallery"></a>Nagyítás hozzáadása a galériából

A Nagyítás Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a nagyítást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Nagyítás** kifejezést a keresőmezőbe.
1. Válassza a **Nagyítás** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zoom"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése nagyításhoz

Konfigurálja és tesztelje az Azure AD SSO-t nagyítással egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó nagyításban.

Az Azure AD-sSO nagyítással való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
2. **[Állítsa be a Zoom SSO-t](#configure-zoom-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre zoom teszt felhasználó](#create-zoom-test-user)** - a B.Simon nagyítás, amely kapcsolódik az Azure AD felhasználói ábrázolása.
3. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az Azure Portalon keresse meg a **Kezelés** **szakaszt,** és válassza az Egyszeri bejelentkezés lehetőséget az [Azure Portalon.](https://portal.azure.com/) **Single sign-on**
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.zoom.us`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`<companyname>.zoom.us`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a Zoom Ügyfél támogatási csapatával,](https://support.zoom.us/hc/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Nagyítás beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

> [!NOTE]
> A szerepkör azure-ban AD-ben való konfigurálásáról a [Vállalati alkalmazások SAML-jogkivonatában kiadott szerepkörjogcím konfigurálása](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)című témakörben olvashat.

> [!NOTE]
> A nagyítás esetén csoportos jogcím várható az SAML-tartalomban. Ha létrehozott csoportokat, lépjen kapcsolatba az [Ügyfél nagyításával foglalkozó támogatási csoporttal](https://support.zoom.us/hc/) a csoportadataival, hogy azok a csoportadatait a saját helyükön konfigurálhassák. Az objektumazonosítót is meg kell adnia az [ügyfél nagyításához támogató csapat](https://support.zoom.us/hc/) számára, hogy az objektumazonosítót a saját végén konfigurálhassák. Az objektumazonosító lekért: [A Nagyítás konfigurálása az Azure-ral](https://support.zoom.us/hc/articles/115005887566)című témakörben látható.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a nagyításhoz való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Nagyítás**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-zoom-sso"></a>Nagyítási sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Zoom vállalati webhelyére rendszergazdaként.

2. Kattintson az **Egyszeri bejelentkezés** fülre.

    ![Egyszeri bejelentkezés lap](./media/zoom-tutorial/ic784700.png "Egyszeri bejelentkezés")

3. Kattintson a **Biztonsági vezérlő** fülre, majd nyissa meg az **Egyszeri bejelentkezés** beállításait.

4. Az Egyszeri bejelentkezés szakaszban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezési szakasz](./media/zoom-tutorial/ic784701.png "Egyszeri bejelentkezés")

    a. A **Bejelentkezési lap URL-címmezőjébe** illessze be az Azure Portalról másolt **bejelentkezési URL-cím** értékét.

    b. A **kijelentkezési lap URL-értékéhez** nyissa meg az Azure Portalt, és kattintson a bal oldalon az **Azure Active Directoryra,** majd keresse meg az **alkalmazásregisztrációkat.**

    ![Az Azure Active Directory gombja](./media/zoom-tutorial/appreg.png)

    c. Kattintson a **Végpontokra**

    ![A Végpont gomb](./media/zoom-tutorial/endpoint.png)

    d. Másolja az **SAML-P SIGN-OUT ENDPOINT-ot,** és illessze be a **Kijelentkezési lap URL-címének** szövegmezőjébe.

    ![A Végpont másolása gomb](./media/zoom-tutorial/endpoint1.png)

    e. Nyissa meg az alap-64 kódolású tanúsítványt a jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be az **Identitásszolgáltató tanúsítványának** szövegmezőjébe.

    f. A **Kiállító** szövegmezőbe illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt. 

    g. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > További információkért látogasson el a zoom dokumentáció[https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-zoom-test-user"></a>Nagyítási tesztfelhasználó létrehozása

A cél ebben a szakaszban, hogy hozzon létre egy felhasználó nevű B.Simon a Zoom. A nagyítás támogatja az automatikus felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Az automatikus felhasználói [here](https://docs.microsoft.com/azure/active-directory/saas-apps/zoom-provisioning-tutorial) kiépítés konfigurálásáról itt olvashat bővebben.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, kapcsolatba kell lépnie [az Ügyfél nagyítási támogatási csapatával](https://support.zoom.us/hc/)

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Nagyítás csempére kattint, automatikusan be kell jelentkeznie arra a nagyításra, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a nagyítást az Azure AD-vel](https://aad.portal.azure.com/)
