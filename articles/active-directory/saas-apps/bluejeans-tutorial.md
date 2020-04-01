---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Azure AD-hez tartozó BlueJeans szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a BlueJeans for Azure AD között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dfc634fd-1b55-4ba8-94a8-b8288429b6a9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/18/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: dfc38f63c5b6361122c236543320b91d22faa70a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72595054"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-bluejeans-for-azure-ad"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Azure AD-hez való BlueJeans csomaggal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a BlueJeans for Azure AD-t az Azure Active Directoryval (Azure AD). Ha integrálja a BlueJeans for Azure AD-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a BlueJeans for Azure AD.Control in Azure AD who has access to BlueJeans for Azure AD.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve bluejeans for Azure AD az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* BlueJeans az Azure AD egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A BlueJeans for Azure AD támogatja az **SP** által kezdeményezett SSO-t

* A BlueJeans for Azure AD támogatja [ **az automatikus** felhasználói kiépítést](bluejeans-provisioning-tutorial.md)

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-bluejeans-for-azure-ad-from-the-gallery"></a>BlueJeans hozzáadása az Azure AD-hez a galériából

A BlueJeans for Azure AD Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a BlueJeans for Azure AD-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a katalógusból** szakaszban írja be **a BlueJeans for Azure AD** a keresőmezőbe.
1. Válassza ki **a BlueJeans for Azure AD** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-bluejeans-for-azure-ad"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését az AzureJeans azure AD-hez

Konfigurálja és tesztelje az Azure AD SSO-t a BlueJeans for Azure AD-vel egy **B.Simon**nevű tesztfelhasználó használatával. Az SSO működéséhez létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a BlueJeans for Azure AD-ben.

