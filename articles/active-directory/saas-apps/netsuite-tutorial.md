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
ms.devlang: na
ms.topic: tutorial
ms.date: 09/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1a9b834feacd6241e66e18a4f4e6aadc43e909c7
ms.sourcegitcommit: 77bfc067c8cdc856f0ee4bfde9f84437c73a6141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2019
ms.locfileid: "72438493"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netsuite"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a NetSuite-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a NetSuite-t a Azure Active Directory (Azure AD) szolgáltatásba. Ha integrálja a NetSuite-t az Azure AD-vel, a következőket teheti:

* A NetSuite-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a NetSuite-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A NetSuite egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A NetSuite támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

* A NetSuite a felhasználók üzembe helyezésének **időpontját is** támogatja

* A NetSuite támogatja a [felhasználók automatikus kiépítési](NetSuite-provisioning-tutorial.md) felállítását

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-netsuite-from-the-gallery"></a>A NetSuite hozzáadása a katalógusból

A NetSuite Azure AD-ba való integrálásának konfigurálásához hozzá kell adnia a NetSuite-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **NetSuite** kifejezést a keresőmezőbe.
1. Válassza a **NetSuite** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netsuite"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a NetSuite-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a NetSuite használatával egy **B. Simon**nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a NetSuite-beli kapcsolódó felhasználó között.

Az Azure AD SSO és a NetSuite konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[NETSUITE SSO konfigurálása](#configure-netsuite-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. A **[NetSuite-teszt felhasználójának létrehozása](#create-netsuite-test-user)** – ha a felhasználó Azure ad-képviseletéhez csatolt B. Simon-beli párja van.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **NetSuite** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:

    | |
    |--|
    | `https://<tenant-name>.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na1.sandbox.NetSuite.com/saml2/acs` |
    | `https://<tenant-name>.na2.sandbox.NetSuite.com/saml2/acs` |

    > [!NOTE]
    > Az érték nem valódi. Frissítse az értéket a tényleges válasz URL-címével. Az érték beszerzéséhez forduljon a [NetSuite ügyfél-támogatási csapathoz](http://www.netsuite.com/portal/services/support-services/suitesupport.shtml) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A NetSuite-alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

1. A fentieken kívül a NetSuite alkalmazás néhány további attribútumot vár az SAML-válaszba való visszatéréshez. A felhasználó attribútumai párbeszédpanel felhasználói jogcímek szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:

    | Név | Forrás attribútum | 
    | ---------------| --------------- |
    | account  | `account id` |

    1. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    1. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    1. Hagyja üresen a **névteret** .

    1. Válassza a forrás **attribútumként**lehetőséget.

    1. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    1. Kattintson **az OK** gombra

    1. Kattintson a **Save** (Mentés) gombra.

    >[!NOTE]
    >A Account attribútum értéke nem valódi. Ezt az értéket frissíteni fogja, amelyet az oktatóanyag későbbi részében ismertetünk.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A megfelelő URL-címet a saját igényeinek megfelelően másolja a **NetSuite-beállítások beállítása** szakaszba.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension értéket. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést a NetSuite elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **NetSuite**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-netsuite-sso"></a>A NetSuite SSO konfigurálása

1. Nyisson meg egy új fület a böngészőben, és jelentkezzen be a NetSuite vállalati webhelyre rendszergazdaként.

2. A lap tetején található eszköztáron kattintson a **beállítás**elemre, majd keresse meg a **vállalatot** , és kattintson a **szolgáltatások engedélyezése**lehetőségre.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setupsaml.png)

3. Az oldal közepén található eszköztáron kattintson a **SuiteCloud**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-suitecloud.png)

4. A **hitelesítés kezelése** szakaszban válassza az **SAML egyszeri bejelentkezés** lehetőséget az SAML egyszeri bejelentkezés beállításának engedélyezéséhez a NetSuite-ban.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-ticksaml.png)

5. A lap tetején található eszköztáron kattintson a **telepítés**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setup.png)

6. A **telepítési feladatok** listában kattintson az **integráció**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-integration.png)

7. A **hitelesítés kezelése** szakaszban kattintson az **SAML egyszeri bejelentkezés**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-saml.png)

8. Az **SAML beállítása** lapon a NetSuite- **konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-saml-setup.png)
  
    a. Válassza ki az **elsődleges hitelesítési módszert**.

    b. Az **SAMLV2-szolgáltatói metaadatokat**tartalmazó mezőnél válassza a **identitásszolgáltató-metaadatok feltöltése**lehetőséget. Ezután kattintson a **Tallózás** gombra a Azure Portal letöltött metaadat-fájl feltöltéséhez.

    c. Kattintson a **Submit (Küldés**) gombra.

9. A NetSuite-ban kattintson a **telepítés** elemre, majd keresse meg a **vállalatot** , és kattintson a felső navigációs menü **vállalati adatok** elemére.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-com.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-account-id.png)

    b. A jobb oldali oszlop **Vállalati információk** lapján másolja a **fiók azonosítóját**.

    c. Illessze be azt a **fiókazonosító** -fiókot, amelyet a NetSuite-fiókból másolt az Azure ad **attribútum érték** mezőjébe. 

10. Ahhoz, hogy a felhasználók egyszeri bejelentkezést tudjanak végezni a NetSuite-ba, először hozzá kell rendelni a megfelelő engedélyeket a NetSuite-ban. Az engedélyek hozzárendeléséhez kövesse az alábbi utasításokat.

    a. A felső navigációs menüben kattintson a **telepítés**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setup.png)

    b. A bal oldali navigációs menüben válassza a **felhasználók/szerepkörök**, majd a **Szerepkörök kezelése**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-manage-roles.png)

    c. Kattintson az **Új szerepkör**elemre.

    d. Adja meg az új szerepkör **nevét** .

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-new-role.png)

    e. Kattintson a **Save** (Mentés) gombra.

    f. A felső menüben kattintson az **engedélyek**elemre. Ezután kattintson a **telepítés**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-sso.png)

    g. Válassza az **SAML egyszeri bejelentkezés**lehetőséget, majd kattintson a **Hozzáadás**gombra.

    h. Kattintson a **Save** (Mentés) gombra.

    i. A felső navigációs menüben kattintson a **telepítő**, majd a **telepítéskezelő**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-setup.png)

    j. A bal oldali navigációs menüben válassza a **felhasználók/szerepkörök**, majd a **felhasználók kezelése**lehetőséget.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-manage-users.png)

    k. Válasszon ki egy teszt felhasználót. Ezután kattintson a **Szerkesztés** gombra, és keresse meg a **hozzáférés** lapot.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-edit-user.png)

    l. A szerepkörök párbeszédpanelen rendelje hozzá a létrehozott megfelelő szerepkört.

    ![Egyszeri bejelentkezés konfigurálása](./media/NetSuite-tutorial/ns-add-role.png)

    m. Kattintson a **Save** (Mentés) gombra.

### <a name="create-netsuite-test-user"></a>NetSuite-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a NetSuite-ban. A NetSuite az igény szerinti felhasználói üzembe helyezést is támogatja, ami alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a NetSuite-ban, a rendszer egy újat hoz létre a hitelesítés után.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor a hozzáférési panelen a NetSuite csempére kattint, automatikusan be kell jelentkeznie a NetSuite-ba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A NetSuite kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

