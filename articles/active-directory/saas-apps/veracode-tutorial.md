---
title: 'Oktatóanyag: Azure Active Directory integráció a Veracode-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és Veracode között.
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
ms.date: 04/05/2019
ms.author: jeedes
ms.openlocfilehash: 67b4d4842bfa330e51244cee0d9ad8ad0ab6a24f
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68825956"
---
# <a name="tutorial-azure-active-directory-integration-with-veracode"></a>Oktatóanyag: Azure Active Directory integráció a Veracode

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Veracode a Azure Active Directory (Azure AD) szolgáltatással.
A Veracode és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Veracode.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a Veracode (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció Veracode való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Veracode egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Veracode támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

* A Veracode **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-veracode-from-the-gallery"></a>Veracode hozzáadása a gyűjteményből

A Veracode Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Veracode a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha Veracode szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Veracode**kifejezést, válassza a **Veracode** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Veracode az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az Veracode-mel konfigurálja és teszteli a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Veracode kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés Veracode való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Veracode egyszeri bejelentkezés konfigurálása](#configure-veracode-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Veracode-teszt felhasználót](#create-veracode-test-user)** – hogy a Veracode Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés Veracode való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **Veracode** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania egy lépést, mivel az alkalmazás már előre integrálva van az Azure-ban.

    ![Veracode tartomány és URL-címek egyszeri bejelentkezési adatai](common/preintegrated.png)

5. A Veracode alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A fentieken kívül a Veracode alkalmazás néhány további attribútumot vár, amelyeket az SAML-válaszban vissza kell adni. A **felhasználó attribútumai** párbeszédpanel **felhasználói** jogcímek szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:

    | Name (Név) | Forrás attribútum|
    | ---------------| --------------- |
    | FirstName |User. givenName |
    | LastName |Felhasználó. vezetéknév |
    | email |User. mail |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

7. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

8. A **Veracode beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-veracode-single-sign-on"></a>Veracode egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Veracode vállalati webhelyre rendszergazdaként.

2. A felső menüben kattintson a **Beállítások**, majd a **rendszergazda**elemre.
   
    ![Felügyelet](./media/veracode-tutorial/ic802911.png "Felügyelet")

3. Kattintson az **SAML** fülre.

4. A **szervezet SAML-beállításai** szakaszban hajtsa végre a következő lépéseket:
   
    ![Felügyelet](./media/veracode-tutorial/ic802912.png "Felügyelet")
   
    a.  A **kiállító** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.
    
    b. A letöltött tanúsítvány Azure Portalból való feltöltéséhez kattintson a **fájl kiválasztása**lehetőségre.
   
    c. Válassza a **saját regisztráció engedélyezése**lehetőséget.

1. Az **Önregisztráció beállításai** szakaszban hajtsa végre az alábbi lépéseket, majd kattintson a **Save (Mentés**) gombra:
   
    ![Felügyelet](./media/veracode-tutorial/ic802913.png "Felügyelet")
   
    a. **Új felhasználói aktiválás**esetén válassza a **nincs szükség aktiválásra**lehetőséget.
   
    b. A **felhasználói**adatfrissítések elemnél válassza a **Veracode felhasználói adatvédelme**lehetőséget.
   
    c. Az **SAML-attribútumok részleteihez**válassza ki a következőket:
      * **Felhasználói szerepkörök**
      * **Házirend rendszergazdája**
      * **Felülvizsgáló**
      * **Biztonsági érdeklődő**
      * **Végrehajtó**
      * **Beküldő**
      * **Creator**
      * **Minden vizsgálat típusa**
      * **Csoport tagsága**
      * **Alapértelmezett csapat**

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be brittasimon@yourcompanydomain.extensiona nevet. Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a Veracode hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **Veracode**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **Veracode**lehetőséget.

    ![Az Veracode hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-veracode-test-user"></a>Veracode-tesztelési felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a Veracode, a Veracode-ben kell kiépíteni őket. Veracode esetén a kiépítés automatizált feladat. Nincs feltételhez tartozó művelet. A felhasználók szükség esetén automatikusan létrejönnek az első egyszeri bejelentkezés megkísérlése során.

> [!NOTE]
> Az Azure AD felhasználói fiókjainak kiépítéséhez bármilyen más, a Veracode által biztosított Veracode felhasználói fiók létrehozására szolgáló eszközt vagy API-t használhat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Veracode csempére kattint, automatikusan be kell jelentkeznie arra a Veracode, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

