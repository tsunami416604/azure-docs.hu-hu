---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Amazon Businessvel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Amazon Business között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b2597a66-afd9-4f11-b14b-646b597bb6c2
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d7ac085beaa85a7ddf3a6c3bfc61820e8e5a63ea
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68496909"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Oktatóanyag: Az Amazon Business integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Amazon Businesst az Azure Active Directoryval (Azure AD). Ha integrálja [az Amazon Businesst](https://www.amazon.com/b2b/info/amazon-business?layout=landing) az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-t, amely hozzáfér az Amazon Businessszolgáltatáshoz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve legyenek az Amazon Businessbe az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)egy hónapos ingyenes próbaverziót kaphat.
* Az Amazon Business egyszeri bejelentkezés (SSO) engedélyezve van előfizetés. Az [Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) oldalára lépve hozzon létre egy Amazon Business-fiókot.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálhatja és tesztelheti az Azure AD SSO-t egy meglévő Amazon Business-fiókban.

* Az Amazon Business támogatja az **SP-t és az IDP** által kezdeményezett SSO-t
* Az Amazon Business támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-amazon-business-from-the-gallery"></a>Amazon Business hozzáadása a galériából

Az Amazon Business Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az Amazon Business-t a galériából a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a galéria szakaszban** írja be az **Amazon Business** kifejezést a keresőmezőbe.
1. Válassza az **Amazon Business** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t az Amazon Business-lal egy **B.Simon**nevű tesztfelhasználó segítségével.

