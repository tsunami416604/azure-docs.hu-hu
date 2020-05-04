---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a NetSuite-nal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a NetSuite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dafa0864-aef2-4f5e-9eac-770504688ef4
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/28/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2eba47dede77f6d8dd19bde99a94de3ff5900f99
ms.sourcegitcommit: 4499035f03e7a8fb40f5cff616eb01753b986278
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/03/2020
ms.locfileid: "82732801"
---
# <a name="tutorial-integrate-azure-ad-single-sign-on-sso-with-netsuite"></a>Oktatóanyag: az Azure AD egyszeri bejelentkezés (SSO) integrálása a NetSuite-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a NetSuite-t a Azure Active Directory (Azure AD) szolgáltatásba. Ha integrálja a NetSuite-t az Azure AD-vel, a következőket teheti:

* A NetSuite-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a NetSuite-ba az Azure AD-fiókjával.
* A fiókokat egy központi helyen, a Azure Portal kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A NetSuite egyszeri bejelentkezés (SSO) használatára képes előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. 

A NetSuite a következőket támogatja:

* IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezés.
* JIT (igény szerinti) felhasználó kiépítés.
* [Automatikus felhasználó-kiépítés](NetSuite-provisioning-tutorial.md).
* A NetSuite konfigurálása után kikényszerítheti a munkamenet-vezérlést, amely a szervezet bizalmas adatainak valós idejű kiszűrése és beszivárgását teszi elérhetővé. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

> [!NOTE]
> Mivel az alkalmazás azonosítója egy rögzített karakterlánc-érték, csak egy példány konfigurálható egyetlen bérlőn.

## <a name="add-netsuite-from-the-gallery"></a>A NetSuite hozzáadása a katalógusból

A NetSuite Azure AD-ba való integrálásának konfigurálásához adja hozzá a NetSuite-t a katalógusból a felügyelt SaaS-alkalmazások listájához a következő módon:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók.
1. A bal oldali ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **NetSuite** kifejezést a keresőmezőbe.
1. Az eredmények ablaktábláján válassza a **NetSuite**lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a NetSuite-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a NetSuite használatával egy **B. Simon**nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a NetSuite-beli kapcsolódó felhasználó között.

Az Azure AD SSO és a NetSuite konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t, hogy a felhasználók használhatják ezt a funkciót.
    * [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon felhasználóval.  
    * [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy engedélyezze a B. Simon felhasználó számára az Azure ad egyszeri bejelentkezés használatát.
1. [Konfigurálja a NETSUITE SSO](#configure-netsuite-sso) -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    * [Hozza létre a NetSuite-teszt felhasználót](#create-the-netsuite-test-user) , hogy rendelkezzen egy, a felhasználó Azure ad-képviseletéhez csatolt B. Simon felhasználóval.
1. Ellenőrizze az [SSO](#test-sso) -t a konfiguráció működésének ellenőrzéséhez.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ha engedélyezni szeretné az Azure AD SSO használatát a Azure Portalban, tegye a következőket:

1. A [Azure Portal](https://portal.azure.com/)a **NetSuite** Application Integration oldalon keresse meg a **kezelés** szakaszt, majd válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** ablaktáblán válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-** panelen való beállítása lapon válassza az **ALAPszintű SAML-konfiguráció**melletti **Szerkesztés** ("ceruza") ikont.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban a **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő formátumok egyikében:

    ||
    |-|
    | `https://<Instance ID>.NetSuite.com/saml2/acs`|
    | `https://<Instance ID>.na1.NetSuite.com/saml2/acs`|
    | `https://<Instance ID>.na2.NetSuite.com/saml2/acs`|
    | `https://<Instance ID>.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Instance ID>.na1.sandbox.NetSuite.com/saml2/acs`|
    | `https://<Instance ID>.na2.sandbox.NetSuite.com/saml2/acs`|

    * Az értéket a NetSuite konfigurációs szakaszban találja meg, amelyet később az oktatóanyagban, a NetSuite-konfiguráció alatt, a 8. lépésben ismertetünk. ** < `Instance ID` ** Itt megtalálja a pontos tartományt (például system.na0.netsuite.com ebben az esetben).

        ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/domain-value.png)

        > [!NOTE]
        > Az előző URL-címek értékei nem valósak. Frissítse őket a tényleges válasz URL-címével. Az érték beszerzéséhez forduljon a [NetSuite ügyfél-támogatási csapathoz](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml). A Azure Portal az **alapszintű SAML-konfiguráció** szakaszának formátumait is megtekintheti.

1. A NetSuite-alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a NetSuite alkalmazás néhány további attribútumot vár az SAML-válaszban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name (Név) | Forrás attribútum |
    | ---------------| --------------- |
    | account  | `account id` |

    > [!NOTE]
    > A fiók attribútum értéke nem valódi. Ezt az értéket az oktatóanyag későbbi részében leírtak szerint frissítheti.

1. Az egyszeri bejelentkezés az SAML-vel lapon az SAML aláíró tanúsítvány szakaszban keresse meg az összevonási metaadatok XML-fájlját, és válassza a letöltés lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A saját szükséglettől függően másolja ki a megfelelő URL-címet vagy URL-címeket az **NetSuite beállítása** szakaszban.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory** > **felhasználók** > **minden felhasználó**lehetőséget.

1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.

1. A **felhasználó** tulajdonságai ablaktáblán hajtsa végre az alábbi lépéseket:

   a. A név mezőbe írja be a **B. Simon** **nevet** .  
   b. A **Felhasználónév** mezőbe írja be a username@companydomain.extension (például B.Simon@contoso.com) nevet.  
   c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.  
   d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban engedélyezheti a B. Simon felhasználó számára az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a NetSuite-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **NetSuite**elemet.
1. Az Áttekintés ablaktáblán keresse meg a **kezelés** szakaszt, majd válassza a **felhasználók és csoportok** hivatkozást.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása** lehetőséget, majd a **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználó hozzáadása" gomb](common/add-assign-user.png)

1. A **felhasználók és csoportok** ablaktábla **felhasználók** legördülő listájában válassza ki a **B. Simon**elemet, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkör-értéket vár, tegye a következőket:

   a. A **szerepkör kiválasztása** ablaktáblában, a legördülő listából válassza ki a megfelelő szerepkört a felhasználó számára.  
   b. A képernyő alján kattintson a **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** panelen kattintson a **hozzárendelés** gombra.

## <a name="configure-netsuite-sso"></a>A NetSuite SSO konfigurálása

1. Nyisson meg egy új fület a böngészőben, és jelentkezzen be a NetSuite vállalati webhelyre rendszergazdaként.

2. A felső navigációs sávon válassza a **beállítás**lehetőséget, majd válassza a **vállalat** > **engedélyezése funkciók**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Az oldal közepén található eszköztáron válassza a **SuiteCloud**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-suitecloud.png)

4. A **hitelesítés kezelése**területen jelölje be az **SAML egyszeri bejelentkezés** jelölőnégyzetet az SAML egyszeri bejelentkezés beállítás engedélyezéséhez a NetSuite-ban.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-ticksaml.png)

5. A felső navigációs sávon válassza a **telepítés**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setup.png)

6. A **beállítási feladatok** listában válassza az **integráció**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-integration.png)

