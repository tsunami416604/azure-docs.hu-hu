---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Symantec webes biztonsági szolgáltatás (VSS) |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Symantec a webes biztonsági szolgáltatás (VSS) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6b9fdc6bf46cff1f3a38d40a4e7abad5bfe56c47
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57877836"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Oktatóanyag: Az Azure Active Directory-integráció Symantec webes biztonsági szolgáltatás (VSS)

Ebben az oktatóanyagban, megtudhatja, hogyan integrálható a Symantec a webes biztonsági szolgáltatás (VSS) fiók az Azure Active Directory (Azure AD-) fiókkal, hogy a WSS hitelesítheti a végfelhasználó az SAML-hitelesítés használata az Azure AD-ben üzembe helyezett és kényszerítése a felhasználói vagy csoport-szintű szabályzat előírásainak.

A Symantec a webes biztonsági szolgáltatás (VSS) integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Kezelheti a végfelhasználók és a csoportok az Azure AD portálon WSS fiókját használják.

- Lehetővé teszi a végfelhasználók számára, hogy az Azure AD hitelesítő adataikkal WSS hitelesítik magukat.

- Annak érdekében, a felhasználó engedélyezése, és csoportosíthatja a WSS-fiókban megadott szintű szabályzat előírásainak.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Symantec webes biztonsági szolgáltatás (VSS), a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* A Symantec a webes biztonsági szolgáltatás (VSS) egy bejelentkezés engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* A Symantec a webes biztonsági szolgáltatás (VSS) támogatja a **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>A Symantec a webes biztonsági szolgáltatás (VSS) hozzáadása a katalógusból

Az Azure AD-be a Symantec webes biztonsági szolgáltatás (VSS) integráció konfigurálásához, hozzá kell a Symantec a webes biztonsági szolgáltatás (VSS) a katalógusból a felügyelt SaaS-alkalmazások listájára.

**A Symantec a webes biztonsági szolgáltatás (VSS) hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Symantec webes biztonsági szolgáltatás (VSS)**, jelölje be **Symantec webes biztonsági szolgáltatás (VSS)** eredmény panelen kattintson a **Hozzáadás** gombra kattintva adhat hozzá a az alkalmazás.

     ![Symantec webes biztonsági szolgáltatás (VSS) a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Symantec webes biztonsági szolgáltatás (WSS) nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó a Symantec webhelyén biztonsági szolgáltatás (WSS) közötti kapcsolat kapcsolatot kell hozható létre.

Az Azure AD egyszeri bejelentkezés Symantec webes biztonsági szolgáltatás (VSS) tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **A Symantec a webes biztonsági szolgáltatás (VSS) egyszeri bejelentkezés konfigurálása** – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[A Symantec a webes biztonsági szolgáltatás (VSS) tesztfelhasználó létrehozása](#create-symantec-web-security-service-wss-test-user)**  – egy megfelelője a Britta Simon kell a Symantec webes biztonsági szolgáltatás (WSS), amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés beállítása a Symantec webes biztonsági szolgáltatás (VSS), hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Symantec webes biztonsági szolgáltatás (VSS)** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A Symantec a webes biztonsági szolgáltatás (VSS) tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be egy URL-címe: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Az a **válasz URL-cím** szövegmezőbe írja be egy URL-címe: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Ügyfél [Symantec webes biztonsági szolgáltatás (VSS) ügyfél-támogatási csapatának](https://www.symantec.com/contact-us) f a tartozó értékeket. a **azonosító** és **válasz URL-cím** valamilyen okból nem működnek... Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

### <a name="configure-symantec-web-security-service-wss-single-sign-on"></a>Symantec-Web Security szolgáltatás (VSS) egyszeri bejelentkezés konfigurálása

Egyszeri bejelentkezés beállítása a Symantec a webes biztonsági szolgáltatás (VSS) oldalon, tekintse meg a WSS online dokumentációt. A letöltött **összevonási metaadatainak XML** a WSS-portálon importálni kell. Forduljon a [Symantec webes biztonsági szolgáltatás (VSS) támogatási csoportjának](https://www.symantec.com/contact-us) kaphat segítséget a konfigurációval a WSS-portálon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be **brittasimon\@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés hozzáférést biztosít a Symantec webes biztonsági szolgáltatás (VSS) használja.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Symantec webes biztonsági szolgáltatás (VSS)**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **Symantec webes biztonsági szolgáltatás (VSS)**.

    ![A Symantec a webes biztonsági szolgáltatás (VSS) hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-symantec-web-security-service-wss-test-user"></a>A Symantec a webes biztonsági szolgáltatás (VSS) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon a Symantec webhelyén biztonsági szolgáltatás (WSS) nevű felhasználó létrehozásához. A megfelelő teljes felhasználónevet manuálisan létrehozott a WSS-portálon, vagy várja meg, néhány perc (~ 15 perc) után szinkronizálni kell a WSS-portálon az Azure AD-ben üzembe helyezett felhasználók/csoportok. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva. A végfelhasználói gép, webhelyek, tallózással használandó nyilvános IP-címét is szükség lesz a Symantec a webes biztonsági szolgáltatás (VSS) portálon.

> [!NOTE]
> Kattintson a [Itt](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) a gépet a nyilvános IP-cím.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni fogja az egyszeri bejelentkezés funkció most, hogy konfigurálta a WSS fiókkal az Azure AD a SAML-hitelesítés.

Miután konfigurálta a proxy-forgalom WSS, a böngészőben nyissa meg a webböngészőjét, és tallózással keresse meg a helyet próbál majd átirányítjuk az Azure bejelentkezési oldala. Adja meg a felhasználó hitelesítő adatait, a tesztelési célból, amelyet az Azure ad (azaz BrittaSimon) és a társított jelszót. A hitelesítést követően lesz a választott webhelyre navigálhat. Érdemes olyan szabályt hoz létre szabályzat BrittaSimon megakadályozza a böngészés egy adott helyre, majd lapnak kell megjelennie a WSS letiltása, tallózással keresse meg a helyet BrittaSimon felhasználóként megkísérlésekor a WSS oldalon.

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

