---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a CyberArk SAML-hitelesítéssel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a CyberArk SAML-hitelesítés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 58d8a0be-5f1b-4680-bbcb-2975e5c57014
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 206ab8931e80628c2d92404198240b074fa4c849
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72240847"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cyberark-saml-authentication"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a CyberArk SAML-hitelesítéssel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a CyberArk SAML-hitelesítést az Azure Active Directoryval (Azure AD). Ha integrálja a CyberArk SAML-hitelesítést az Azure AD-vel, a következőket teheti:

* Az Azure AD- ban, aki hozzáfér a CyberArk SAML-hitelesítéshez.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve CyberArk SAML hitelesítés az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* CyberArk SAML hitelesítés egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A CyberArk SAML authentication támogatja az **SP és az IDP** által kezdeményezett sso-t

## <a name="adding-cyberark-saml-authentication-from-the-gallery"></a>CyberArk SAML-hitelesítés hozzáadása a galériából

A CyberArk SAML-hitelesítés Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a CyberArk SAML-hitelesítést a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **CyberArk SAML hitelesítés kifejezést** a keresőmezőbe.
1. Válassza a **CyberArk SAML hitelesítés** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cyberark-saml-authentication"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése a CyberArk SAML-hitelesítéshez

Konfigurálja és tesztelje az Azure AD SSO-t a CyberArk SAML-hitelesítéssel egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó cyberark SAML hitelesítés.

Az Azure AD SSO konfigurálásához és teszteléséhez a CyberArk SAML-hitelesítéssel hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a CyberArk SAML hitelesítési egyszeri bejelentkezési egyszeri bejelentkezési](#configure-cyberark-saml-authentication-sso)** beállításait az alkalmazás oldalon.
    * **[Hozzon létre CyberArk SAML hitelesítésteszt-felhasználót](#create-cyberark-saml-authentication-test-user)** – hogy a Felhasználó Azure AD-alapú megjelenítéséhez kapcsolódó B.Simon megfelelője legyen a CyberArk SAML-hitelesítésben.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **CyberArk SAML hitelesítésalkalmazás-integrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<PVWA DNS or IP>/passwordvault/api/auth/saml/logon`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<PVWA DNS or IP>/PasswordVault/v10/logon/saml`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-címmel és bejelentkezési URL-címmel. Lépjen kapcsolatba [a CyberArk SAML authentication ügyfél támogatási csapatával,](mailto:bizdevtech@cyberark.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **CyberArk SAML-hitelesítés beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a CyberArk SAML-hitelesítés engedélyezésével.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **CyberArk SAML hitelesítés lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-cyberark-saml-authentication-sso"></a>CyberArk SAML hitelesítési sso konfigurálása

A **CyberArk SAML hitelesítési** oldalán történő egyszeri bejelentkezés konfigurálásához el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-címeket az Azure Portalról a [CyberArk SAML hitelesítéstámogatási csapatának.](mailto:bizdevtech@cyberark.com) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-cyberark-saml-authentication-test-user"></a>CyberArk SAML hitelesítési tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználót hoz létre a CyberArk SAML hitelesítésben. Együttműködve [CyberArk SAML hitelesítés támogatási csapat](mailto:bizdevtech@cyberark.com) ával a felhasználók hozzáadása a CyberArk SAML hitelesítési platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a CyberArk SAML hitelesítés csempére kattint, automatikusan be kell jelentkeznie a CyberArk SAML hitelesítésbe, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a CyberArk SAML-hitelesítést az Azure AD-vel](https://aad.portal.azure.com/)