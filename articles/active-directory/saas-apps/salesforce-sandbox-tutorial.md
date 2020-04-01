---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Salesforce sandboxával | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Salesforce sandbox között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ee54c39e-ce20-42a4-8531-da7b5f40f57c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b9d6d11b6b56483f954049fdc1858db31f35c14a
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76290003"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-salesforce-sandbox"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Salesforce sandbox-szal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Salesforce sandboxot az Azure Active Directoryval (Azure AD). Ha integrálja a Salesforce sandboxot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Salesforce sandboxhoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve a Salesforce Sandbox az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Salesforce sandbox egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Salesforce Sandbox támogatja az **SP és az IDP** által kezdeményezett sso-t
* A Salesforce sandbox támogatja a **Just In Time** felhasználói kiépítést
* A Salesforce sandbox támogatja az [ **automatikus** felhasználói kiépítést](salesforce-sandbox-provisioning-tutorial.md)
* A Salesforce védőfal konfigurálása után kényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlők a feltételes hozzáféréstől származnak. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-salesforce-sandbox-from-the-gallery"></a>Salesforce sandbox hozzáadása a galériából

A Salesforce Sandbox Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Salesforce sandboxot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **katalógus hozzáadásszakaszában** írja be a **Salesforce sandbox kifejezést** a keresőmezőbe.
1. Válassza a **Salesforce Sandbox** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-salesforce-sandbox"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése a Salesforce sandboxhoz

Konfigurálja és tesztelje az Azure AD SSO-t a Salesforce sandbox segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a salesforce sandbox kapcsolódó felhasználója között.

Az Azure AD SSO konfigurálásához és teszteléséhez a Salesforce sandbox segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Salesforce Sandbox SSO-t](#configure-salesforce-sandbox-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Hozzon létre Salesforce sandbox teszt felhasználó](#create-salesforce-sandbox-test-user)** - egy megfelelője B.Simon a Salesforce sandbox, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Salesforce sandbox** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Ha **a** **Szolgáltató metaadatfájlja** van, és **idp** által kezdeményezett módban szeretne konfigurálni, hajtsa végre a következő lépéseket:

    a. Kattintson **a Metaadatfájl feltöltése gombra.**

    ![Metaadatfájl feltöltése](common/upload-metadata.png)

    b. Kattintson a **mappaemblémára** a metaadatfájl kijelöléséhez, majd a **Feltöltés gombra.**

    ![metaadatfájl kiválasztása](common/browse-upload-metadata.png)

    > [!NOTE]
    > A szolgáltató metaadatfájlját a Salesforce sandbox felügyeleti portáljáról kapja meg, amelyet az oktatóanyag későbbi részében ismertetünk.

    c. A metaadatfájl sikeres feltöltése után a **Válasz URL-értéke** automatikusan megjelenik a **Válasz URL-cím** szövegmezőjében.

    ![image](common/both-replyurl.png)

    > [!Note]
    > Ha a **Válasz URL-értéke** nem automatikusan polulated, majd töltse ki az értéket manuálisan a követelménynek megfelelően.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra a **metaadat-XML** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Salesforce sandbox beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Salesforce sandbox hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Salesforce Sandbox lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-salesforce-sandbox-sso"></a>Salesforce sandbox sso konfigurálása

1. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be a Salesforce sandbox rendszergazdai fiókjába.

2. Kattintson a **beállítás** gombra a lap jobb felső sarkában található **Beállítások ikonra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure1.png)

3. Görgessen le a bal oldali navigációs ablak **beállítások eleméhez,** és kattintson az **Identitás gombra** a kapcsolódó szakasz kibontásához. Ezután kattintson **az Egyszeri bejelentkezési beállítások gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

4. Az **Egyszeri bejelentkezés beállításai** lapon kattintson a **Szerkesztés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure3.png)

5. Válassza **az SAML Enabled**lehetőséget, majd kattintson a Mentés **gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

6. Az SAML egyszeri bejelentkezési beállításainak konfigurálásához kattintson az **Új a metaadatfájlból gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

7. Kattintson a **Fájl kiválasztása gombra** az Azure portalról letöltött METAadat-XML-fájl feltöltéséhez, majd kattintson a **Létrehozás gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/xmlchoose.png)

8. Az **SAML Egyszeri bejelentkezési beállítások** lapon a mezők automatikusan feljönnek, és a Mentés gombra kattintanak.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/salesforcexml.png)

9. Az **Egyszeri bejelentkezés beállításai** lapon kattintson a **Metaadatok letöltése** gombra a szolgáltató metaadatfájljának letöltéséhez. Használja ezt a fájlt az Azure Portal **alapszintű SAML-konfiguráció szakaszában** a szükséges URL-címek konfigurálásához a fent leírtak szerint.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure4.png)

