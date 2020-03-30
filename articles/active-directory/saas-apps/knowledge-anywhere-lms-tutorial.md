---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Knowledge Anywhere LMS-szel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Knowledge Anywhere LMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5cfa07b1-a792-4f0a-8c6f-1a13142193d9
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f44324bbdd5af6675dfb4f5664cbbde2627edfec
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67098562"
---
# <a name="tutorial-integrate-knowledge-anywhere-lms-with-azure-active-directory"></a>Oktatóanyag: A tudás integrálása bárhol Az LMS szolgáltatásba az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Knowledge Anywhere LMS-t az Azure Active Directoryval (Azure AD). Ha integrálja a Knowledge Anywhere LMS-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-t, aki hozzáféréssel rendelkezik a Knowledge Anywhere LMS-hez.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve knowledge anywhere LMS az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Knowledge Anywhere LMS egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. Tudás Bárhol LMS támogatja **sp** kezdeményezett Egyszeri bejelentkezés és támogatja **a Just In Time** felhasználói kiépítés.

## <a name="adding-knowledge-anywhere-lms-from-the-gallery"></a>Tudás hozzáadása bárhol LMS a galériából

A Knowledge Anywhere LMS azure-beli AD-be való integrálásának konfigurálásához hozzá kell adnia a Tudás bárhol az LMS-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Tudás bárhol LMS** kifejezést a keresőmezőbe.
1. Válassza a **Tudás bárhol LMS** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Knowledge Anywhere LMS szolgáltatással egy **B. Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Knowledge Anywhere LMS-ben.

Az Azure AD SSO konfigurálásához és teszteléséhez a Knowledge Anywhere LMS szolgáltatással hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
2. **[Konfigurálja a Knowledge Anywhere LMS-t](#configure-knowledge-anywhere-lms)** az sso-beállítások alkalmazásoldali konfigurálásához.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B. Simonnal.
4. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b. Simon az Azure AD egyszeri bejelentkezést.
5. **[Hozzon létre tudás bárhol LMS teszt felhasználó,](#create-knowledge-anywhere-lms-test-user)** hogy egy megfelelője B. Simon a tudás bárhol LMS, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Knowledge Anywhere LMS** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    1. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<CLIENTNAME>.knowledgeanywhere.com/`

    1. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<CLIENTNAME>.knowledgeanywhere.com/SSO/SAML/Response.aspx?<IDPNAME>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-cím, amely később az oktatóanyag ban ismertetjük.

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<CLIENTNAME>.knowledgeanywhere.com/`

    > [!NOTE]
    > A bejelentkezési URL-érték nem valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [a Knowledge Anywhere LMS ügyféltámogatási csapatával,](https://knowany.zendesk.com/hc/en-us/articles/360000469034-SAML-2-0-Single-Sign-On-SSO-Set-Up-Guide) hogy megkapja ezt az értéket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Tudás beállítása bárhol LMS** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-knowledge-anywhere-lms"></a>Tudás konfigurálása bárhol LMS

1. A Knowledge Anywhere LMS konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzábővítményt a böngésző, kattintson a **Setup Knowledge Anywhere LMS** irányítja, hogy a Tudás Bárhol LMS alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatokat a Knowledge Anywhere LMS-be való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-7.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Knowledge Anywhere LMS-t, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Knowledge Anywhere LMS vállalati webhelyére, és hajtsa végre a következő lépéseket:

4. Jelölje ki a **Webhely** lapon.

    ![Tudás bárhol LMS konfiguráció](./media/knowledge-anywhere-lms-tutorial/configure1.png)

5. Jelölje ki az **SAML Beállítások** lapon.

    ![Tudás bárhol LMS konfiguráció](./media/knowledge-anywhere-lms-tutorial/configure2.png)

6. Kattintson az **Új hozzáadása gombra.**

    ![Tudás bárhol LMS konfiguráció](./media/knowledge-anywhere-lms-tutorial/configure3.png)

7. Az **SAML-beállítások hozzáadása/frissítése** lapon hajtsa végre az alábbi lépéseket:

    ![Tudás bárhol LMS konfiguráció](./media/knowledge-anywhere-lms-tutorial/configure4.png)

    a. Adja meg az IDP-nevet a szervezetszerint. Az ex:- `Azure`.

    b. Az **IDP-entitásazonosító** szövegdobozába illessze be **az Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    c. Az **IDP URL-cím** szövegmezőjébe illessze be **a bejelentkezési URL-értéket,** amelyet az Azure Portalról másolt.

    d. Nyissa meg a letöltött tanúsítványfájlt az Azure Portalról a jegyzettömbbe, másolja a tanúsítvány tartalmát, és illessze be **a Tanúsítvány** szövegmezőbe.

    e. A **Kijelentkezés URL-címmezőjébe** illessze be **a kijelentkezési URL-cím** értékét, amelyet az Azure Portalról másolt.

    f. Válassza ki a **Fő webhelylehetőséget** a **Domain**legördülő menüből.

    g. Másolja az **SP-entitás azonosítójának** értékét, és illessze be **az Azure** Portal **alapszintű SAML-konfiguráció** szakaszában az Azonosító mezőbe.

    h. Másolja az **SP-válasz(ACS) URL-értékét,** és illessze be a **Válasz URL-cím** mezőjébe az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában.

    i. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B. Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B. Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b. Simon azure egyszeri bejelentkezés t azáltal, hogy hozzáférést biztosít a Knowledge Anywhere LMS.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Tudás bárhol LMS**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B. Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-knowledge-anywhere-lms-test-user"></a>Tudás létrehozása bárhol LMS teszt felhasználó

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a Tudás bárhol LMS. Knowledge Anywhere LMS támogatja a just-in-time felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó már nem létezik a Knowledge Anywhere LMS,egy új jön létre a hitelesítés után.

### <a name="test-sso"></a>SSO tesztelése

Ha a Hozzáférési panelen kiválasztja a Tudás bárhol lms csempét, automatikusan be kell jelentkeznie a Tudás bárhol LMS-be, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)