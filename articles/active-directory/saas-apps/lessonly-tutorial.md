---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja Lesson.ly | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Lesson.ly között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8c9dc6e6-5d85-4553-8a35-c7137064b928
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 02/12/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e7911f52d71501324a1b05c290402cc2ee33a706
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77370414"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-lessonly"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja Lesson.ly

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja Lesson.ly az Azure Active Directoryval (Azure AD). Ha integrálja Lesson.ly az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér Lesson.ly.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve Lesson.ly az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Lesson.ly egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Lesson.ly támogatja az **SP** által kezdeményezett SSO-t
* Lesson.ly támogatja a **Just In Time** felhasználói kiépítést
* A Lesson.ly konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáféréstől terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-lessonly-from-the-gallery"></a>Lesson.ly hozzáadása a galériából

A Lesson.ly Azure AD-be való integrálásának konfigurálásához hozzá kell adnia Lesson.ly a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be **Lesson.ly** a keresőmezőbe.
1. Válassza **ki Lesson.ly** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-lessonly"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését Lesson.ly

Konfigurálja és tesztelje az Azure AD-sSO-t Lesson.ly egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó Lesson.ly.

Az Azure AD-sSO konfigurálásához és teszteléséhez Lesson.ly hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja Lesson.ly egyszeri](#configure-lessonly-sso)** bejelentkezési beállításokat az alkalmazás oldalon.
    * **[Hozzon létre Lesson.ly tesztfelhasználót](#create-lessonly-test-user)** – b.Simon megfelelőjét Lesson.ly, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Lesson.ly** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.lessonly.com/auth/saml`

    > [!NOTE]
    > Általános névre való hivatkozáskor a **vállalatnevet** tényleges névvel kell helyettesíteni.
    
    b. A **Válasz URL -címe (Helyességi feltétel ügyfélszolgálatának URL-címe)** mezőbe írjon be egy URL-t a következő minta használatával:`https://<companyname>.lessonly.com/auth/saml/callback`

    c. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.lessonly.com/auth/saml/metadata`
    
    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel, a Válasz URL-címével és az azonosítóval. Lépjen kapcsolatba [Lessonly.com ügyféltámogatási csapatával,](mailto:support@lessonly.com) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Lesson.ly alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül Lesson.ly alkalmazás azt várja, hogy néhány további attribútumot kell visszaadni az SAML válaszban, amelyek az alábbiakban láthatók. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.

    | Név | Forrás attribútuma|
    | ---------------  | ----------------|
    | urna:oid:2.5.4.42 | user.givenname |
    | urna:oid:2.5.4.4  | user.vezetéknév |
    | urna:oid:0.9.2342.19200300.100.1.3 | user.mail |
    | urna:oid:1.3.6.1.4.1.5923.1.1.1.10 | user.objectid |

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Beállítás Lesson.ly** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t, hozzáférést biztosítva Lesson.ly.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza **a Lesson.ly**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-lessonly-sso"></a>Lesson.ly sso konfigurálása

Az egyszeri bejelentkezés konfigurálásához **Lesson.ly** oldalon el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-címeket az Azure Portalról [Lesson.ly támogatási csapatnak.](mailto:support@lessonly.com) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-lessonly-test-user"></a>Lesson.ly tesztfelhasználó létrehozása

Ennek a szakasznak az a célja, hogy hozzon létre egy felhasználó nevű B.Simon Lessonly.com. Lessonly.com támogatja a just-in-time kiépítése, amely alapértelmezés szerint engedélyezve van.

Ebben a szakaszban nincs műveletelem. Új felhasználó jön létre a Lessonly.com elérésére tett kísérlet során, ha még nem létezik.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, kapcsolatba kell lépnie a [Lessonly.com támogatási csapatával.](mailto:support@lessonly.com)

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen a Lesson.ly csempére kattint, automatikusan be kell jelentkeznie arra a Lesson.ly, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki Lesson.ly az Azure AD-vel](https://aad.portal.azure.com/)