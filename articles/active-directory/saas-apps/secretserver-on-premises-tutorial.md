---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a titkos kiszolgálóval (helyszíni) | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a titkos kiszolgáló (helyszíni) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4926fc1833cc14b2ad81a01e230a5c3c37ba6ab3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68880131"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>Oktatóanyag: Titkos kiszolgáló (helyszíni) integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a titkos kiszolgálót (helyszíni) az Azure Active Directoryval (Azure AD). Ha integrálja a Titkos kiszolgálót (helyszíni) az Azure AD-vel, a következőket teheti:

* Az Azure AD- ban, aki hozzáfér a titkos kiszolgálóhoz (helyszíni).
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve titkos kiszolgálóra (helyszíni) az Azure AD-fiókokkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Titkos kiszolgáló (Helyszíni) egyszeri bejelentkezés (SSO) engedélyezve van az előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Titkos kiszolgáló (helyszíni) támogatja az SP és **az IDP** által kezdeményezett egyszeri szolgáltatót

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Titkos kiszolgáló (helyszíni) hozzáadása a gyűjteményből

A titkos kiszolgáló (helyszíni) azure-ad-ba való integrációjának konfigurálásához hozzá kell adnia titkos kiszolgálót (helyszíni) a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **titkos kiszolgálót (helyszíni)** a keresőmezőbe.
1. Válassza a **Titkos kiszolgáló (helyszíni)** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD egyszeri szolgáltatást titkos kiszolgálóval (helyszíni) egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az egyszeri bejelentkezés működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó titkos kiszolgálón (helyszíni).

Az Azure AD egyszeri szolgáltatás titkos kiszolgálóval (helyszíni) konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[A Secret Server (Helyszíni) egyszeri bejelentkezés konfigurálása](#configure-secret-server-on-premises-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre titkos kiszolgáló (helyszíni) teszt felhasználó](#create-secret-server-on-premises-test-user)** – a B.Simon titkos kiszolgáló (helyszíni) megfelelője, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Titkos kiszolgáló (helyszíni) alkalmazásintegrációs** lapon keresse meg a Kezelés **szakaszt,** és válassza az **Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írja be példaként a felhasználó által választott értéket:`https://secretserveronpremises.azure`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > A fenti entitásazonosító csak egy példa, és szabadon választhat olyan egyedi értéket, amely azonosítja a titkos kiszolgáló példányát az Azure AD-ben. Ezt az entitásazonosítót el kell küldenie [a titkos kiszolgáló (helyszíni) ügyféltámogatási csapatának,](https://thycotic.force.com/support/s/) és ők az ő oldalukon konfigurálják. További részletekért kérjük, olvassa el [ezt a cikket](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<SecretServerURL>/login.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-címmel és bejelentkezési URL-címmel. Lépjen kapcsolatba [a titkos kiszolgáló (helyszíni) ügyféltámogatási csapatával,](https://thycotic.force.com/support/s/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![Aláírási beállítások](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. Válassza **az Aláírási lehetőség lehetőséget** **aláírási SAML válaszként és állításként.**

    ![Aláírási beállítások](./media/secretserver-on-premises-tutorial/signing-option.png)

1. A **Titkos kiszolgáló beállítása (helyszíni)** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-secret-server-on-premises-sso"></a>Titkos kiszolgáló (helyszíni) egyszeri szolgáltatás sso konfigurálása

Az egyszeri bejelentkezés konfigurálásához a **titkos kiszolgáló (helyszíni)** oldalán el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-címeket az Azure Portalról a [titkos kiszolgáló (helyszíni) támogatási csapatának.](https://thycotic.force.com/support/s/) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a titkos kiszolgáló (helyszíni) engedélyezésével.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Titkos kiszolgáló (helyszíni)** lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-secret-server-on-premises-test-user"></a>Titkos kiszolgáló (helyszíni) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a titkos kiszolgálón (helyszíni). Együttműködve [secret server (helyszíni) támogatási csapat](https://thycotic.force.com/support/s/) a felhasználók hozzáadása a titkos kiszolgáló (helyszíni) platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

### <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a hozzáférési panelen a Titkos kiszolgáló (helyszíni) csempére kattint, automatikusan be kell jelentkeznie arra a titkos kiszolgálóra (helyszíni), amelyhez az egyszeri bejelentkezést beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)