Az Azure AD SSO konfigurálásához és teszteléséhez az Amazon Business szolgáltatással hajtsa végre az alábbi építési lépéseket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az Amazon Business SSO-t](#configure-amazon-business-sso)** - az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Amazon Business teszt felhasználó](#create-amazon-business-test-user)** - hogy egy megfelelője B.Simon az Amazon Business, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **Amazon Business alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha **IDP** által kezdeményezett módban szeretne konfigurálni, hajtsa végre a következő lépéseket:

    1. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet az alábbi minták egyikének használatával:
    
       | | |
       |-|-|
       | `https://www.amazon.com`|
       | `https://www.amazon.co.jp`|
       | `https://www.amazon.de`|

    1. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet az alábbi minták egyikével:
    
       | | |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`|

       > [!NOTE]
       > A Válasz URL-értéke nem valós. Frissítse ezt az értéket a tényleges Válasz URL-címével. Az értéket `<idpid>` az Amazon Business SSO konfigurációs részéből kapja meg, amelyet a bemutató későbbi részében ismertetünk. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet:`https://www.amazon.com/`

1. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Az attribútumok szerkesztéséhez kattintson a **Felhasználói attribútumok & Jogcímek** szakasz **Szerkesztés** ikonjára.

    ![Attribútumok](media/amazon-business-tutorial/map-attribute3.png)

1. Az attribútumok szerkesztése és az attribútumok **névtérértékének** másolása a Jegyzettömbbe.

    ![Attribútumok](media/amazon-business-tutorial/map-attribute4.png)

1. A fentiek mellett az Amazon Business alkalmazás arra számít, hogy még néhány attribútumot vissza kell adni az SAML válaszban. A **Csoportjogcímek** párbeszédpanel **Felhasználói attribútumok & jogcímek** csoportjában hajtsa végre az alábbi lépéseket:

    a. Kattintson a **jogcímben visszaadott csoportok**melletti **tollra.**

    ![image](./media/amazon-business-tutorial/config04.png)

    ![image](./media/amazon-business-tutorial/config05.png)

    b. Válassza a minden **csoport lehetőséget** a rádiólistából.

    c. Válassza a **Csoportazonosító t** **forrásattribútumként.**

    d. Jelölje **be a Csoportjogcím nevének testreszabása** jelölőnégyzetet, és írja be a csoport nevét a szervezeti követelménynek megfelelően.

    e. Kattintson a **Mentés** gombra.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **Amazon Business beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Az Amazon Business SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az Amazon Business vállalati webhelyére rendszergazdaként.

1. Kattintson a **Felhasználói profilra,** és válassza az **Üzleti beállítások lehetőséget.**

    ![Felhasználói profil](media/amazon-business-tutorial/user-profile.png)

1. A **Rendszerintegrációk** varázslóban válassza **az Egyszeri bejelentkezés (SSO)** lehetőséget.

    ![Egyszeri bejelentkezés (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. Az **SSO beállítása** varázslóban válassza ki a szolgáltatót a szervezeti követelményeknek megfelelően, és kattintson a **Tovább**gombra.

    ![Alapértelmezett csoport](media/amazon-business-tutorial/default-group1.png)

1. Az **Új felhasználói fiók alapértelmezései** varázslóban válassza az **Alapértelmezett csoportot,** majd a Szervezet felhasználói szerepkörének megfelelő **alapértelmezett vásárlási szerepkört,** majd kattintson a **Tovább**gombra.

    ![Alapértelmezett csoport](media/amazon-business-tutorial/dafault-group2.png)

1. A **Metaadatfájl feltöltése** varázslóban kattintson a **Tallózás** gombra az Azure portalról letöltött **metaadat-XML-fájl** feltöltéséhez, majd kattintson a **Feltöltés gombra.**

    ![Kapcsolati adatok](media/amazon-business-tutorial/connection-data1.png)

1. A letöltött metaadatfájl feltöltése után a **Kapcsolatadatok** szakasz mezői automatikusan feltöltődnek. Ezt követően kattintson a **Tovább gombra.**

    ![Kapcsolati adatok](media/amazon-business-tutorial/connection-data2.png)

1. Az **Attribútumutasítás feltöltése varázslóban** kattintson a **Kihagyás**gombra.

    ![Attribútumok](media/amazon-business-tutorial/map-attribute1.png)

1. Az **Attribútumleképezés varázslóban** adja hozzá a követelménymezőket a **+ Mező hozzáadása** lehetőségre kattintva. Adja hozzá az attribútumértékeket, beleértve a névteret is, amelyet az Azure Portal **Felhasználói attribútumok & jogcímek** szakaszából másolt az **SAML Attribútumnév** mezőbe, majd kattintson a **Tovább**gombra.

    ![Attribútumok](media/amazon-business-tutorial/map-attribute2.png)

1. Az **Amazon-kapcsolatadatok** varázslón kattintson a **Tovább**gombra.

    ![Kapcsolat](media/amazon-business-tutorial/amazon-connect.png)

1. Ellenőrizze a konfigurált lépések **állapotát,** és kattintson a **Tesztelés megkezdése**gombra.

    ![Kapcsolat](media/amazon-business-tutorial/sso-connection1.png)

1. Az **SSO-kapcsolat tesztelése** varázslóban kattintson a **Teszt**gombra.

    ![Kapcsolat](media/amazon-business-tutorial/sso-connection2.png)

1. Az **IDP által kezdeményezett URL-cím** varázsló, mielőtt az **Aktiválás gombra kattintana,** másolja az **ürességhez** rendelt értéket, és illessze be az **üres** paraméterbe az Azure Portal **alapszintű SAML-konfigurációja** **szakaszválasz URL-címében.**

    ![Kapcsolat](media/amazon-business-tutorial/sso-connection3.png)

1. A **Készen áll az aktív SSO varázslóra való váltásra?** **I have fully tested SSO and am ready to go live** **Switch to active**

    ![Kapcsolat](media/amazon-business-tutorial/sso-connection4.png)

1. Végül az **SSO-kapcsolat részletei** szakaszban az **Állapot** **aktívként**jelenik meg.

    ![Kapcsolat](media/amazon-business-tutorial/sso-connection5.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

> [!NOTE]
> A rendszergazdáknak szükség esetén létre kell hozniuk a tesztfelhasználókat a bérlőben. Az alábbi lépések bemutatják, hogyan hozhat létre tesztfelhasználót.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Hozzon létre egy Azure AD-biztonsági csoportot az Azure Portalon

1. Kattintson az **Azure Active Directory > az Összes csoport elemre.**

    ![Azure AD-biztonsági csoport létrehozása](./media/amazon-business-tutorial/all-groups-tab.png)

1. Kattintson **az Új csoport gombra:**

    ![Azure AD-biztonsági csoport létrehozása](./media/amazon-business-tutorial/new-group-tab.png)

1. Adja meg a **Csoport típusát**, A **csoport nevét**, a Csoport **leírását**, **a tagság típusát.** Kattintson a nyílra, hogy válassza ki a tagokat, majd keresse meg, vagy kattintson a tag szeretne hozzáadni a csoporthoz. Kattintson a **Kijelölés gombra** a kijelölt tagok hozzáadásához, majd kattintson a **Létrehozás gombra.**

    ![Azure AD-biztonsági csoport létrehozása](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t az Amazon Business hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **Amazon Business**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

    >[!NOTE]
    > Ha nem rendeli hozzá a felhasználókat az Azure AD-ben, a következő hibaüzenet jelenik meg.

    ![A Felhasználó hozzáadása hivatkozás](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Rendelje hozzá az Azure AD biztonsági csoportot az Azure Portalon

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd az Amazon **Business**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza az **Amazon Business**lehetőséget.

    ![Az Amazon Business link az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása gombra**.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. Keresse meg a használni kívánt biztonsági csoportot, majd kattintson a csoportra a Tagok kiválasztása szakaszhoz való hozzáadásához. Kattintson **a Kijelölés**gombra, majd a **Hozzárendelés gombra.**

    ![Keresés biztonsági csoportja](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Ellenőrizze a menüsorban található értesítéseket, amelyek értesítést kapnak arról, hogy a csoport sikeresen hozzá lett rendelve az Azure Portalon lévő Enterprise alkalmazáshoz.

### <a name="create-amazon-business-test-user"></a>Amazon Business tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználó jön létre az Amazon Business alkalmazásban. Az Amazon Business támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik az Amazon Business, egy új jön létre a hitelesítés után.

### <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha az Amazon Business csempére kattint a Hozzáférési panelen, automatikusan be kell jelentkeznie az Amazon Business be, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
