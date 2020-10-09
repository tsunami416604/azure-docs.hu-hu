---
title: 'Oktatóanyag: Azure Active Directory integráció a Salesforce-Sandboxmal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Salesforce között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.openlocfilehash: 277acbc84ab435ce1076c30a1e49f6ffdd2a0586
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "88543717"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Salesforce-Sandboxtal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Salesforce-munkaterületet a Azure Active Directory (Azure AD) szolgáltatással. Ha az Azure AD-vel integrálja a Salesforce-munkaterületet, a következőket teheti:

* Az Azure AD-ben a Salesforce-Homokozóhoz hozzáférő vezérlő.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Salesforce a homokozóba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Salesforce-alapú egyszeri bejelentkezésre (SSO) vonatkozó előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Salesforce sandbox támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t
* A Salesforce Sandbox a felhasználók üzembe helyezésének **időpontját is** támogatja
* A Salesforce sandbox támogatja a [felhasználók **automatikus** kiépítési felállítását](salesforce-sandbox-provisioning-tutorial.md)
* Miután konfigurálta a Salesforce-munkaterületet, megszabhatja a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszűrése és beszivárgását. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Salesforce-homokozó hozzáadása a katalógusból

A Salesforce-homokozó Azure AD-be való integrálásának konfigurálásához hozzá kell adnia egy Salesforce-munkaterületet a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Salesforce homokozó** kifejezést a keresőmezőbe.
1. Válassza ki a **Salesforce sandbox** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce-sandbox"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Salesforce-homokozóban

Konfigurálja és tesztelje az Azure AD SSO-t a Salesforce homokozóban egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Salesforce-homokozóban.

Az Azure AD SSO Salesforce-beli homokozóval való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Salesforce SANDBOX SSO konfigurálása](#configure-salesforce-sandbox-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Salesforce-alapú sandbox-teszt felhasználó létrehozása](#create-salesforce-sandbox-test-user)** – a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-beli Salesforce
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Salesforce sandbox** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban, ha **szolgáltatói metaadatokat tartalmazó fájllal** rendelkezik, és **identitásszolgáltató** kezdeményezett módban kívánja konfigurálni a következő lépéseket:

    a. Kattintson a **metaadat-fájl feltöltése**elemre.

    ![Metaadat-fájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés**elemre.

    ![metaadat-fájl kiválasztása](common/browse-upload-metadata.png)

    > [!NOTE]
    > A szolgáltatói metaadatokat tartalmazó fájlt a Salesforce sandbox felügyeleti portálján érheti el, amelyet az oktatóanyag későbbi részében talál.

    c. A metaadat-fájl feltöltése után a **Válasz URL-címe** automatikusan betöltődik a **Válasz URL-címe** szövegmezőbe.

    ![image](common/both-replyurl.png)

    > [!Note]
    > Ha a **Válasz URL-címe** nem kap automatikus polulated, akkor a követelménynek megfelelően adja meg manuálisan az értéket.

5. Az **egyszeres Sign-On beállítása az SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** lehetőségre a **metaadatok XML** -fájljának a megadott beállítások alapján történő letöltéséhez, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A Salesforce-munkaterületének **beállítása** szakaszban a követelmények szerint másolja a megfelelő URL-címeket.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL-cím

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** elemre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Salesforce Homokozóhoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Salesforce-homokozó**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-salesforce-sandbox-sso"></a>A Salesforce sandbox SSO konfigurálása

1. Nyisson meg egy új fület a böngészőben, és jelentkezzen be a Salesforce sandbox-rendszergazdai fiókjába.

2. Kattintson **az oldal** jobb felső sarkában található **Beállítások ikonra** .

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/configure1.png)

3. Görgessen le a **beállításokhoz** a bal oldali navigációs ablaktáblán, és kattintson az **Identity (identitás** ) elemre a kapcsolódó szakasz kibontásához. Ezután kattintson az **egyszeres Sign-On beállítások**lehetőségre.

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Az **egyszeres Sign-On beállítások** lapon kattintson a **Szerkesztés** gombra.

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/configure3.png)

5. Válassza az **SAML engedélyezve**lehetőséget, majd kattintson a **Mentés**gombra.

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Az SAML egyszeri bejelentkezés beállításainak konfigurálásához kattintson **az új elemre a metaadat-fájlból**.

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Kattintson a **fájl kiválasztása** lehetőségre a Azure Portal letöltött metaadat XML-fájl feltöltéséhez, majd kattintson a **Létrehozás**gombra.

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Az **SAML egyszeri Sign-On beállításai** lapon a mezők automatikusan kitölthetők, majd a Mentés gombra.

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Az **egyszeres Sign-On beállítások** lapon kattintson a **metaadatok letöltése** gombra a szolgáltatói metaadat fájl letöltéséhez. Ezt a fájlt a Azure Portal **alapszintű SAML-konfiguráció** szakaszában tekintheti meg a szükséges URL-címek konfigurálásához a fentiekben leírtak szerint.

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/configure4.png)

10. Ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni, az alábbi előfeltételek vonatkoznak rá:

    a. Rendelkeznie kell egy ellenőrzött tartománnyal.

    b. A Salesforce-homokozóban konfigurálnia és engedélyeznie kell a tartományt, ennek az oktatóanyagnak a lépéseit később ismertetjük.

    c. A Azure Portal az **alapszintű SAML-konfiguráció** szakaszban kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést:
  
    ![Salesforce és URL-címek egyszeri bejelentkezésének adatai](common/both-signonurl.png)

    A **bejelentkezési URL** szövegmezőbe írja be az értéket a következő minta használatával: `https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > A tartomány engedélyezése után ezt az értéket át kell másolni a Salesforce homokozó portálról.

11. Az **SAML aláíró tanúsítvány** szakaszban kattintson az **összevonási metaadatok XML** elemre, majd mentse az XML-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

12. Nyisson meg egy új fület a böngészőben, és jelentkezzen be a Salesforce sandbox-rendszergazdai fiókjába.

13. Kattintson **az oldal** jobb felső sarkában található **Beállítások ikonra** .

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/configure1.png)

14. Görgessen le a **beállításokhoz** a bal oldali navigációs ablaktáblán, és kattintson az **Identity (identitás** ) elemre a kapcsolódó szakasz kibontásához. Ezután kattintson az **egyszeres Sign-On beállítások**lehetőségre.

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Az **egyszeres Sign-On beállítások** lapon kattintson a **Szerkesztés** gombra.

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/configure3.png)

16. Válassza az **SAML engedélyezve**lehetőséget, majd kattintson a **Mentés**gombra.

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Az SAML egyszeri bejelentkezés beállításainak konfigurálásához kattintson **az új elemre a metaadat-fájlból**.

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. A metaadatok XML-fájljának feltöltéséhez kattintson a **fájl kiválasztása** elemre, majd kattintson a **Létrehozás**gombra.

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Az **SAML egyszeri Sign-On beállításai** lapon a mezők automatikusan töltődnek be, írja be a konfiguráció nevét (például: *SPSSOWAAD_Test*), a **név** szövegmezőbe, majd kattintson a Mentés gombra.

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. A tartomány Salesforce-beli homokozóban való engedélyezéséhez hajtsa végre a következő lépéseket:

    > [!NOTE]
    > A tartomány engedélyezése előtt ugyanezt a Salesforce-homokozóban kell létrehoznia. További információ: [a tartománynév meghatározása](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US). A tartomány létrehozása után győződjön meg arról, hogy megfelelően van-e konfigurálva.

21. A Salesforce homokozó bal oldali navigációs paneljén kattintson a **Vállalati beállítások** elemre a kapcsolódó szakasz kibontásához, majd kattintson **a saját tartomány**elemre.

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. A **hitelesítés konfigurációja** szakaszban kattintson a **Szerkesztés**elemre.

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. A **hitelesítési konfiguráció** szakasz **hitelesítési szolgáltatás**részében válassza ki a Salesforce-homokozóban az SSO-konfiguráció során beállított SAML-Sign-On beállítás nevét, majd kattintson a **Save (Mentés**) gombra.

    ![Egyetlen Sign-On konfigurálása](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Salesforce-beli sandbox-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Salesforce homokozóban. A Salesforce sandbox az igény szerinti üzembe helyezést is támogatja, ami alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Salesforce-homokozóban, akkor a rendszer létrehoz egy újat, amikor megpróbál hozzáférni a Salesforce-Homokozóhoz. A Salesforce sandbox Emellett támogatja az automatikus felhasználó-kiépítés használatát, így további részleteket [itt](salesforce-sandbox-provisioning-tutorial.md) talál az automatikus felhasználó-kiépítés konfigurálásával kapcsolatban.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Salesforce-homokozó csempére kattint, automatikusan be kell jelentkeznie a Salesforce-Sandboxba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Salesforce Sandboxot az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/protect-salesforce)

- [A felhasználók üzembe helyezésének konfigurálása](salesforce-sandbox-provisioning-tutorial.md)

- [A Salesforce-homokozó védelem speciális láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
