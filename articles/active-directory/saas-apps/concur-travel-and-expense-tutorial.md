---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a egyetértett utazással és költségekkel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory között, és egyetért az utazással és a költségekkel.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6401ace4-71c0-4778-8b15-900f2f5f0c4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dddd9f6904aa5ef7840850792aeabf04666dddc
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72373414"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a egyetértett utazással és költségekkel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a belefoglalt utazási és költségeiket Azure Active Directory (Azure AD-val). Az Azure AD-vel való együttműködés és költségek integrálásával a következőket teheti:

* A hozzáférés az Azure AD-ben, amely az utazáshoz és a költségekhez fér hozzá.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy az Azure AD-fiókjával való részvételre és a költségekre is megtartsák
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egyetért az utazási és a költségek előfizetésével.
* A "céges rendszergazda" szerepkör az Ön által birtokolt felhasználói fiókban. Tesztelheti, hogy rendelkezik-e a megfelelő hozzáféréssel, ha a megtartja az [egyszeri bejelentkezés önkiszolgáló eszközét](https://www.concursolutions.com/nui/authadmin/ssoadmin). Ha nem rendelkezik hozzáféréssel, lépjen kapcsolatba a partneri támogatással vagy a megvalósítás projekt kezelőjével. 

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését végzi.

* Az utazás és a költségek egyetértenek a **identitásszolgáltató** és az **SP** által kezdeményezett SSO-val
* Az utazás és a költségek egyetértenek az egyszeri bejelentkezés tesztelésével mind az üzemi, mind a megvalósítási környezetben 

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték a következő három régióban: USA, EMEA és Kína. Így csak egy példány konfigurálható az egyes bérlők mindegyik régiójában. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>A katalógusban való megváltozások és költségek hozzáadása

Ha úgy szeretné konfigurálni az integrációt, hogy egyetért az Azure AD-val való utazással és költségekkel, hozzá kell adnia a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a következőt: **egybeesik Travel és költségelszámolás** a keresőmezőbe.
1. Válassza az **utazás és költségek** az eredmények panelen lehetőséget, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-concur-travel-and-expense"></a>Azure AD-beli egyszeri bejelentkezés konfigurálása és tesztelése a megkerülő utazáshoz és költségekhez

Konfigurálja és tesztelje az Azure AD SSO-t, és használja a " **B. Simon**" nevű tesztet. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között, amely egyetért az utazással és a költségekkel kapcsolatban.

Ha az Azure AD SSO-t az utazással és a költségekkel együtt szeretné konfigurálni és tesztelni, végezze el a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A " **[egyetértő utazás és költség SSO" beállítása](#configure-concur-travel-and-expense-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre belefoglalt utazási és költségelszámolás-tesztelési felhasználót](#create-concur-travel-and-expense-test-user)** – hogy a B. Simon partnere a felhasználó Azure ad-beli képviseletéhez kapcsolódó utazási és költségekkel rendelkezzen.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **utazás és költség** alkalmazás integrációja oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfigurációs** szakaszban az alkalmazás előre konfigurálva van a **identitásszolgáltató** által kezdeményezett módban, és a szükséges URL-címek már előre fel vannak töltve az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Save (Mentés** ) gombra kattintva.

    > [!NOTE]
    > Az azonosító (Entity ID) és a válasz URL-címe (a fogyasztói szolgáltatás URL-címe) a régióra jellemző. Válasszon a saját egyetértő entitás adatközpontja alapján. Ha nem ismeri az Ön egyetértő entitásának adatközpontját, vegye fel a kapcsolatot a egyetértek támogatási szolgálatával. 

5. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon a beállítások szerkesztéséhez kattintson a Szerkesztés/toll ikonra a **felhasználói attribútumhoz** . Az egyedi felhasználói azonosítónak egyeznie kell a felhasználói login_id. A **User. userPrincipalName** általában módosítania kell a User. **mail**.

    ![Felhasználói attribútum szerkesztése](common/edit-attribute.png)

6. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a metaadatok letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az utazás és a költségek megadásával való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **egyetért az utazás és a költség**lehetőséggel.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-concur-travel-and-expense-sso"></a>A "egyetértő" utazás és a költségek egyszeri bejelentkezésének konfigurálása

1. Ha be szeretné állítani az egyszeri bejelentkezést a **egyetértő utazás és a költség** oldalon, fel kell töltenie a letöltött **összevonási metaadatokat tartalmazó XML-fájlt** , hogy az [SSO önkiszolgáló eszközre](https://www.concursolutions.com/nui/authadmin/ssoadmin) lépjen, és a "vállalati rendszergazda" szerepkörrel rendelkező fiókkal jelentkezzen be. 

1. Kattintson a **Hozzáadás** parancsra.
1. Adja meg a identitásszolgáltató egyéni nevét, például: "Azure AD (US)". 
1. Kattintson az **XML-fájl feltöltése** elemre, és csatolja a korábban letöltött **összevonási METAADATOKAT tartalmazó XML-** fájlt.
1. A módosítás mentéséhez kattintson a **metaadatok hozzáadása** elemre.

    ![Az egyszeri bejelentkezéses önkiszolgáló eszköz képernyőképe](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>A Create egyetért az utazási és a költségek tesztelése felhasználóval

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre, és egyetért az utazással és a költségekkel. A egyetértő támogatási csapattal együttműködve veheti fel a felhasználókat a egyetértett utazási és költség platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. 

> [!NOTE]
> B. Simon egyetértett bejelentkezési azonosítójának meg kell egyeznie a B. Simon egyedi azonosítójával az Azure AD-ben. Ha például a B. Simon Azure AD egyedi termékazonosító `B.Simon@contoso.com`. B. Simon egyetértett bejelentkezési azonosítójának `B.Simon@contoso.com` is kell lennie. 

## <a name="configure-concur-mobile-sso"></a>Az egybeesik Mobile SSO konfigurálása
A megtagadott mobil egyszeri bejelentkezés engedélyezéséhez meg kell adnia a támogatási csoport **felhasználói hozzáférésének URL-címét**. Kövesse az alábbi lépéseket a **felhasználói hozzáférés URL-címének** lekéréséhez az Azure ad-ből:
1. **Vállalati alkalmazások** ugrása
1. Kattintson **a egyetértek utazási és költségek** elemre.
1. Kattintson a **Tulajdonságok** elemre.
1. **Felhasználói hozzáférési URL-cím** másolása és az URL-cím megadása a támogatás megadásához

> [!NOTE]
> Az egyszeri bejelentkezés konfigurálására szolgáló önkiszolgáló beállítás nem érhető el, ezért az összevont támogatási csapattal együttműködve engedélyezheti a mobil egyszeri bejelentkezést. 

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen az egyezményes utazás és ráfordítás csempére kattint, automatikusan be kell jelentkeznie a egyetértett utazásra és költségekre, amelyekhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az utazást és a költségeket az Azure AD-vel](https://aad.portal.azure.com/)

