---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a AppNeta-Teljesítményfigyelővel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és AppNeta Teljesítményfigyelő között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 643a45fb-d6fc-4b32-b721-68899f8c7d44
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0ac9e1d32e0280bcf053578aa102cc6fd200a4b2
ms.sourcegitcommit: 5925df3bcc362c8463b76af3f57c254148ac63e3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/31/2019
ms.locfileid: "75561222"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-appneta-performance-monitor"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a AppNeta-Teljesítményfigyelővel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a AppNeta Teljesítményfigyelőt Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a AppNeta-Teljesítményfigyelőt, a következőket teheti:

* A AppNeta Teljesítményfigyelőhöz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával a AppNeta Teljesítményfigyelőbe.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* AppNeta Teljesítményfigyelő egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A AppNeta Teljesítményfigyelője támogatja az **SP** által KEZDEMÉNYEZett SSO-t


* A AppNeta Performance monitor **csak időben támogatja a** felhasználók üzembe helyezését

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.


## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>AppNeta Teljesítményfigyelő hozzáadása a katalógusból

A AppNeta Performance monitor Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a AppNeta Teljesítményfigyelőt a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **AppNeta Teljesítményfigyelő** kifejezést a keresőmezőbe.
1. Válassza ki a **AppNeta Teljesítményfigyelőt** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-appneta-performance-monitor"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a AppNeta Teljesítményfigyelőben

Konfigurálja és tesztelje az Azure AD SSO-t a AppNeta Teljesítményfigyelővel egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a AppNeta Teljesítményfigyelőben.

Az Azure AD SSO AppNeta Teljesítményfigyelővel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[AppNeta Performance monitor SSO konfigurálása](#configure-appneta-performance-monitor-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[AppNeta Performance monitor-tesztelési felhasználó létrehozása](#create-appneta-performance-monitor-test-user)** – ha a AppNeta Teljesítményfigyelőben a felhasználó Azure ad-képviseletéhez csatolt B. Simon-ügyféllel rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **AppNeta Teljesítményfigyelő** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<subdomain>.pm.appneta.com`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be a következő értéket: `PingConnect`

    > [!NOTE]
    > A bejelentkezési URL-cím értéke nem valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez lépjen kapcsolatba a [AppNeta Performance monitor](mailto:support@appneta.com) ügyfélszolgálatával. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A AppNeta Teljesítményfigyelő alkalmazás megadott formátumban várja az SAML-kijelentéseket, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/edit-attribute.png)

1. A fentieken kívül a AppNeta-figyelő alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre is fel vannak töltve, de a követelménynek megfelelően áttekintheti őket.

    | Név | Forrás attribútum|
    | --------| ----------------|
    | firstName| User. givenName|
    | lastName| felhasználó. vezetéknév|
    | e-mail| User. userPrincipalName|
    | név| User. userPrincipalName|
    | csoportok  | User. assignedroles |
    | telefon| User. telephoneNumber |
    | title| User. beosztás|
    | | |

    > [!NOTE]
    > a **csoportok** a Appneta található biztonsági csoportra vonatkoznak, amely az Azure ad egyik **szerepköréhez** van rendelve. Tekintse meg [ezt](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) a dokumentációt, amely ismerteti, hogyan hozhat létre egyéni szerepköröket az Azure ad-ben.

    1. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    1. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    1. Hagyja üresen a **névteret** .

    1. Válassza a forrás **attribútumként**lehetőséget.

    1. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    1. Kattintson **az OK** gombra

    1. Kattintson a **Mentés** gombra.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **AppNeta Teljesítményfigyelő beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a AppNeta teljesítmény-figyelőhöz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **AppNeta Teljesítményfigyelő**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-appneta-performance-monitor-sso"></a>AppNeta Performance monitor SSO konfigurálása

Ha az egyszeri bejelentkezést szeretné konfigurálni a **AppNeta Teljesítményfigyelő** oldalán, el kell küldenie a letöltött **összevonási metaadatokat tartalmazó XML-fájlt** és a megfelelő másolt url-címeket a Azure Portal a AppNeta- [figyelő támogatási csapatának](mailto:support@appneta.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-appneta-performance-monitor-test-user"></a>AppNeta Teljesítményfigyelő teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a AppNeta Teljesítményfigyelőben. A AppNeta Performance monitor támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a AppNeta Teljesítményfigyelőben, a rendszer egy újat hoz létre a hitelesítés után.

> [!Note]
> Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [AppNeta Performance monitor támogatási csapatával](mailto:support@appneta.com).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a AppNeta Teljesítményfigyelő csempére kattint, automatikusan be kell jelentkeznie a AppNeta Teljesítményfigyelőbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a AppNeta a Teljesítményfigyelőt az Azure AD-vel](https://aad.portal.azure.com/)

