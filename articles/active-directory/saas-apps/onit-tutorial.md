---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Onittal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Onit között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/28/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e908cb76a57f027494230edc648b69da0730ac27
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "70164246"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-onit"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Onittal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Onitot az Azure Active Directoryval (Azure AD). Ha integrálja az Onit-ot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az Onithoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve onit az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Onit egyszeri bejelentkezés (SSO) engedélyezve van az előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Onit támogatja **SP** kezdeményezett SSO

## <a name="adding-onit-from-the-gallery"></a>Onit hozzáadása a galériából

Az Onit Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az Onit-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be **az Onit** kifejezést a keresőmezőbe.
1. Válassza **az Onit** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-onit"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése az Onit hoz

Konfigurálja és tesztelje az Azure AD SSO-t az Onit segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó onit.

Az Azure AD-sSO onit szolgáltatással való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja onit egyszeri egyszeri](#configure-onit-sso)** bejelentkezési beállításokat az alkalmazás oldalán.
    1. **[Hozzon létre Onit teszt felhasználó](#create-onit-test-user)** - egy megfelelője B.Simon onit, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **Onit-alkalmazás** integrációja lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<sub-domain>.onit.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<sub-domain>.onit.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [az Onit ügyfél támogatási csapatával](https://www.onit.com/support) az értékek lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **SAML aláíró tanúsítvány csoportban** kattintson a **Szerkesztés** gombra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![SAML aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

1. Az **SAML aláíró tanúsítvány szakaszban** másolja a **hüvelykujj értékét,** és mentse a számítógépre.

    ![Ujjlenyomat másolása érték](common/copy-thumbprint.png)

1. A **Beállítás onit** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával az Onit hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **Onit**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-onit-sso"></a>Onit sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az Onit vállalati webhelyére rendszergazdaként.

2. A felső menüben kattintson az **Adminisztráció gombra.**
   
    ![Felügyelet](./media/onit-tutorial/IC791174.png "Adminisztráció")

3. Kattintson **a Vállalat szerkesztése gombra.**
   
    ![Edit Corporation](./media/onit-tutorial/IC791175.png "Edit Corporation")
   
4. Kattintson a **Biztonság** fülre.
    
    ![Vállalati adatok szerkesztése](./media/onit-tutorial/IC791176.png "Vállalati adatok szerkesztése")

5. A **Biztonság** lapon hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés](./media/onit-tutorial/IC791177.png "Egyszeri bejelentkezés")

    a. **Hitelesítési stratégiaként**válassza **az Egyszeri bejelentkezés és jelszó**lehetőséget.
    
    b. Az **Idp cél URL-címmezőbe** illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    c. Az **Idp-kijelentkezési URL-cím** szövegmezőbe illessze be a **kijelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    d. Az **Idp Tanúsítvány ujjlenyomat (SHA1)** szövegmezőbe illessze be a tanúsítvány **ujjlenyomatértékét,** amelyet az Azure Portalról másolt.

### <a name="create-onit-test-user"></a>Onit tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek az Onit-ba, ki kell építeni őket az Onit-ba. Onit esetén kiépítése manuális feladat.

**A felhasználói kiépítés konfigurálásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be az **Onit** vállalati webhelyére rendszergazdaként.

2. Kattintson az **Add User** (Felhasználó hozzáadása) elemre.

    ![Felügyelet](./media/onit-tutorial/IC791180.png "Adminisztráció")

3. A **Felhasználó hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Felhasználó hozzáadása](./media/onit-tutorial/IC791181.png "Felhasználó hozzáadása")

    a. Írja be egy érvényes Azure AD-fiók **nevét** és **e-mail címét,** amelyet a kapcsolódó szövegdobozokba szeretne beépíteni.

    b. Kattintson **a Létrehozás gombra.**

    > [!NOTE]
    > Az Azure Active Directory-fiók tulajdonosa kap egy e-mailt, és egy hivatkozást követ, hogy erősítse meg a fiók, mielőtt aktívvá válik.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelOnit csempéjére kattint, automatikusan be kell jelentkeznie arra az Onit-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az Onit szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)