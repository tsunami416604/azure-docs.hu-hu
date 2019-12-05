---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a uniFLOW online-nal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a uniFLOW online között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 28313d27-638c-4d50-92ad-d093f2ae9ecf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/02/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9eb369047574ef76dd31996fd16399380ea027c8
ms.sourcegitcommit: 6c01e4f82e19f9e423c3aaeaf801a29a517e97a0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2019
ms.locfileid: "74823304"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-uniflow-online"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a uniFLOW online-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a uniFLOW online-t a Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a uniFLOW online-t, a következőket teheti:

* Az Azure AD-ben a uniFLOW online elérésére jogosult vezérlő.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az online uniFLOW az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* uniFLOW online egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* a uniFLOW online támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-uniflow-online-from-the-gallery"></a>Online uniFLOW hozzáadása a katalógusból

A uniFLOW online Azure AD-be való integrálásának konfigurálásához a katalógusból a felügyelt SaaS-alkalmazások listájára kell uniFLOW online-t hozzáadnia.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **uniFLOW online** kifejezést a keresőmezőbe.
1. Válassza az **UniFLOW online** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-uniflow-online"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése online uniFLOW

Konfigurálja és tesztelje az Azure AD SSO-t a uniFLOW online-ban egy **B. Simon**nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a uniFLOW online-ban.

Az Azure AD SSO és a uniFLOW online konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[UniFLOW online egyszeri bejelentkezés konfigurálása](#configure-uniflow-online-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[UniFLOW-alapú online tesztelési felhasználó létrehozása](#create-uniflow-online-test-user)** – ha a felhasználó Azure ad-uniFLOW kapcsolódik, a "B
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **uniFLOW online** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.us.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.sg.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.jp.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.au.uniFLOWonline.com`|

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:

    | | |
    |-|-|
    | `https://<tenant_domain_name>.eu.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.us.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.sg.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.jp.uniFLOWonline.com`|
    | `https://<tenant_domain_name>.au.uniFLOWonline.com`|

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek megszerzéséhez forduljon a [UniFLOW online ügyfélszolgálati csapatához](mailto:support@nt-ware.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. a uniFLOW online alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a uniFLOW online alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Név |  Forrás attribútum|
    | -----------| --------------- |
    | DisplayName | felhasználó. DisplayName |
    | Becenév | User. onpremisessamaccountname |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a Másolás gombra az **alkalmazás-összevonási metaadatok URL-címének** másolásához és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a uniFLOW online-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **UniFLOW online**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-uniflow-online-sso"></a>UniFLOW online SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a uniFLOW online webhelyre rendszergazdaként.

1. A bal oldali navigációs panelen válassza a **felhasználó** fület.

    ![uniFLOW online konfiguráció](./media/uniflow-online-tutorial/configure1.png)

1. Kattintson az **identitás-szolgáltatók**elemre.

    ![uniFLOW online konfiguráció](./media/uniflow-online-tutorial/configure2.png)

1. Kattintson az **Identity Provider hozzáadása**elemre.

    ![uniFLOW online konfiguráció](./media/uniflow-online-tutorial/configure3.png)

1. Az **identitás-szolgáltató hozzáadása** szakaszban hajtsa végre a következő lépéseket:


    ![uniFLOW online konfiguráció](./media/uniflow-online-tutorial/configure4.png)

    a. Adja meg a megjelenítendő nevet: *AZUREAD SSO*.

    b. A **szolgáltató típusa**mezőben válassza a **ws-fed** lehetőséget a legördülő listából.

    c. A **ws-fed típusnál**válassza a legördülő menüből a **Azure Active Directory** lehetőséget.

    d. Kattintson a **Save** (Mentés) gombra.

1. Az **általános** lapon hajtsa végre a következő lépéseket:

    ![uniFLOW online konfiguráció](./media/uniflow-online-tutorial/configure5.png)

    a. Adja meg a megjelenítendő nevet: *AZUREAD SSO*.

    b. Válassza ki az **URL-cím** lehetőséget a **ADGS-összevonási metaadatokhoz**.

    c. Az **összevonási metaadatok URl-címe** szövegmezőbe illessze be az **alkalmazás-összevonási metaadatok URL-címét** , amelyet a Azure Portal másolt.

    d. Válassza az **Identitáskezelés** **engedélyezettként**lehetőséget.

    e. Válassza az **automatikus felhasználói regisztráció** **aktiválva**lehetőséget.

    f. Kattintson a **Save** (Mentés) gombra.

### <a name="create-uniflow-online-test-user"></a>UniFLOW online tesztelési felhasználó létrehozása

1. Egy másik böngészőablakban jelentkezzen be a uniFLOW online webhelyre rendszergazdaként.

1. A bal oldali navigációs panelen válassza a **felhasználó** fület.

    ![uniFLOW online konfiguráció](./media/uniflow-online-tutorial/configure1.png)

1. Kattintson a **felhasználó hozzáadása**elemre.

    ![uniFLOW online konfiguráció](./media/uniflow-online-tutorial/user1.png)

1. Kattintson a **felhasználó manuális létrehozása**lehetőségre.

    ![uniFLOW online konfiguráció](./media/uniflow-online-tutorial/user2.png)

1. A **felhasználó manuális létrehozása** lapon adja meg a szükséges értékeket a szervezet követelményeinek megfelelően.

    ![uniFLOW online konfiguráció](./media/uniflow-online-tutorial/user3.png)

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a uniFLOW online csempére kattint, automatikusan be kell jelentkeznie a uniFLOW online-ba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A uniFLOW online kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)