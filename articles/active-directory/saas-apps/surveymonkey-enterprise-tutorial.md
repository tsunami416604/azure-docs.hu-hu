---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SurveyMonkey Enterprise-nal | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést a Azure Active Directory és a SurveyMonkey Enterprise között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 10/17/2019
ms.author: jeedes
ms.openlocfilehash: dd54a53db500457606b1e4bbe2073fe7b3e34215
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88533316"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-surveymonkey-enterprise"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SurveyMonkey Enterprise-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a SurveyMonkey Enterprise-t Azure Active Directory (Azure AD-val). Ha integrálja a SurveyMonkey Enterprise-t az Azure AD-vel, a következőket teheti:

* A SurveyMonkey Enterprise-hoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a SurveyMonkey Enterprise szolgáltatásba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SurveyMonkey Enterprise egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A SurveyMonkey Enterprise támogatja a **identitásszolgáltató** által KEZDEMÉNYEZett SSO-t

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-surveymonkey-enterprise-from-the-gallery"></a>SurveyMonkey Enterprise hozzáadása a katalógusból

A SurveyMonkey Enterprise Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a SurveyMonkey Enterprise-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **surveymonkey Enterprise** kifejezést a keresőmezőbe.
1. Válassza ki a **surveymonkey Enterprise** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-surveymonkey-enterprise"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a SurveyMonkey Enterprise-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a SurveyMonkey Enterprise használatával egy **B. Simon**nevű tesztelési felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a SurveyMonkey Enterprise kapcsolódó felhasználója között.

Az Azure AD SSO és a SurveyMonkey Enterprise konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[Surveymonkey vállalati egyszeri bejelentkezés konfigurálása](#configure-surveymonkey-enterprise-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy surveymonkey Enterprise test User](#create-surveymonkey-enterprise-test-user)** -t, hogy rendelkezzen a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-surveymonkey.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **surveymonkey Enterprise** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban az alkalmazás előre konfigurálva van, és a szükséges URL-címek már előre fel vannak töltve az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Save (Mentés** ) gombra kattintva.

1. A SurveyMonkey Enterprise-alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/edit-attribute.png)

6. A fentieken kívül a SurveyMonkey Enterprise alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre is fel vannak töltve, de a követelménynek megfelelően áttekintheti őket.

    | Name | Forrás attribútum|
    | ---------------| --------------- |
    | E-mail | User. mail |
    | FirstName | User. givenName |
    | LastName | felhasználó. vezetéknév |

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **surveymonkey Enterprise beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a SurveyMonkey Enterprise-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **surveymonkey Enterprise**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-surveymonkey-enterprise-sso"></a>SurveyMonkey vállalati egyszeri bejelentkezés konfigurálása

Ha az egyszeri bejelentkezést a **surveymonkey Enterprise** oldalon szeretné beállítani, a letöltött **összevonási METAADATOKat tartalmazó XML** -fájlt és a megfelelő másolt URL-címeket el kell küldenie a Azure Portalról a [surveymonkey Enterprise Support csapatnak](mailto:support@selerix.com). Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-surveymonkey-enterprise-test-user"></a>SurveyMonkey vállalati tesztelési felhasználó létrehozása

Tesztelési felhasználót nem kell létrehozni a SurveyMonkey Enterprise-ban. A felhasználói fiókokat a rendszer kiépíti, ha a felhasználó egy új fiókot hoz létre az SAML-állítás alapján. A SurveyMonkey Enterprise Customer Success Manager a folyamat elvégzéséhez szükséges lépéseket ismerteti, miután az Azure-metaadatok hozzá lettek adva a SurveyMonkey Enterprise-konfigurációhoz, és készen állnak az ellenőrzésre.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a SurveyMonkey Enterprise csempére kattint, akkor automatikusan be kell jelentkeznie a SurveyMonkey Enterprise-ba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A SurveyMonkey Enterprise kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)