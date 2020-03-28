---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Carbonite Endpoint Backup szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Carbonite Endpoint Backup között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b78091f1-2f06-4c2c-8541-72aee0b5a322
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e078cb7daa787b9fe5e8bc996b36f0fef198f41c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68879673"
---
# <a name="tutorial-integrate-carbonite-endpoint-backup-with-azure-active-directory"></a>Oktatóanyag: A carbonite végpontok biztonsági mentésének integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Carbonite Endpoint Backup szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja a Carbonite Endpoint Backup szolgáltatást az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Carbonite Endpoint backup.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve carbonite endpoint backup az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Carbonite Endpoint Backup egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A carbonite endpoint backup támogatja az **SP-t és az IDP** által kezdeményezett sso-t

## <a name="adding-carbonite-endpoint-backup-from-the-gallery"></a>Carbonite végpontbiztonsági mentés hozzáadása a galériából

A Carbonite Endpoint Backup azure-beli Biztonsági mentés integrálásának konfigurálásához hozzá kell adnia a karbonitvégpont biztonsági mentést a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Karbonit végpont biztonsági mentés** kifejezést a keresőmezőbe.
1. Válassza **a Carbonite Endpoint Backup lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Carbonite endpoint backup használatával egy **B.Simon**nevű tesztfelhasználó használatával. Az SSO működéséhez létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Carbonite Endpoint Backup.For SSO to work, you need to establish a link relationship between a Azure AD user and the related user in Carbonite Endpoint Backup.

Az Azure AD SSO konfigurálásához és teszteléséhez a Carbonite Endpoint Backup segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a Carbonite Endpoint Backup SSO-t](#configure-carbonite-endpoint-backup-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre carbonite végpontbiztonsági mentési teszt felhasználó](#create-carbonite-endpoint-backup-test-user)** – a B.Simon megfelelője a Carbonite Endpoint Backup, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Carbonite Endpoint Backup** alkalmazás integrációs lapján keresse meg a **Kezelés szakaszt,** és válassza az **Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írja be az alábbi URL-címek egyikét:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com`|
    | `https://red-apac.mysecuredatavault.com`|
    | `https://red-fr.mysecuredatavault.com`|
    | `https://red-emea.mysecuredatavault.com`|
    | `https://kamino.mysecuredatavault.com`|
    | | |

    b. A **Válasz URL-címmezőjébe** írja be az alábbi URL-címek egyikét:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-apac.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-fr.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | `https://red-emea.mysecuredatavault.com/AssertionConsumerService.aspx`|
    | | |

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írja be az alábbi URL-címek egyikét:

    | | |
    |-|-|
    | `https://red-us.mysecuredatavault.com/`|
    | `https://red-apac.mysecuredatavault.com/`|
    | `https://red-fr.mysecuredatavault.com/`|
    | `https://red-emea.mysecuredatavault.com/`|
    | | |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Carbonite endpoint biztonsági mentésbeállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-carbonite-endpoint-backup-sso"></a>Carbonite végpontbiztonsági biztonsági mentési biztonsági rendszerének konfigurálása

1. A Carbonite Endpoint Backup konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzátette a bővítményt a böngészőhöz, kattintson a **Setup Carbonite Endpoint Backup gombra,** amely a Carbonite Endpoint Backup alkalmazáshoz irányítja. Innen adja meg a rendszergazdai hitelesítő adatokat a Carbonite Endpoint Backup-ba való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-7.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Carbonite Endpoint Backup biztonsági mentést, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Carbonite Endpoint Backup vállalati webhelyére, és hajtsa végre a következő lépéseket:

4. Kattintson a **Vállalat** elemre a bal oldali ablaktáblából.

    ![Carbonite végpontbiztonsági mentés konfigurációja ](media/carbonite-endpoint-backup-tutorial/configure1.png)

5. Kattintson az **Egyszeri bejelentkezés gombra.**

    ![Carbonite végpontbiztonsági mentés konfigurációja ](media/carbonite-endpoint-backup-tutorial/configure2.png)

6. Kattintson az **Engedélyezés,** majd a **Beállítások szerkesztése** gombra a konfiguráláshoz.

    ![Carbonite végpontbiztonsági mentés konfigurációja ](media/carbonite-endpoint-backup-tutorial/configure3.png)

7. Az **Egyszeri bejelentkezési** beállítások lapon hajtsa végre az alábbi lépéseket:

    ![Carbonite végpontbiztonsági mentés konfigurációja ](media/carbonite-endpoint-backup-tutorial/configure4.png)

    1. Az **identitásszolgáltató nevének** szövegdobozába illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    1. Az **identitásszolgáltató URL-címmezőjébe** illessze be a **bejelentkezési URL-értéket,** amelyet az Azure Portalról másolt.

    1. Kattintson **a Fájl kiválasztása** gombra a letöltött **tanúsítvány(Base64)** fájl azure portalról való feltöltéséhez.

    1. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Carbonite Endpoint backup eléréséhez való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Carbonite Endpoint Backup lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-carbonite-endpoint-backup-test-user"></a>Carbonite végpontbiztonsági mentési tesztfelhasználójának létrehozása

1. Egy másik böngészőablakban jelentkezzen be a Carbonite Endpoint Backup vállalati webhelyére rendszergazdaként.

1. Kattintson a bal oldali ablaktáblában a **Felhasználók** elemre, majd a **Felhasználó hozzáadása**parancsra.

    ![Felhasználó hozzáadása a Carbonite végpontbiztonsági mentésben](media/carbonite-endpoint-backup-tutorial/adduser1.png)

1. A **Felhasználó hozzáadása** lapon hajtsa végre az alábbi lépéseket:

    ![Felhasználó hozzáadása a Carbonite végpontbiztonsági mentésben](media/carbonite-endpoint-backup-tutorial/adduser2.png)

    1. Adja meg az **E-mail**, **Utónév**, **Vezetéknév** a felhasználó, és adja meg a szükséges engedélyeket a felhasználó nak a szervezeti követelményeknek megfelelően.

    1. Kattintson **a Felhasználó hozzáadása**gombra.

### <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen a Karbonit végpontbiztonsági mentés csempére kattint, automatikusan be kell jelentkeznie a Carbonite Endpoint Backup-ba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)