---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Catchpointtal'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Catchpoint között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: ab3eead7-8eb2-4c12-bb3a-0e46ec899d37
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/27/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7b19e286d299811a950df05f93d221bd710676ea
ms.sourcegitcommit: bd5fee5c56f2cbe74aa8569a1a5bce12a3b3efa6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80743498"
---
# <a name="tutorial-azure-active-directory-single-sign-on-integration-with-catchpoint"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési integrációja a Catchpointtal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Catchpointot az Azure Active Directoryval (Azure AD). Ha integrálja a Catchpointot az Azure AD-vel, a következőket teheti:

* Szabályozhatja a catchpoint hoz való felhasználói hozzáférést az Azure AD-ből.
* Engedélyezze az automatikus Catchpoint-bejelentkezést az Azure AD-fiókkal rendelkező felhasználók számára.
* Kezelje fiókjait egyetlen központi helyen: az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Catchpoint-előfizetés egyszeri bejelentkezéssel (SSO) engedélyezve.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A Catchpoint támogatja az SP által kezdeményezett és az IDP által kezdeményezett egyszeri szolgáltatót.
* Catchpoint támogatja a just-in-time (JIT) felhasználói kiépítés.
* A Catchpoint konfigurálása után kényszerítheti a munkamenet-vezérlést. Ez az óvintézkedés valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférés kiterjesztése. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="add-catchpoint-from-the-gallery"></a>Catchpoint hozzáadása a gyűjteményből

A Catchpoint Azure AD-be való integrációjának konfigurálásához adja hozzá a Catchpointot a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy munkahelyi, iskolai vagy személyes Microsoft-fiókkal.
1. A bal oldali ablaktáblán válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások lehetőséget,** és válassza **az Összes alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Catchpoint** kifejezést a keresőmezőbe.
1. Válassza **a Catchpoint** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-catchpoint"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a Catchpoint ban

Ahhoz, hogy az SSO működjön, össze kell kapcsolnia egy Azure AD-felhasználót egy catchpoint-i felhasználóval. Ebben az oktatóanyagban egy **B.Simon**nevű tesztfelhasználót állítunk be. 

Töltse ki a következő szakaszokat:

