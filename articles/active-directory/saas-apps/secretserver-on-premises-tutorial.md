---
title: 'Oktatóanyag: Azure Active Directory integráció a titkos kiszolgálóval (helyszíni) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a titkos kiszolgáló (helyszíni) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: be4ba84a-275d-4f71-afce-cb064edc713f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4926fc1833cc14b2ad81a01e230a5c3c37ba6ab3
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68880131"
---
# <a name="tutorial-integrate-secret-server-on-premises-with-azure-active-directory"></a>Oktatóanyag: A titkos kiszolgáló (helyszíni) integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálható a titkos kiszolgáló (helyszíni) a Azure Active Directory (Azure AD) szolgáltatással. Ha a titkos kiszolgálót (helyszíni) az Azure AD-vel integrálja, a következőket teheti:

* Az Azure AD-ben található, a titkos kiszolgálóhoz (helyszíni) hozzáférő vezérlő.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a titkos kiszolgálóra (helyszíni) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A titkos kiszolgáló (helyszíni) egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A titkos kiszolgáló (helyszíni) támogatja az **SP és a identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-secret-server-on-premises-from-the-gallery"></a>Titkos kiszolgáló (helyszíni) hozzáadása a gyűjteményből

A titkos kiszolgáló (helyszíni) Azure AD-be való integrálásának konfigurálásához hozzá kell adnia egy titkos kiszolgálót (helyszíni) a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **titkos kiszolgáló (helyszíni)** kifejezést a keresőmezőbe.
1. Válassza a **titkos kiszolgáló (helyszíni)** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálja és tesztelje az Azure AD SSO-t a titkos kiszolgálóval (helyszíni) egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a titkos kiszolgálón (helyszíni).

Az Azure AD SSO és a titkos kiszolgáló (helyszíni) konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. A **[titkos kiszolgáló (helyszíni) egyszeri bejelentkezésének konfigurálása](#configure-secret-server-on-premises-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre egy titkos kiszolgálót (helyszíni) tesztelési felhasználót](#create-secret-server-on-premises-test-user)** – hogy a B. Simon egy, a felhasználó Azure ad-képviseletéhez kapcsolódó titkos kiszolgáló (helyszíni) partnere legyen.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **titkos kiszolgáló (helyszíni)** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az alapszintű **SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be a felhasználó által választott értéket példaként:`https://secretserveronpremises.azure`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<SecretServerURL>/SAML/AssertionConsumerService.aspx`

    > [!NOTE]
    > A fent látható entitás-azonosító csak példa, és szabadon választhat olyan egyedi értéket, amely azonosítja a titkos kiszolgálói példányt az Azure AD-ben. Ezt az entitás-azonosítót el kell küldenie a [titkos kiszolgáló (helyszíni) ügyfél-támogatási csapatának](https://thycotic.force.com/support/s/) , és konfigurálnia kell őket a saját oldalán. További részletekért tekintse meg [ezt a cikket](https://thycotic.force.com/support/s/article/Configuring-SAML-in-Secret-Server).

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<SecretServerURL>/login.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges válasz URL-címmel és a bejelentkezési URL-címmel. Az értékek beszerzéséhez vegye fel [a kapcsolatot a titkos kiszolgáló (helyszíni) ügyfél-támogatási csapatával](https://thycotic.force.com/support/s/) . Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az **SAML aláíró tanúsítvány** párbeszédpanel megnyitásához.

    ![Aláírási beállítások](./media/secretserver-on-premises-tutorial/edit-saml-signon.png)

1. Válassza az **aláírás lehetőséget** az **SAML-válasz és az állítás aláírásához**.

    ![Aláírási beállítások](./media/secretserver-on-premises-tutorial/signing-option.png)

1. A **titkos kiszolgáló beállítása (helyszíni)** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-secret-server-on-premises-sso"></a>A titkos kiszolgáló (helyszíni) egyszeri bejelentkezésének konfigurálása

Ha az egyszeri bejelentkezést a **titkos kiszolgáló (helyszíni)** oldalon szeretné konfigurálni, el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt url-címeket a Azure Portal a [titkos kiszolgáló (helyszíni) támogatási csapatának](https://thycotic.force.com/support/s/). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a titkos kiszolgálóhoz (helyszíni).

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **titkos kiszolgáló (helyszíni)** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-secret-server-on-premises-test-user"></a>Titkos kiszolgáló (helyszíni) tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a titkos kiszolgálón (helyszíni). A [titkos kiszolgáló (helyszíni) támogatási csapatával](https://thycotic.force.com/support/s/) felveheti a felhasználókat a titkos kiszolgáló (helyszíni) platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a titkos kiszolgáló (helyszíni) csempére kattint, automatikusan be kell jelentkeznie a titkos kiszolgálóra (helyszíni), amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)