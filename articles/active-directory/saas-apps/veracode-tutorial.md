---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Veracode-dal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Veracode között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4fe78050-cb6d-4db9-96ec-58cc0779167f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcec326ddab1e74f43e1bb7ef446998a40799fd0
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73043560"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-veracode"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Veracode-dal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Veracode-ot az Azure Active Directoryval (Azure AD). Ha integrálja a Veracode-ot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Veracode.Control in Azure AD who has access to Veracode.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve legyenek a Veracode-ba az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen: az Azure Portalon.

Ha többet szeretne megtudni a szoftverszolgáltatásként (SaaS) alkalmazásintegrációról az Azure AD-vel, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* A Veracode egyszeri bejelentkezés (SSO)-kompatibilis előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. A Veracode támogatja az identitásszolgáltató által kezdeményezett egyszeri bejelentkezést és a just-in-time felhasználói kiépítést.

## <a name="add-veracode-from-the-gallery"></a>Veracode hozzáadása a gyűjteményből

A Veracode azure AD-be való integrálásának konfigurálásához adja hozzá a Veracode-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a Minden **alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a "Veracode" kifejezést a keresőmezőbe.
1. Válassza a **Veracode** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-veracode"></a>Az Azure AD egyszeri bejelentkezésének konfigurálása és tesztelése a Veracode-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a Veracode segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolatot egy Azure AD-felhasználó és a veracode kapcsolódó felhasználója között.

Az Azure AD SSO Veracode-mal való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
    * **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B.Simon nal.
    * **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b.Simon az Azure AD egyszeri bejelentkezést használhasson.
1. Állítsa be a **[Veracode egyszeri bejelentkezési](#configure-veracode-sso)** beállításokat az alkalmazás oldalon.
    * **[Hozzon létre egy Veracode-tesztfelhasználót,](#create-veracode-test-user)** hogy a Veracode-beli B.Simon megfelelője kapcsolódjon a felhasználó Azure AD-ábrázolásához.
1. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Veracode** alkalmazásintegrációs lapon keresse meg a **Kezelés** szakaszt. Válassza **ki az egyszeri bejelentkezést**.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon válassza az **egyszerű SAML-konfiguráció** ceruza ikonját a beállítások szerkesztéséhez.

   ![Az egyszeri bejelentkezés beállítása SAML-lel felirattal, kiemelve a ceruzaikont](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció szakaszban** az alkalmazás előre konfigurált, és a szükséges URL-címek már előre kitöltött az Azure-ban. Kattintson a **Mentés** gombra.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64) című részt.** A **Letöltés gombra** a tanúsítvány letöltéséhez és a számítógépre való mentéséhez válassza a Letöltés lehetőséget.

    ![Képernyőkép az SAML aláíró tanúsítvány szakaszról, kiemelve a Letöltés hivatkozással](common/certificatebase64.png)

1. A Veracode az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumkonfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![Képernyőkép a Felhasználói attribútumok & jogcímek szakaszról](common/default-attributes.png)

1. A Veracode arra is számít, hogy még néhány attribútum visszakerül az SAML-válaszba. Ezek az attribútumok is előre kivannak töltve, de áttekintheti őket a követelmények szerint.

    | Név | Forrás attribútum|
    | ---------------| --------------- |
    | Utónév |User.givenname |
    | Vezetéknév |Felhasználó.vezetéknév |
    | e-mail |Felhasználó.mail |

1. A **Veracode beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Képernyőkép: Veracode beállítása szakasz, kiemelve a konfigurációs URL-címek](common/copy-configuration-urls.png)

## <a name="configure-veracode-sso"></a>Veracode sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Veracode vállalati webhelyére rendszergazdaként.

1. A felső menüben válassza a **Beállítások** > **rendszergazda lehetőséget.**
   
    ![Képernyőkép a Veracode-felügyelet ről, kiemelve a Beállítások ikont és a Rendszergazda beállítást](./media/veracode-tutorial/ic802911.png "Adminisztráció")

1. Válassza az **SAML** lapot.

1. A **Szervezeti SAML-beállítások** csoportban hajtsa végre az alábbi lépéseket:

    ![Képernyőkép a Szervezeti SAML beállításai szakaszról](./media/veracode-tutorial/ic802912.png "Adminisztráció")

    a.  A **Kiállító**esetén illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    b. A **helyességi feltétel aláíró tanúsítvány,** válassza **a Fájl kiválasztása** a letöltött tanúsítvány feltöltéséhez az Azure Portalon.

    c. Self **Registration**esetén válassza **az Önregisztráció engedélyezése**lehetőséget.

1. Az **Önregisztrálási beállítások** csoportban hajtsa végre a következő lépéseket, majd válassza a **Mentés**lehetőséget:

    ![Képernyőkép: Önregisztrációs beállítások szakasz, a különböző beállítások kiemelve](./media/veracode-tutorial/ic802913.png "Adminisztráció")

    a. Az **Új felhasználó aktiválása**esetén válassza a **Nincs szükséges aktiválás lehetőséget.**

    b. A **felhasználói adatok frissítéséhez**válassza **a Felhasználói adatok beállítását.**

    c. Az **SAML attribútum részletei területen**válassza a következőket:
      * **Felhasználói szerepkörök**
      * **Házirend-rendszergazda**
      * **Felülvizsgáló**
      * **Biztonsági érdeklődő**
      * **Vezető**
      * **Beküldő**
      * **Létrehozó**
      * **Minden beszkéstípus**
      * **Csapattagságok**
      * **Alapértelmezett csapat**

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory** >**felhasználói** > **minden felhasználó lehetőséget.**
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:

   1. A **Név** `B.Simon`mezőbe írja be a mezőbe.  
   1. A **Felhasználónév**mezőbe username@companydomain.extensionírja be a. Például: `B.Simon@contoso.com`.
   1. Válassza **a Jelszó megjelenítése**lehetőséget, majd írja le a megjelenített értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze b.Simon azure egyszeri bejelentkezés a Veracode-hoz való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások** > **minden alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Veracode**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![Képernyőkép: Kezelés szakasz, kiemelt Felhasználók és csoportok](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Felhasználók és csoportok**lehetőséget.

    ![Képernyőkép: Felhasználók és csoportok lap, kiemelt Felhasználó hozzáadása](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanel **Felhasználók**csoportjában válassza a **B.Simon**lehetőséget. Ezután válassza a **Kijelölés** lehetőséget a képernyő alján.
1. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. Ezután válassza a **Kijelölés** lehetőséget a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

### <a name="create-veracode-test-user"></a>Veracode-tesztfelhasználó létrehozása

A Veracode-ba való bejelentkezéshez az Azure AD-felhasználóknak ki kell építeniük a Veracode-ba. Ez a feladat automatikus, és nem kell semmit manuálisan csinálnia. A felhasználók szükség esetén automatikusan létrejönnek az első egyszeri bejelentkezési kísérlet során.

> [!NOTE]
> A Veracode felhasználói fiókok létrehozása bármely más, a Veracode által biztosított api-k at használhat az Azure AD felhasználói fiókok kiépítéséhez.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelhasználatával teszteli.

Amikor a Hozzáférési panelen a **Veracode lehetőséget választja,** automatikusan be kell jelentkeznie arra a Veracode-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directoryval való integrálásáról szóló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Veracode-ot az Azure AD-vel](https://aad.portal.azure.com/)