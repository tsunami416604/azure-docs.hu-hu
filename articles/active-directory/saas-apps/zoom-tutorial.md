---
title: 'Oktatóanyag: Azure Active Directory integráció a nagyítással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és nagyítás között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 0ebdab6c-83a8-4737-a86a-974f37269c31
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/08/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8e36d1bb91e70e21ee1940e189bfedaebafa4412
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68975949"
---
# <a name="tutorial-integrate-zoom-with-azure-active-directory"></a>Oktatóanyag: Nagyítás integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a nagyítást Azure Active Directory (Azure AD-val). Ha az Azure AD-vel integrálja a nagyítást, a következőket teheti:

* A nagyításhoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával való nagyításhoz.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A nagyítás támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-zoom-from-the-gallery"></a>Nagyítás hozzáadása a katalógusból

A nagyítás Azure AD-ba való integrálásának konfigurálásához hozzá kell adnia egy nagyítást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban a keresőmezőbe írja be a **Nagyítás** kifejezést.
1. Válassza az eredmények panel **Nagyítás** elemét, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-zoom"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a nagyításhoz

Konfigurálja és tesztelje az Azure AD SSO-t a nagyítással egy **B. Simon**nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a nagyításban.

Az Azure AD SSO nagyítással való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. A **[Nagyítás egyszeri](#configure-zoom-sso)** bejelentkezésének konfigurálása – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Nagyítási teszt felhasználó létrehozása](#create-zoom-test-user)** – a felhasználó Azure ad-beli képviseletéhez csatolt B. Simon-hez tartozó a nagyításban.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a nagyítási alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az alapszintű **SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.zoom.us`

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:`<companyname>.zoom.us`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. Az értékek lekéréséhez vegye fel a kapcsolatot a [nagyítási ügyfél támogatási csoportjával](https://support.zoom.us/hc/en-us) . Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

5. A nagyítási alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A fentieken kívül a nagyítási alkalmazás néhány további attribútumot vár az SAML-válaszban. A  **felhasználó attribútumai** **** párbeszédpanelfelhasználóijogcímekszakaszábanakövetkezőlépésekkeladjahozzáazSAML-jogkivonatattribútumátazalábbitáblázatbanlátható módon: 

    | Name (Név) | Névtér  |  Forrás attribútum|
    | ---------------| --------------- | --------- |
    | E-mail-cím  | user.mail  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/mail` |
    | Utónév  | User. givenName  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname` |
    | Vezetéknév  | felhasználó. vezetéknév  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname` |
    | Telefonszám  | User. telephoneNumber  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/phone` |
    | Részleg  | felhasználó. részleg  | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/department` |
    | role |    User. assignedrole |`http://schemas.xmlsoap.org/ws/2005/05/identity/claims/role` |

    > [!NOTE]
    > Ide kattintva [](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) megtudhatja, hogyan konfigurálhatja a szerepkört az Azure ad-ben

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Válassza a forrás **attribútumként**lehetőséget.

    d. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    e. Kattintson a **Ok**

    f. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > A nagyítás várhatóan az SAML-adattartalomban lévő csoportos jogcímet is megvárhatja, így ha bármilyen csoportot hozott létre, vegye fel a kapcsolatot a [nagyítási ügyfél támogatási csapatával](https://support.zoom.us/hc/en-us) a csoport adataival, hogy a csoport adatait is be lehessen állítani. Emellett meg kell adnia az objektumazonosító számára az [ügyfél-támogatási csapat nagyítását](https://support.zoom.us/hc/en-us) , hogy azok a végén is konfigurálhatók legyenek. Kérjük, kövesse a [dokumentumot](https://support.zoom.us/hc/en-us/articles/115005887566) az objektumazonosító beszerzéséhez.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. A **Nagyítás beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

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

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a nagyításhoz való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Nagyítás**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-zoom-sso"></a>A nagyítás egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a nagyítású vállalati webhelyre rendszergazdaként.

2. Kattintson az **egyszeri bejelentkezés** fülre.

    ![Egyszeri bejelentkezés lap](./media/zoom-tutorial/ic784700.png "Egyszeri bejelentkezés")

3. Kattintson a **Biztonság-vezérlés** fülre, majd lépjen az **egyszeri bejelentkezési** beállítások menüpontra.

4. Az egyszeri bejelentkezés szakaszban hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés szakasz](./media/zoom-tutorial/ic784701.png "Egyszeri bejelentkezés")

    a. A **bejelentkezési oldal URL-címe** szövegmezőbe illessze be az Azure Portalból másolt **bejelentkezési URL-címet** .

    b. A kijelentkezési **oldal URL-címéhez** lépjen a Azure Portalra, és kattintson a bal oldali **Azure Active Directoryra** , majd lépjen a **Alkalmazásregisztrációkre**.

    ![Az Azure Active Directory gomb](./media/zoom-tutorial/appreg.png)

    c. Kattintson a **végpontokra**

    ![A végpont gomb](./media/zoom-tutorial/endpoint.png)

    d. Másolja ki az **SAML-P** kijelentkezési végpontot, és illessze be a kijelentkezési **oldal URL-** szövegmezőbe.

    ![A másolási végpont gombja](./media/zoom-tutorial/endpoint1.png)

    e. Nyissa meg a Base-64 kódolású tanúsítványt a Jegyzettömbben, másolja a vágólapra a tartalmát, majd illessze be az identitás- **szolgáltatói tanúsítvány** szövegmezőbe.

    f. A **kiállító** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét. 

    g. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > További információért látogasson el a nagyítási dokumentációba[https://zoomus.zendesk.com/hc/articles/115005887566](https://zoomus.zendesk.com/hc/articles/115005887566)

### <a name="create-zoom-test-user"></a>Nagyítási teszt felhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezzenek a Nagyításba, a nagyításhoz kell őket kiépíteni. Nagyítás esetén a kiépítés manuális feladat.

### <a name="to-provision-a-user-account-perform-the-following-steps"></a>Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a **nagyítású** vállalati webhelyre rendszergazdaként.

2. Kattintson a **Fiókkezelés** lapra, majd a **felhasználói kezelés**elemre.

3. A felhasználói kezelés szakaszban kattintson a **felhasználók hozzáadása**elemre.

    ![Felhasználói felügyelet](./media/zoom-tutorial/ic784703.png "Felhasználói felügyelet")

4. A **felhasználók hozzáadása** oldalon hajtsa végre a következő lépéseket:

    ![Felhasználók hozzáadása](./media/zoom-tutorial/ic784704.png "Felhasználók hozzáadása")

    a. A **felhasználó típusa**területen válasszaaz alapszintű lehetőséget.

    b. Az **e-mailek** szövegmezőbe írja be a kiépíteni kívánt érvényes Azure ad-fiók e-mail-címét.

    c. Kattintson a **Hozzáadás**lehetőségre.

> [!NOTE]
> Az Azure Active Directory felhasználói fiókok kiépítéséhez a nagyítással elérhető bármely más nagyítású felhasználói fiók létrehozási eszközét vagy API-jait használhatja.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Amikor a hozzáférési panelen a nagyítás csempére kattint, automatikusan be kell jelentkeznie arra a nagyításra, amelyhez be szeretné állítani az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

