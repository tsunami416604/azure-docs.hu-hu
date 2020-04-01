---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az AskYourTeam-el | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az AskYourTeam között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1cef6764-de54-4920-b0ad-e560c214c72e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/28/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 403f39de1b7d226d9feeb33388c32f63271fdcd6
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78968527"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-askyourteam"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az AskYourTeam-el

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az AskYourTeam-et az Azure Active Directoryval (Azure AD). Ha integrálja az AskYourTeam-et az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér askyourteam.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve AskYourTeam az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* AskYourTeam egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* AskYourTeam támogatja **sp és IDP** kezdeményezett SSO.
* Az AskYourTeam konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáféréstől terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-askyourteam-from-the-gallery"></a>AskYourTeam hozzáadása a galériából

Az AskYourTeam azure AD-be való integrációjának konfigurálásához hozzá kell adnia az AskYourTeam-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **gyűjtemény hozzáadásszakaszában** írja be az **AskYourTeam** kifejezést a keresőmezőbe.
1. Válassza **az AskYourTeam** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-askyourteam"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése az AskYourTeam számára

Konfigurálja és tesztelje az Azure AD SSO-t az AskYourTeam segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó AskYourTeam között.

Az Azure AD SSO konfigurálásához és teszteléséhez az AskYourTeam segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja AskYourTeam SSO](#configure-askyourteam-sso)** -konfigurálása az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
    * **[AskYourTeam tesztfelhasználó létrehozása](#create-askyourteam-test-user)** – b.Simon megfelelője az AskYourTeam-ben, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **AskYourTeam** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<COMPANY>.app.askyourteam.com/users/auth/saml/callback`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<COMPANY>.app.askyourteam.com/login`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-cím és a bejelentkezési URL-értékeket, amelyek később az oktatóanyagban ismertetjük.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Az **AskYourTeam beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t add hozzáférést AskYourTeam hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **AskYourTeam lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-askyourteam-sso"></a>AskYourTeam SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként az AskYourTeam webhelyére.

1. Kattintson a **Saját szervezet gombra.**

    ![AskYourTeam konfiguráció](./media/askyourteam-tutorial/user1.png)

1. Kattintson az **Integrációk gombra.**

    ![AskYourTeam konfiguráció](./media/askyourteam-tutorial/configure1.png)

1. Kattintson a **Beállítások szerkesztése gombra.**

    ![AskYourTeam konfiguráció](./media/askyourteam-tutorial/configure2.png)

1. Az **Egyszeri bejelentkezés integráció szerkesztése** lapon hajtsa végre az alábbi lépéseket: 

    ![AskYourTeam konfiguráció](./media/askyourteam-tutorial/configure3.png)

    a. Az **SAML Single Sign-On Service URL-címszövegbe** illessze be a **bejelentkezési URL-cím,** amely et az Azure Portalról másolt.

    b. Az **SAML-entitásazonosító** szövegdobozába illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    c. A **Kijelentkezési URL-cím** szövegmezőbe illessze be a **kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    d. Nyissa meg a letöltött **tanúsítványt (Base64)** az Azure Portalról a Jegyzettömbbe, és illessze be a tartalmat az **SAML aláíró tanúsítvány – Base64** szövegdobozba.

    > [!NOTE]
    > Másik lehetőségként feltöltheti az **összevonási metaadat-XML-fájlt** is, ha a **Fájl kiválasztása** gombra kattint.

    e. Másolás **válasz URL-cím (helyességi feltétel fogyasztói szolgáltatás URL-címe)** érték, illessze be ezt az értéket a **Válasz URL-cím** mezőjébe az Azure Portal **alapszintű SAML konfigurációja** szakaszban.

    f. Sign **on URL-érték** másolása, illessze be ezt az értéket az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában a **Bejelentkezés az URL-címen** mezőbe.

    g. Kattintson a **Mentés** gombra.

### <a name="create-askyourteam-test-user"></a>AskYourTeam tesztfelhasználó létrehozása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként az AskYourTeam webhelyére.

1. Kattintson a **Saját szervezet gombra.**

    ![AskYourTeam konfiguráció](./media/askyourteam-tutorial/user1.png)

1. Kattintson **a Felhasználók** gombra, és válassza az **Új felhasználó lehetőséget.**

    ![AskYourTeam konfiguráció](./media/askyourteam-tutorial/user2.png)

1. Az **Új felhasználó** csoportban hajtsa végre az alábbi lépéseket:

    ![AskYourTeam konfiguráció](./media/askyourteam-tutorial/user3.png)

    1. Az **Utónév** mezőbe írja be a felhasználó keresztnevét.

    1. A **Vezetéknév** mezőbe írja be a felhasználó vezetéknevét.

    1. Az **E-mail** mezőbe írja be a B.Simon@contoso.comfelhasználó e-mail címét, például .

    1. Válassza ki a **szerepkör** a felhasználó szerint a szervezet követelménye.

    1. Kattintson a **Mentés** gombra.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen az AskYourTeam csempére kattint, automatikusan be kell jelentkeznie az AskYourTeam-be, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az AskYourTeam szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