Az Azure AD SSO konfigurálásához és teszteléséhez az Azure AD-hez készült BlueJeans szolgáltatással hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a BlueJeans az Azure AD Egyszeri bejelentkezés](#configure-bluejeans-for-azure-ad-sso)** – az alkalmazás oldalán az egyszeri bejelentkezési beállítások konfigurálásához.
    1. **[Hozzon létre BlueJeans azure AD teszt felhasználó](#create-bluejeans-for-azure-ad-test-user)** – egy megfelelője B.Simon a BlueJeans azure AD, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **BlueJeans for Azure AD** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.bluejeans.com`

    > [!NOTE]
    > Az érték nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [a BlueJeans for Azure AD ügyfél támogatási csapatával](https://support.bluejeans.com/contact) az érték lekéri. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **BlueJeans beállítása az Azure AD-hez** szakaszban másolja a megfelelő URL-cím(ek) a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t, hozzáférést biztosítva a BlueJeans az Azure AD.In this section, you'll enable B.Simon to use Azure single sign-on by access to BlueJeans for Azure AD.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **BlueJeans for Azure AD lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-bluejeans-for-azure-ad-sso"></a>BlueJeans konfigurálása az Azure AD SSO-hoz

1. Egy másik böngészőablakban jelentkezzen be a **BlueJeans for Azure AD** vállalati webhelyére rendszergazdaként.

2. Nyissa meg a **FELÜGYELETI \> CSOPORT BEÁLLÍTÁSAINAK \> BIZTONSÁGA lapot.**

    ![Felügyelet](./media/bluejeans-tutorial/ic785868.png "Rendszergazda")

3. A **BIZTONSÁG szakaszban** hajtsa végre az alábbi lépéseket:

    ![SAML egyszeri bejelentkezés](./media/bluejeans-tutorial/ic785869.png "SAML egyszeri bejelentkezés")

    a. Válassza **az SAML Single Sign On lehetőséget.**

    b. Válassza **az Automatikus kiépítés engedélyezése**lehetőséget.

4. Lépjen tovább a következő lépésekkel:

    ![Tanúsítvány elérési útja](./media/bluejeans-tutorial/ic785870.png "Tanúsítvány elérési útja")

    a. Kattintson **a Fájl kiválasztása**gombra az Azure Portalról letöltött 64-es alapú kódolt tanúsítvány feltöltéséhez.

    b. A **Bejelentkezési URL-cím** mezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    c. A **Jelszómódosítása URL-címmezőbe** illessze be a **Jelszó url-címének módosítása** az Azure Portalról másolt értékét.

    d. A **Kijelentkezés URL-cím** beírt mezőjébe illessze be a **Kijelentkezés i URL-címét,** amelyet az Azure Portalról másolt.

5. Lépjen tovább a következő lépésekkel:

    ![Módosítások mentése](./media/bluejeans-tutorial/ic785874.png "Módosítások mentése")

    a. A **Felhasználói azonosító** mezőbe írja `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`be a következőt:

    b. Az **E-mail** mezőbe `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name`írja be a következőt:

    c. Kattintson **a Módosítások mentése gombra.**

### <a name="create-bluejeans-for-azure-ad-test-user"></a>BlueJeans létrehozása az Azure AD tesztfelhasználójának

Ez a szakasz célja, hogy hozzon létre egy b.Simon nevű felhasználó ta- Azure AD.The objective of this section is to create a user called B.Simon in BlueJeans for Azure AD. A BlueJeans for Azure AD támogatja az automatikus felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Az automatikus felhasználói [here](bluejeans-provisioning-tutorial.md) kiépítés konfigurálásáról itt olvashat bővebben.

**Ha manuálisan kell létrehoznia a felhasználót, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **BlueJeans for Azure AD** vállalati webhelyére rendszergazdaként.

2. Tovább a ** \> ADMIN \> KEZELÉSE FELHASZNÁLÓK HOZZÁADÁSA FELHASZNÁLÓ**.

    ![Felügyelet](./media/bluejeans-tutorial/ic785877.png "Rendszergazda")

    > [!IMPORTANT]
    > Az **ADD USER** lap csak akkor érhető el, ha a **SECUTIRY lapon**nincs bejelölve az Automatikus **kiépítés engedélyezése.**

3. Az **ADD USER (FELHASZNÁLÓ hozzáadása)** szakaszban hajtsa végre az alábbi lépéseket:

    ![Felhasználó hozzáadása](./media/bluejeans-tutorial/ic785886.png "Felhasználó hozzáadása")

    a. Az **Utónév** mezőbe írja be a felhasználó keresztnevét, például **B.**

    b. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például **Simon**.

    c. A **BlueJeans for Azure AD username** mezőbe írja be a felhasználó felhasználónevét, például **Brittasimon**

    d. A **Jelszó létrehozása** mezőbe írja be a jelszót.

    e. A **Vállalat** mezőbe írja be a Vállalat értéket.

    f. Az **E-mail cím** mezőbe írja `b.simon\@contoso.com`be a felhasználó e-mail címét, például .

    g. A **BlueJeans létrehozása az Azure AD-értekezlet azonosítójának** szövegmezőjében adja meg az értekezlet azonosítóját.

    h. A **Válassza ki a moderátor jelkód** szövegmező, adja meg a jelszót.

    i. Kattintson **a FOLYTATÁS gombra.**

    ![Felhasználó hozzáadása](./media/bluejeans-tutorial/ic785887.png "Felhasználó hozzáadása")

    J. Kattintson **a Felhasználó hozzáadása gombra.**

> [!NOTE]
> Az Azure AD-felhasználói fiókok létrehozásához használt bármely más BlueJeans-eszköz vagy API-k azure AD-hez való kiépítéséhez használhatja a BlueJeans for Azure AD által biztosított eszközöket.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a BlueJeans for Azure AD csempére kattint, automatikusan be kell jelentkeznie a BlueJeans for Azure AD-be, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a BlueJeans for Azure AD-t az Azure AD-vel](https://aad.portal.azure.com/)