1. [Konfigurálja az Azure AD SSO](#configure-azure-ad-sso)konfigurálása, hogy engedélyezze ezt a funkciót a felhasználók számára.
    * [Hozzon létre egy Azure AD-tesztfelhasználót,](#create-an-azure-ad-test-user)hogy tesztelje az Azure AD egyszeri bejelentkezést B.Simonnal.
    * [Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)hogy b.Simon az Azure AD egyszeri bejelentkezést használhasson.
1. [A Catchpoint Egyszeri bejelentkezés konfigurálása](#configure-catchpoint-sso)az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
    * [Catchpoint tesztfelhasználó létrehozása](#create-a-catchpoint-test-user), a B.Simon Azure AD tesztfiók összekapcsolásának lehetővé tételezése egy hasonló felhasználói fiókkal a Catchpoint-ban.
1. [Tesztelje az SSO-t,](#test-sso)hogy ellenőrizze, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure Portalon az Azure AD SSO engedélyezéséhez:

1. Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)
1. A **Catchpoint** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon jelölje ki a toll ikonját az **egyszerű SAML konfigurációs** beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. A catchpoint által kezdeményezett mód konfigurálása:
   - **Az IDP**által kezdeményezett mód esetén adja meg a következő mezők értékeit:
     - **Azonosító**esetén:`https://portal.catchpoint.com/SAML2`
     - Válasz **URL-cím esetén**:`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`
   - **Sp**-initiated módban válassza **a További URL-ek beállítása** lehetőséget, és adja meg a következő értéket:
     - Bejelentkezési **URL esetén:**`https://portal.catchpoint.com/ui/Entry/SingleSignOn.aspx`

1. A Catchpoint alkalmazás elvárja az SAML állításokat egy adott formátumban. Egyéni attribútum-hozzárendelések hozzáadása az SAML token attribútumok konfigurációjához. Az alábbi táblázat az alapértelmezett attribútumok listáját tartalmazza:

    | Név | Forrás attribútum|
    | ------------ | --------- |
    | Utónév | user.givenneame |
    | vezetéknév; | user.vezetéknév |
    | E-mail cím | user.mail |
    | Név | user.userprincipalname |
    | Egyedi felhasználói azonosító | user.userprincipalname |

    ![Felhasználói attribútumok & jogcímlista képernyőképe](common/default-attributes.png)

1. Emellett a Catchpoint alkalmazás elvárja, hogy egy másik attribútumot kell átadni egy SAML-válaszban. Lásd az alábbi táblázatot. Ez az attribútum is előre kivan töltve, de áttekintheti és frissítheti, hogy megfeleljen a követelményeknek.

    | Név | Forrás attribútum|
    | ------------ | --------- |
    | névtér | user.assignedrole |

    > [!NOTE]
    > A `namespace` jogcímet a fiók nevével kell leképezni. Ezt a fióknevet be kell állítani egy szerepkörrel az Azure AD-ben, hogy saml válaszban kell visszaadni. Az Azure AD szerepköreiről további információt [a Vállalati alkalmazások SAML-jogkivonatában kiadott szerepkörjogcím konfigurálása című témakörben talál.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management)

1. Nyissa meg az **Egyszeri bejelentkezés beállítása SAML-lel** lapot. Az **SAML aláíró tanúsítvány szakaszban** keresse meg **a tanúsítványt (Base64)**. A Tanúsítvány számítógépre mentéséhez válassza a **Letöltés** lehetőséget.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Catchpoint beállítása** szakaszban másolja a későbbi lépésben szükséges URL-címeket.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban az Azure Portal használatával hozzon létre egy B.Simon nevű Azure AD-tesztfelhasználót.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Adja meg például a következőt: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet. Jegyezze fel a megjelenített jelszó értékét.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Catchpoint hozzáférést biztosítva.

1. Az Azure Portalon válassza a **Vállalati alkalmazások** > **minden alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Catchpoint lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** **párbeszédpanelen** a Felhasználók és csoportok lehetőséget.

    ![A "Felhasználó hozzáadása" hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználók listájából a **B.Simon** elemet. **Kattintson** a kijelölés gombra a képernyő alján.
1. Ha az SAML-feltételben szerepkörértéket vár, keresse meg a **Szerepkör kiválasztása** párbeszédpanelen, és válassza ki a felhasználó szerepkörét a listából. Kattintson a **kijelölés** gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

## <a name="configure-catchpoint-sso"></a>Catchpoint sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Catchpoint alkalmazásba.

1. Válassza a **Beállítások** ikont, majd az **SSO identitásszolgáltatót**.

    ![Catchpoint beállítások képernyőkép az SSO-identitásszolgáltató kijelölésével](./media/catchpoint-tutorial/configuration1.png)

1. Az **Egyszeri bejelentkezés** lapon adja meg a következő mezőket:

   ![Catchpoint Single Sign On oldal képernyőképe](./media/catchpoint-tutorial/configuration2.png)

   Mező | Érték
   ----- | ----- 
   **Namespace** | Érvényes névtérérték.
   **Identitásszolgáltató kiállítója** | Az `Azure AD Identifier` Azure Portal értéke.
   **Egyszeri bejelentkezés url-cím** | Az `Login URL` Azure Portal értéke.
   **Tanúsítvány** | A letöltött `Certificate (Base64)` fájl tartalma az Azure Portalról. A Jegyzettömb segítségével megtekintheti és másolhatja azt.

   Az **összevonási metaadat-XML-t** a **Metaadatok feltöltése** beállítás kiválasztásával is feltöltheti.

1. Kattintson a **Mentés** gombra.

### <a name="create-a-catchpoint-test-user"></a>Catchpoint tesztfelhasználó létrehozása

Catchpoint támogatja a just-in-time felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincsenek műveletelemek. Ha B.Simon még nem létezik felhasználóként a Catchpoint, ez jön létre a hitelesítés után.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a Saját alkalmazások portálhasználatával teszteli.

Ha kiválasztja a Catchpoint csempe a My Apps portálon, akkor automatikusan be kell jelentkeznie a Catchpoint alkalmazás sso konfigurálva. A Saját alkalmazások portálról további információt a [Bejelentkezés és az alkalmazások indítása a Saját alkalmazások portálról című témakörben](https://docs.microsoft.com/azure/active-directory/user-help/my-apps-portal-end-user-access)talál.

> [!NOTE]
> Amikor a bejelentkezési lapon keresztül jelentkezik be a Catchpoint alkalmazásba, a **Catchpoint hitelesítő adatok**megadása után adja meg az érvényes **névtérértéket** a **Vállalati hitelesítő adatok (SSO)** mezőben, és válassza a **Bejelentkezés lehetőséget.**
> 
> ![Catchpoint konfigurációja](./media/catchpoint-tutorial/loginimage.png)

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directoryval való integrálásáról szóló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Catchpointot az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
