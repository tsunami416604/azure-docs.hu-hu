---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Meta hálózatok összekötővel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Meta hálózatok összekötő és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.openlocfilehash: a510fcce6b004e8384ce888ad421802bc144e73f
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2019
ms.locfileid: "56985971"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Oktatóanyag: Meta hálózatok összekötő az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Meta hálózatok összekötő integrálása az Azure Active Directory (Azure AD).
Meta hálózatok összekötő integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Meta hálózatok összekötő Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezve Meta hálózatok Connector (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Meta hálózatok összekötővel, a következő elemek szükségesek:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Meta hálózatok összekötő egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Meta hálózatok összekötő támogatja az **SP** és **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés
 
* Meta hálózatok összekötő támogatja az **igény szerinti** felhasználók átadása

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Meta hálózatok összekötő hozzáadása a katalógusból

Meta hálózatok összekötő integrálása az Azure AD beállításához szüksége Meta hálózatok összekötő hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Meta hálózatok összekötő hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Meta hálózatok összekötő**, jelölje be **Meta hálózatok összekötő** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Meta hálózatok összekötő a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés Meta hálózatok összekötővel nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó Meta hálózatok Connector közötti kapcsolat kapcsolatot kell hozható létre.

Az Azure AD egyszeri bejelentkezés Meta hálózatok összekötő tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Meta hálózatok összekötő egyszeri bejelentkezés konfigurálása](#configure-meta-networks-connector-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Meta hálózatok összekötő tesztfelhasználót](#create-meta-networks-connector-test-user)**  – egy megfelelője a Britta Simon Meta hálózatok összekötő, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezés Meta hálózatok összekötővel, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Meta hálózatok összekötő** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![Meta hálózatok összekötő tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Meta hálózatok összekötő tartomány és URL-címeket egyetlen bejelentkezési adatait](common/both-advanced-urls.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`

    b. Az a **továbbítási állapot** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítheti ezeket az értékeket a tényleges azonosítóval, válasz URL-cím, és a bejelentkezési URL-magyarázatát olvashatja az oktatóanyag későbbi részében.

6. Meta hálózatok összekötő alkalmazást a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg. Kattintson a **szerkesztése** ikonra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)
    
7. Emellett a fent Meta hálózatok összekötőalkalmazás vár néhány további attribútumok vissza SAML-válasz átadni. A a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen a következő lépésekkel adja hozzá a SAML-jogkivonat attribútumot, ahogyan az alábbi táblázatban:
    
    | Name (Név) | Forrásattribútum | Névtér|
    | ---------------| --------------- | -------- |
    | Keresztnév | user.givenname | |
    | Vezetéknév | user.surname | |
    | e-mail cím| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | név | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | telefon | user.telephonenumber | |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

8. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

9. Az a **Meta hálózatok Connector telepítése** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-meta-networks-connector-single-sign-on"></a>Meta hálózatok összekötő egyszeri bejelentkezés konfigurálása

1. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be a Meta hálózatok összekötő rendszergazdai fiókjával.
    
    > [!NOTE]
    > Meta hálózatok összekötő egy olyan biztonságos rendszer. Ezért a portál elérése előtt le szeretné kérni a nyilvános IP-cím engedélyezési listán az oldalon. A nyilvános IP-cím lekéréséhez kövesse az alábbi hivatkozással megadott [Itt](https://whatismyipaddress.com/). Az IP-cím küldése az [Meta hálózatokhoz csatlakozó ügyfél-támogatási csapatának](mailto:support@metanetworks.com) beolvasni az IP-cím engedélyezési listán.
    
2. Lépjen a **rendszergazda** válassza **beállítások**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure3.png)
    
3. Győződjön meg arról, hogy **Log internetes forgalom** és **kényszerített VPN MFA** vannak beállítva, hogy ki.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure1.png)
    
4. Lépjen a **rendszergazda** válassza **SAML**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure4.png)
    
5. Hajtsa végre a következő lépéseket a **részletek** oldalon:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure2.png)
    
    a. Másolás **egyszeri bejelentkezési URL-cím** értékét, és illessze be azt a **bejelentkezési URL** szövegmezőjébe a **Meta hálózatok összekötő tartomány és URL-címek** szakaszban.
    
    b. Másolás **címzett URL-cím** értékét, és illessze be azt a **válasz URL-cím** szövegmezőjébe a **Meta hálózatok összekötő tartomány és URL-címek** szakaszban.
    
    c. Másolás **célközönség-URI (SP entitás azonosítója)** értékét, és illessze be azt a **azonosító (entityid)** szövegmezőjébe a **Meta hálózatok összekötő tartomány és URL-címek** szakaszban.
    
    d. Az SAML engedélyezése
    
6. Az a **általános** lapon tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure5.png)

    a. Az a **identitás szolgáltató egyszeri bejelentkezési URL-cím**, illessze be a **bejelentkezési URL-cím** az Azure Portalról másolt érték.

    b. Az a **identitásszolgáltató kibocsátója**, illessze be a **az Azure AD-azonosító** az Azure Portalról másolt érték.

    c. Nyissa meg a letöltött tanúsítvány az Azure Portalról a Jegyzettömbben, illessze be azt a **X.509-tanúsítvány** szövegmezőbe.

    d. Engedélyezze a **Just-in-Time-kiépítés**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Meta hálózatok összekötő Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Meta hálózatok összekötő**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Meta hálózatok összekötő**.

    ![Az alkalmazások listáját a Meta hálózatok összekötő hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-meta-networks-connector-test-user"></a>Meta hálózatok összekötő tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó Meta hálózatok összekötőben jön létre. Meta hálózatok összekötő támogatja az just-in-time-kiépítés, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik a metaadat-hálózatok összekötőben, egy új jön létre, Meta hálózatok összekötő eléréséhez megkísérlésekor.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [Meta hálózatokhoz csatlakozó ügyfél-támogatási csapatának](mailto:support@metanetworks.com).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Meta hálózatok összekötő csempére kattint, akkor kell automatikusan megtörténik a az Adattisztítás hálózatok összekötőre, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

