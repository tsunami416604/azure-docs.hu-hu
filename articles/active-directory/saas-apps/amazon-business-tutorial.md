---
title: 'Oktatóanyag: Azure Active Directory integráció az Amazon Businessrel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az Amazon Business között.
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
ms.sourcegitcommit: 5604661655840c428045eb837fb8704dca811da0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2019
ms.locfileid: "68496909"
---
# <a name="tutorial-integrate-amazon-business-with-azure-active-directory"></a>Oktatóanyag: Az Amazon Business integrálása a Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az Amazon businesst Azure Active Directory (Azure AD) használatával. Ha integrálja az [Amazon businesst](https://www.amazon.com/b2b/info/amazon-business?layout=landing) az Azure ad-vel, a következőket teheti:

* Az Amazon Businesshez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az Amazon businessbe az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)kérhet egy hónapos ingyenes próbaverziót.
* Amazon Business egyszeri bejelentkezés (SSO) engedélyezett előfizetése. Nyissa meg az [Amazon Business](https://www.amazon.com/business/register/org/landing?ref_=ab_reg_mlp) oldalt egy Amazon Business-fiók létrehozásához.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy meglévő Amazon Business-fiókban konfigurálja és teszteli az Azure AD SSO-t.

* Az Amazon Business **az SP és a identitásszolgáltató** által kezdeményezett SSO-t támogatja
* Az Amazon Business **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-amazon-business-from-the-gallery"></a>Az Amazon Business hozzáadása a katalógusból

Az Amazon Business az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Amazon businesst a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **Amazon Business** kifejezést a keresőmezőbe.
1. Válassza az **Amazon Business** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálja és tesztelje az Azure AD SSO-t az Amazon Business használatával egy **B. Simon**nevű teszt felhasználó segítségével.

Az Azure AD SSO az Amazon Businessrel való konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. Az **[Amazon Business SSO konfigurálása](#configure-amazon-business-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre egy Amazon Business test User](#create-amazon-business-test-user)** -t, amely az Amazon Businessben található, a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-ügyféllel rendelkezik.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **Amazon Business** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha az **alapszintű SAML** -konfigurációt **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    1. Az **azonosító (Entity ID)** szövegmezőben írja be az URL-címet a következő minták egyikének használatával:
    
       | | |
       |-|-|
       | `https://www.amazon.com`|
       | `https://www.amazon.co.jp`|
       | `https://www.amazon.de`|

    1. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minták egyikének használatával:
    
       | | |
       |-|-|
       | `https://www.amazon.com/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.co.jp/bb/feature/sso/action/3p_redirect?idpid={idpid}`|
       | `https://www.amazon.de/bb/feature/sso/action/3p_redirect?idpid={idpid}`|

       > [!NOTE]
       > A válasz URL-cím értéke nem valódi. Frissítse ezt az értéket a tényleges válasz URL-címével. Megjelenik az Amazon Business `<idpid>` SSO konfigurációs szakaszának értéke, amelyet az oktatóanyag későbbi részében talál. Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be a következő URL-címet:`https://www.amazon.com/`

1. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Az attribútumok szerkesztéséhez kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok &** jogcímek szakaszban.

    ![Attribútumok](media/amazon-business-tutorial/map-attribute3.png)

1. Szerkessze az attribútumokat, és másolja a **névtér** értékét a Jegyzettömbbe.

    ![Attribútumok](media/amazon-business-tutorial/map-attribute4.png)

1. A fentieken kívül az Amazon Business Application néhány további attribútumot vár az SAML-válaszba való visszatéréshez. A **csoport** jogcímek párbeszédpanel **felhasználói attribútumok &** jogcímek szakaszában hajtsa végre a következő lépéseket:

    a. Kattintson a **kérelemben visszaadott csoportok**melletti **tollra** .

    ![image](./media/amazon-business-tutorial/config04.png)

    ![image](./media/amazon-business-tutorial/config05.png)

    b. Válassza az **összes csoport** lehetőséget a választógombok listájáról.

    c. Válassza ki a **csoport azonosítóját** **forrás attribútumként**.

    d. Jelölje be a csoportjogcím **nevének testreszabása** jelölőnégyzetet, és adja meg a csoport nevét a szervezet követelményeinek megfelelően.

    e. Kattintson a **Save** (Mentés) gombra.

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **metaadatok XML** -fájlját, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. Az **Amazon Business beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-amazon-business-sso"></a>Az Amazon Business SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az Amazon Business Company webhelyére rendszergazdaként.

1. Kattintson a **felhasználói profilra** , és válassza az **üzleti beállítások**lehetőséget.

    ![Felhasználói profil](media/amazon-business-tutorial/user-profile.png)

1. A rendszerintegrációk varázslóban válassza az **egyszeri bejelentkezés (SSO)** lehetőséget.

    ![Egyszeri bejelentkezés (SSO)](media/amazon-business-tutorial/sso-settings.png)

1. Az **egyszeri bejelentkezés beállítása** varázslóban válassza ki a szolgáltatót a szervezeti követelményeknek megfelelően, és kattintson a **tovább**gombra.

    ![Alapértelmezett csoport](media/amazon-business-tutorial/default-group1.png)

1. Az **új felhasználói fiók** alapértelmezései varázslóban válassza ki az **alapértelmezett csoportot** , majd válassza ki az **alapértelmezett felvásárlási szerepkört** a szervezet felhasználói szerepköre szerint, és kattintson a **tovább**gombra.

    ![Alapértelmezett csoport](media/amazon-business-tutorial/dafault-group2.png)

1. A **metaadat-fájl feltöltése** varázslóban kattintson a **Tallózás** gombra a **metaadatok XML-** fájljának feltöltéséhez, amelyet a Azure Portal letöltött, majd kattintson a **feltöltés**gombra.

    ![Kapcsolatok adatvédelme](media/amazon-business-tutorial/connection-data1.png)

1. A letöltött metaadat-fájl feltöltése után a rendszer automatikusan feltölti a **kapcsolatok adatai** szakaszban található mezőket. Ezután kattintson a **tovább**gombra.

    ![Kapcsolatok adatvédelme](media/amazon-business-tutorial/connection-data2.png)

1. A **Attribute utasítás feltöltése** varázslóban kattintson a **kihagyás**gombra.

    ![Attribútumok](media/amazon-business-tutorial/map-attribute1.png)

1. Az **attribútum** -hozzárendelési varázslóban adja hozzá a követelmény mezőket a **+ mező hozzáadása** lehetőségre kattintva. Adja hozzá az attribútumok értékeit, beleértve a névteret is, amelyet a Azure Portal a **SAML-AttributeName** mezőbe másolt **felhasználói attribútumok &** a jogcímek szakasza alapján, majd kattintson a **tovább**gombra.

    ![Attribútumok](media/amazon-business-tutorial/map-attribute2.png)

1. Az **Amazon** -adatkapcsolatok varázslóban kattintson a **tovább**gombra.

    ![Kapcsolat](media/amazon-business-tutorial/amazon-connect.png)

1. Ellenőrizze a konfigurált lépések **állapotát** , és kattintson a **tesztelés**megkezdése elemre.

    ![Kapcsolat](media/amazon-business-tutorial/sso-connection1.png)

1. Az **SSO-kapcsolatok tesztelése** varázslóban kattintson a **teszt**elemre.

    ![Kapcsolat](media/amazon-business-tutorial/sso-connection2.png)

1. A **identitásszolgáltató által kezdeményezett URL-cím** varázslóban az **aktiválás**gombra kattintva másolja ki a **idpid** hozzárendelt értéket, és illessze be az Azure alapszintű **SAML-konfiguráció** szakaszának **Válasz URL** -címében található **idpid** paraméterbe. portál.

    ![Kapcsolat](media/amazon-business-tutorial/sso-connection3.png)

1. A **készen áll az aktív SSO-ra való váltásra?** varázsló, ellenőrizze, hogy **teljes körűen tesztelt SSO-** t használok-e, és készen áll-e az élő indításra jelölőnégyzet, és kattintson a **váltás aktív**

    ![Kapcsolat](media/amazon-business-tutorial/sso-connection4.png)

1. Végül az **SSO-kapcsolat részletei** szakaszban az **állapot** aktívként jelenik meg.

    ![Kapcsolat](media/amazon-business-tutorial/sso-connection5.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

> [!NOTE]
> Ha szükséges, a rendszergazdáknak létre kell hozniuk a saját bérlőn belüli tesztelési felhasználókat. A következő lépések bemutatják, hogyan hozhat létre egy tesztelési felhasználót.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Azure AD biztonsági csoport létrehozása a Azure Portalban

1. Kattintson **Azure Active Directory > minden csoport**elemre.

    ![Azure AD biztonsági csoport létrehozása](./media/amazon-business-tutorial/all-groups-tab.png)

1. Kattintson az **új csoport**elemre:

    ![Azure AD biztonsági csoport létrehozása](./media/amazon-business-tutorial/new-group-tab.png)

1. Adja meg a **csoport típusát**, a **csoport nevét**, a **csoport leírását**, a **tagság típusát**. Kattintson a nyílra a tagok kiválasztásához, majd keressen rá, vagy kattintson arra a tagra, amelyet hozzá szeretne adni a csoporthoz. Kattintson a **kiválasztás** elemre a kijelölt tagok hozzáadásához, majd kattintson a **Létrehozás**gombra.

    ![Azure AD biztonsági csoport létrehozása](./media/amazon-business-tutorial/group-information.png)

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az Amazon Business elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **Amazon Business**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

    >[!NOTE]
    > Ha nem rendeli hozzá a felhasználókat az Azure AD-ben, a következő hibaüzenet jelenik meg.

    ![A felhasználó hozzáadása hivatkozás](media/amazon-business-tutorial/assign-user.png)

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Rendelje hozzá az Azure AD biztonsági csoportot a Azure Portal

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza az **Amazon Business**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában írja be és válassza az **Amazon Business**lehetőséget.

    ![Az Amazon Business hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása**elemre.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Keresse meg a használni kívánt biztonsági csoportot, majd kattintson a csoportra, és adja hozzá a tagok kiválasztása szakaszhoz. Kattintson a **kiválasztás**, majd a **hozzárendelés**elemre.

    ![Keresés a biztonsági csoportban](./media/amazon-business-tutorial/assign-group.png)

    > [!NOTE]
    > Győződjön meg arról, hogy a menüsorban található értesítések azt jelzik, hogy a csoport sikeresen hozzá lett-e rendelve a Azure Portal vállalati alkalmazásához.

### <a name="create-amazon-business-test-user"></a>Amazon Business test-felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoztak létre az Amazon Businessben. Az Amazon Business az igény szerinti felhasználói üzembe helyezést is támogatja, ami alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik az Amazon Business-ben, akkor a hitelesítés után létrejön egy új.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Amazon Business csempére kattint, automatikusan be kell jelentkeznie arra az Amazon Business-szolgáltatásba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
