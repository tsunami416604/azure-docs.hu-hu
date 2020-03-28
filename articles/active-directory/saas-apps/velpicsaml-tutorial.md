---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Velpic SAML-lel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Velpic SAML között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28acce3e-22a0-4a37-8b66-6e518d777350
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/01/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f699e2244cde53200a9885602bd20c899c0ce4cd
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72241562"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-velpic-saml"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Velpic SAML-lel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Velpic SAML-t az Azure Active Directoryval (Azure AD). Ha integrálja a Velpic SAML-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Velpic SAML.Control in Azure AD who has access to Velpic SAML.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve velpic SAML az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Velpic SAML egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Velpic SAML támogatja **SP** kezdeményezett SSO

## <a name="adding-velpic-saml-from-the-gallery"></a>Velpic SAML hozzáadása a galériából

A Velpic SAML azure AD-be való integrálásának konfigurálásához hozzá kell adnia a velpic SAML-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Velpic SAML kifejezést** a keresőmezőbe.
1. Válassza **a Velpic SAML** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.    

## <a name="configure-and-test-azure-ad-single-sign-on-for-velpic-saml"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a Velpic SAML-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Velpic SAML segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a velpic SAML kapcsolódó felhasználója között.

Az Azure AD SSO konfigurálásához és teszteléséhez a Velpic SAML segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Velpic SAML Egyszeri bejelentkezést](#configure-velpic-saml-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre velpic SAML teszt felhasználó](#create-velpic-saml-test-user)** - a B.Simon megfelelője a Velpic SAML, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Velpic SAML** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza ki **az egyszeri bejelentkezést.**
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<sub-domain>.velpicsaml.net`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://auth.velpic.com/saml/v2/<entity-id>/login`

    > [!NOTE]
    > Kérjük, vegye figyelembe, hogy a Bejelentkezési URL-címet a Velpic SAML csapat adja meg, és az azonosító érték elérhető lesz, amikor konfigurálja az SSO plugint a Velpic SAML oldalon. Meg kell másolni, hogy az érték velpic SAML alkalmazás oldalon, és illessze be ide.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Velpic SAML beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Velpic SAML használatával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Velpic SAML lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-velpic-saml-sso"></a>Velpic SAML sso konfigurálása

1. A Velpic SAML konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Hozzáadása után kiterjesztés a böngésző, kattintson a **Beállítása Velpic SAML** irányítja, hogy a Velpic SAML alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatokat a Velpic SAML-be való bejelentkezéshez. A böngészőbővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-8.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Velpic SAML-t, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Velpic SAML vállalati webhelyére, és hajtsa végre a következő lépéseket:

4. Kattintson a **Kezelés** fülre, és lépjen **az Integráció** szakaszra, ahol a **Beépülő modulok gombra** kell kattintania a bejelentkezés új bővítményének létrehozásához.

    ![Plugin](./media/velpicsaml-tutorial/velpic_1.png)

5. Kattintson a **"Plugin hozzáadása"** gombra.
    
    ![Plugin](./media/velpicsaml-tutorial/velpic_2.png)

6. Kattintson az **SAML** csempére a Plugin hozzáadása oldalon.
    
    ![Plugin](./media/velpicsaml-tutorial/velpic_3.png)

7. Írja be az új SAML beépülő modul nevét, és kattintson a **"Hozzáadás"** gombra.

    ![Plugin](./media/velpicsaml-tutorial/velpic_4.png)

8. Adja meg a részleteket az alábbiak szerint:

    ![Plugin](./media/velpicsaml-tutorial/velpic_5.png)

    a. A **Név** mezőbe írja be az SAML beépülő modul nevét.

    b. A **Kiállító URL-címmezőjébe** illessze be az **Azure AD-azonosítót,** amelyet az Azure **Portal Bejelentkezés konfigurálása** ablakából másolt.

    c. A **szolgáltató metaadat-konfigurációfeltöltése** a metaadat-XML-fájlt, amelyet az Azure Portalról töltött le.

    d. Az **"Automatikus új felhasználók létrehozása"** jelölőnégyzet engedélyezésével is engedélyezheti az SAML-t az időkiépítéshez. Ha a felhasználó nem létezik a Velpic, és ez a jelző nincs engedélyezve, az Azure-ból való bejelentkezés sikertelen lesz. Ha a jelző engedélyezve van, a felhasználó automatikusan kiépíti a Velpic-be a bejelentkezés időpontjában. 

    e. Másolja az egyetlen bejelentkezést az **URL-címre** a szövegmezőből, és illessze be az Azure Portalon.
    
    f. Kattintson a **Mentés** gombra.

### <a name="create-velpic-saml-test-user"></a>Velpic SAML tesztfelhasználó létrehozása

Ez a lépés általában nem szükséges, mivel az alkalmazás támogatja a közvetlenül az időben a felhasználó kiépítése. Ha az automatikus felhasználói kiépítés nincs engedélyezve, akkor a manuális felhasználólétrehozása az alábbiak szerint végezhető el.

Jelentkezzen be rendszergazdaként a Velpic SAML vállalati webhelyére, és hajtsa végre a következő lépéseket:
    
1. Kattintson a Kezelés fülre, és lépjen a Felhasználók szakaszra, majd az Új gombra a felhasználók hozzáadásához.

    ![felhasználó hozzáadása](./media/velpicsaml-tutorial/velpic_7.png)

2. Az **"Új felhasználó létrehozása"** párbeszédpanelen hajtsa végre a következő lépéseket.

    ![felhasználó!](./media/velpicsaml-tutorial/velpic_8.png)
    
    a. Az **Utónév** mezőbe írja be a B keresztnevét.

    b. A **Vezetéknév** mezőbe írja be a Simon vezetéknevét.

    c. A **Felhasználónév** mezőbe írja be a B.Simon felhasználónevét.

    d. Az **E-mail** mezőbe írja be B.Simon@contoso.com a fiók e-mail címét.

    e. A többi információ nem kötelező, szükség esetén kitöltheti.
    
    f. Kattintson **a MENTÉS gombra.**

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

1. Ha a Hozzáférési panelen a Velpic SAML csempére kattint, a Velpic SAML alkalmazás bejelentkezési lapját kapja meg. A bejelentkezési lapon megjelenik a **"Bejelentkezés az Azure AD-vel"** gomb.

    ![Plugin](./media/velpicsaml-tutorial/velpic_6.png)

1. Kattintson a **"Bejelentkezés az Azure AD-vel"** gombra az Azure AD-fiókjával történő bejelentkezéshez.

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Velpic SAML-t az Azure AD-vel](https://aad.portal.azure.com/)