7. A **hitelesítés kezelése**területen válassza az **SAML egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-saml.png)

8. Az **SAML beállítása** ablaktábla **NetSuite konfigurálása**területén tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Jelölje be az **elsődleges hitelesítési módszer** jelölőnégyzetet.

    b. Az **SAMLV2-szolgáltató metaadatainak**területén válassza a **identitásszolgáltató-metaadatok feltöltése**lehetőséget, majd a **Tallózás** gombra kattintva töltse fel a letöltött metaadat-fájlt a Azure Portal.

    c. Válassza a **Küldés** lehetőséget.

9. A NetSuite felső navigációs sávban válassza a **beállítás**, majd a **vállalati** > **vállalati adatok**elemet.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-com.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-account-id.png)

    b. A **Vállalati információk** ablaktáblán, a jobb oldali oszlopban másolja ki a **fiókazonosító** értékét.

    c. Illessze be a NetSuite-fiókból másolt **fiók azonosítóját** az Azure ad **attribútum érték** mezőjébe.

10. Ahhoz, hogy a felhasználók egyszeri bejelentkezést tudjanak végezni a NetSuite-ba, először hozzá kell rendelni a megfelelő engedélyeket a NetSuite-ban. Az engedélyek hozzárendeléséhez tegye a következőket:

    a. A felső navigációs sávon válassza a **telepítés**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setup.png)

    b. A bal oldali ablaktáblán válassza a **felhasználók/szerepkörök**, majd a **Szerepkörök kezelése**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Válassza az **Új szerepkör**lehetőséget.

    d. Adja meg az új szerepkör **nevét** .

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-new-role.png)

    e. Kattintson a **Mentés** gombra.

    f. A felső navigációs sávon válassza az **engedélyek**lehetőséget. Ezután válassza a **telepítés**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-sso.png)

    g. Válassza az **SAML egyszeri bejelentkezés**lehetőséget, majd válassza a **Hozzáadás**lehetőséget.

    h. Kattintson a **Mentés** gombra.

    i. A felső navigációs sávon válassza a **beállítás**, majd a **telepítéskezelő**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setup.png)

    j. A bal oldali ablaktáblán válassza a **felhasználók/szerepkörök**lehetőséget, majd válassza a **felhasználók kezelése**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Válasszon ki egy teszt felhasználót, válassza a **Szerkesztés**lehetőséget, majd válassza a **hozzáférés** lapot.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-edit-user.png)

    l. A **szerepkörök** ablaktáblán rendelje hozzá a létrehozott megfelelő szerepkört.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-add-role.png)

    m. Kattintson a **Mentés** gombra.

### <a name="create-the-netsuite-test-user"></a>A NetSuite test felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a NetSuite-ban. A NetSuite az igény szerinti felhasználói üzembe helyezést is támogatja, ami alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs művelet. Ha egy felhasználó még nem létezik a NetSuite-ban, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen kiválasztja a NetSuite csempét, akkor automatikusan be kell jelentkeznie arra a NetSuite-ra, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További háttéranyagok

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [A NetSuite kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)
- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [A NetSuite védelem speciális láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)