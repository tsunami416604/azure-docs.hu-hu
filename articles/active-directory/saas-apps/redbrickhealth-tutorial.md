---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az RedBrick Health |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés RedBrick állapotát és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 26290c65-9aa3-42ab-8ba5-901b14dc8e73
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/3/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 52d2952ba838d69e94ebf6b009086888091e0264
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57778059"
---
# <a name="tutorial-azure-active-directory-integration-with-redbrick-health"></a>Oktatóanyag: Az Azure Active Directory-integráció az RedBrick Health

Ebben az oktatóanyagban elsajátíthatja, hogyan RedBrick egészségügyi integrálása az Azure Active Directory (Azure AD).
RedBrick egészségügyi integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá RedBrick Health az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve RedBrick Health (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az RedBrick Health, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* RedBrick egészségügyi egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.


* RedBrick Health támogatja **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés




## <a name="adding-redbrick-health-from-the-gallery"></a>RedBrick állapotfigyelő hozzáadása a katalógusból

RedBrick egészségügyi integrálása az Azure AD beállítása, hozzá kell RedBrick egészségügyi a galériából a felügyelt SaaS-alkalmazások listájára.

**RedBrick állapotfigyelő hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **RedBrick egészségügyi**, jelölje be **RedBrick egészségügyi** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában redBrick állapota](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az úgynevezett tesztfelhasználó alapján RedBrick Health **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó a RedBrick Health segítségével elérhető hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés az RedBrick Health tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[RedBrick egészségügyi egyszeri bejelentkezés konfigurálása](#configure-redbrick-health-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre RedBrick egészségügyi tesztfelhasználót](#create-redbrick-health-test-user)**  – egy megfelelője a Britta Simon RedBrick állapotát, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása az RedBrick Health, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **RedBrick egészségügyi** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![RedBrick egészségügyi tartomány és URL-címek egyszeri bejelentkezési adatait](common/idp-relay.png)

    a. Az a **azonosító** szövegmezőbe írja be egy URL-címe: `https://www.redbrickhealth.com`

    b. Az a **válasz URL-cím** szövegmezőbe írja be egy URL-címe: `https://sso-intg.redbrickhealth.com/sp/ACS.saml2`

    Éles környezetben: `https://sso.redbrickhealth.com/sp/ACS.saml2`

    c. Kattintson a **további URL-címet beállítani**.

    d. Az a **továbbítási állapot** szövegmezőbe írja be a következő minta használatával URL-cím: `https://api-sso2.redbricktest.com/identity/sso/nbound?target=https://vanity9-sso2.redbrickdev.com/portal&connection=<companyname>conn1`

    > [!NOTE]
    > Továbbítási állapot értéke nem valódi. Frissítse a tényleges továbbítási állapot ezt az értéket. Kapcsolattartó [RedBrick állapot ügyfél-támogatási csapatának](https://home.redbrickhealth.com/contact/) lekérni ezt az értéket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. RedBrick egészségügyi alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

6. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Name (Név) | Adatforrás-attribútum|
    | ---------------| --------------- | --------- |
    | Egyszerű név | ********** |
    | ügyfél-azonosító | ********** |
    | a résztvevő azonosítója | ********** |

    > [!NOTE]
    > Ezek az értékek csak hivatkozási célból. Meg kell meghatároznia az attribútum a szervezeti követelmények alapján. Vegye fel a kapcsolatot [RedBrick Health támogatási csapatának](https://home.redbrickhealth.com/contact/) a szükséges jogcímek kapcsolatos további információért.

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

7. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

8. Az a **RedBrick állapotának beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-redbrick-health-single-sign-on"></a>RedBrick egészségügyi egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálása **RedBrick egészségügyi** oldalon kell küldenie a letöltött **tanúsítvány (Base64)** és az Azure Portalról másolt URL-címek megfelelő [RedBrick egészségügyi támogatása csapat](https://home.redbrickhealth.com/contact/). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használandó Azure egyszeri bejelentkezést a hozzáférés biztosításával RedBrick állapotába.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **RedBrick egészségügyi**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **RedBrick egészségügyi**.

    ![Az alkalmazások listájában a RedBrick Health-hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-redbrick-health-test-user"></a>RedBrick egészségügyi tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű RedBrick állapota a felhasználó hoz létre. Együttműködve [RedBrick Health támogatási csapatának](https://home.redbrickhealth.com/contact/) a felhasználók hozzáadása az RedBrick egészségügyi platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha rákattint a RedBrick állapota csempe a hozzáférési panelen, meg kell automatikusan megtörténik a a RedBrick állapotába, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