10. Ha az alkalmazást **SP** által kezdeményezett módban szeretné konfigurálni, az alábbi előfeltételei vannak:

    a. Ellenőrzött tartománnyal kell rendelkeznie.

    b. Konfigurálnia kell és engedélyeznie kell a tartományt a Salesforce sandboxban, ennek lépéseit az oktatóanyag későbbi részében ismerteti.

    c. Az Azure Portalon az **Alapvető SAML-konfiguráció** szakaszban kattintson a **További URL-címek beállítása** elemre, és hajtsa végre a következő lépést:
  
    ![Salesforce sandbox tartomány és URL-címek egyszeri bejelentkezési információk](common/both-signonurl.png)

    A **Bejelentkezési URL-cím** mezőbe írja be az értéket a következő minta használatával:`https://<instancename>--Sandbox.<entityid>.my.salesforce.com`

    > [!NOTE]
    > Ezt az értéket a tartomány engedélyezése után a Salesforce sandbox portálról kell másolni.

11. Az **SAML aláíró tanúsítvány csoportban** kattintson az **Összevonási metaadat-XML elemre,** majd mentse az XML-fájlt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

12. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be a Salesforce sandbox rendszergazdai fiókjába.

13. Kattintson a **beállítás** gombra a lap jobb felső sarkában található **Beállítások ikonra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure1.png)

14. Görgessen le a bal oldali navigációs ablak **beállítások eleméhez,** és kattintson az **Identitás gombra** a kapcsolódó szakasz kibontásához. Ezután kattintson **az Egyszeri bejelentkezési beállítások gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso.png)

15. Az **Egyszeri bejelentkezés beállításai** lapon kattintson a **Szerkesztés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure3.png)

16. Válassza **az SAML Enabled**lehetőséget, majd kattintson a Mentés **gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-enable-saml.png)

17. Az SAML egyszeri bejelentkezési beállításainak konfigurálásához kattintson az **Új a metaadatfájlból gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-admin-sso-new.png)

18. Kattintson a **Fájl kiválasztása gombra** a metaadat-XML-fájl feltöltéséhez, majd a **Létrehozás gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/xmlchoose.png)

19. Az **SAML Egyszeri bejelentkezési beállítások** lapon a mezők automatikusan felteljesednek, írja be a konfiguráció nevét (például: *SPSSOWAAD_Test*), a **Név** mezőbe, és kattintson a Mentés gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-saml-config.png)

20. Ha engedélyezni szeretné tartományát a Salesforce sandboxban, hajtsa végre az alábbi lépéseket:

    > [!NOTE]
    > A tartomány engedélyezése előtt ugyanazt kell létrehoznia a Salesforce sandbox-on. További információt [a Tartománynév megadása](https://help.salesforce.com/HTViewHelpDoc?id=domain_name_define.htm&language=en_US)című témakörben talál. A tartomány létrehozása után győződjön meg arról, hogy megfelelően van konfigurálva.

21. A Salesforce sandbox bal oldali navigációs ablaktábláján kattintson a **Vállalati beállítások** elemre a kapcsolódó szakasz kibontásához, majd kattintson a **Saját tartomány**parancsra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-my-domain.png)

22. A **Hitelesítési konfiguráció csoportban** kattintson a **Szerkesztés gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/sf-edit-auth-config.png)

23. A **Hitelesítési konfiguráció** szakasz **hitelesítési szolgáltatásként**jelölje ki annak az SAML egyszeri bejelentkezési beállításnak a nevét, amelyet a Salesforce sandbox egyszeri bejelentkezési konfigurációja során állított be, majd kattintson a **Mentés gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-sandbox-tutorial/configure2.png)

### <a name="create-salesforce-sandbox-test-user"></a>Salesforce sandbox tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Salesforce sandboxban. A Salesforce sandbox támogatja a just-in-time kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik a Salesforce sandboxban, akkor egy új jön létre, amikor megpróbál hozzáférni a Salesforce sandboxhoz. A Salesforce Sandbox támogatja az automatikus felhasználói kiépítést is, itt további [részleteket](salesforce-sandbox-provisioning-tutorial.md) talál az automatikus felhasználói kiépítés konfigurálásáról.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Salesforce sandbox csempére kattint, automatikusan be kell jelentkeznie arra a Salesforce sandboxba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Salesforce sandboxot az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/protect-salesforce)

- [Felhasználói kiépítés konfigurálása](salesforce-sandbox-provisioning-tutorial.md)

- [A Salesforce védőfal védelme fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
