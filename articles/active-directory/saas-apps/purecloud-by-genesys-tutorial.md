---
title: 'Oktatóanyag: Azure Active Directory integráció a PureCloud-mel a Genesys használatával | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést az Azure Active Directory és a PureCloud között a Genesys használatával.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: e16a46db-5de2-4681-b7e0-94c670e3e54e
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/25/2019
ms.author: jeedes
ms.openlocfilehash: 92df57737b75ab2c9bb9992dd3f223f55dbc39bb
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68826138"
---
# <a name="tutorial-azure-active-directory-integration-with-purecloud-by-genesys"></a>Oktatóanyag: Azure Active Directory integráció a PureCloud-mel a Genesys használatával

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a PureCloud a Genesys segítségével a Azure Active Directory (Azure AD) használatával.
A Genesys és az Azure AD PureCloud integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a Genesys-PureCloud.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a PureCloud a Genesys (egyszeri bejelentkezés) és az Azure AD-fiókjaik használatával.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Ha az Azure AD-integrációt a Genesys PureCloud szeretné konfigurálni, a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* PureCloud Genesys egyszeri bejelentkezéses előfizetés esetén

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A Genesys által támogatott PureCloud az **SP és a identitásszolgáltató** által kezdeményezett SSO-t támogatja

## <a name="adding-purecloud-by-genesys-from-the-gallery"></a>PureCloud hozzáadása a Genesys-ből a katalógusból

Ahhoz, hogy az Azure AD-be Genesys az PureCloud-integrációt, hozzá kell adnia a PureCloud a Genesys a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Az alábbi lépéseket követve adhat hozzá PureCloud a Genesys a katalógusból:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a következőt: **PureCloud by Genesys**, válassza a **PureCloud by Genesys** from result panelt, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![PureCloud Genesys szerint az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést konfigurálja és teszteli a Genesys PureCloud-mel, a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a Genesys által a PureCloud által a kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés az Genesys-mel történő PureCloud való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[PureCloud konfigurálása Genesys egyszeri bejelentkezéssel](#configure-purecloud-by-genesys-single-sign-on)** – az egyszeri bejelentkezési beállítások konfigurálása az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre PureCloud a Genesys test User használatával](#create-purecloud-by-genesys-test-user)** – hogy rendelkezzen egy, a PureCloud által az Azure ad-hez társított Britta
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Ha az Azure AD egyszeri bejelentkezést a Genesys PureCloud szeretné konfigurálni, hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **PureCloud az Genesys** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![PureCloud Genesys tartomány és URL-címek egyszeri bejelentkezési adatai alapján](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be a régiójának megfelelő URL-címet:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.au/saml` |

    b. A **Válasz URL-címe** szövegmezőbe írja be a régiójának megfelelő URL-címet:

    | |
    |--|
    | `https://login.mypurecloud.com/saml` |
    | `https://login.mypurecloud.de/saml` |
    | `https://login.mypurecloud.jp/saml` |
    | `https://login.mypurecloud.ie/saml` |
    | `https://login.mypurecloud.com.au/saml` |

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![PureCloud Genesys tartomány és URL-címek egyszeri bejelentkezési adatai alapján](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be a régiójának megfelelő URL-címet:
    
    | |
    |--|
    | `https://login.mypurecloud.com` |
    | `https://login.mypurecloud.de` |
    | `https://login.mypurecloud.jp` |
    | `https://login.mypurecloud.ie` |
    | `https://login.mypurecloud.com.au` |

6. A Genesys-alkalmazás által használt PureCloud egy adott formátumban várja az SAML-jogcímeket, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

7. A fentiek mellett a Genesys alkalmazás PureCloud néhány további attribútumot vár az SAML-válaszban. A **felhasználó attribútumai** párbeszédpanel **felhasználói** jogcímek szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon:

    | Name (Név) | Forrás attribútum|
    | ---------------| --------------- |
    | Email | user.userprinicipalname |
    | Cégnév | `Your organization name` |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

8. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

9. A **PureCloud szerinti beállítás Genesys** szakaszban adja meg a megfelelő URL (eke) t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-purecloud-by-genesys-single-sign-on"></a>PureCloud konfigurálása Genesys egyszeri bejelentkezéssel

1. Egy másik böngészőablakban jelentkezzen be, hogy a Genesys rendszergazdaként PureCloud a következőt:.

2. Kattintson a **rendszergazda** elemre a felső részen, és az integrációkterületen navigáljon az **egyszeri bejelentkezés** lehetőségre.

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure01.png)

3. Váltson az **ADFS/Azure ad (prémium)** lapra, és hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure02.png)

    a. Kattintson a **Tallózás** gombra, és töltse fel a Azure Portalból letöltött base-64 kódolt tanúsítványt az **ADFS**-tanúsítványba.

    b. Az **ADFS kiállítói URI** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    c. A **cél URI** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portal másolt.

    d. **Függő entitás azonosító** értékének megkezdéséhez lépjen a Azure Portal **PureCloud az Genesys** alkalmazás-integráció lapján, kattintson a **Tulajdonságok** fülre, és másolja az **alkalmazás-azonosító** értékét. Illessze be a **függő entitás azonosítójának** szövegmezőbe. 

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure06.png)

    e. Kattintson a **Save** (Mentés) gombra   

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A Felhasználónév mezőbe írja be a **brittasimon\@yourcompanydomain. Extension** **nevet**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy a Genesys által PureCloud hozzáférést biztosít.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **PureCloud a Genesys alapján**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **PureCloud a Genesys alapján**lehetőséget.

    ![A PureCloud by Genesys hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-purecloud-by-genesys-test-user"></a>PureCloud létrehozása a Genesys test User használatával

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a PureCloud a Genesys-be, azokat a Genesys PureCloud kell kiépíteni. A Genesys által PureCloud a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a PureCloud a Genesys rendszergazdaként.

2. Kattintson a felső **rendszergazda** lehetőségre, és navigáljon a személyek **& engedélyek**területen.

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure03.png)

3. A személyek lapon kattintson a **személy felvétele**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure04.png)

4. A **személyek hozzáadása a szervezethez** előugró ablakban végezze el a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/purecloud-by-genesys-tutorial/configure05.png)

    a. A **teljes név** szövegmezőbe írja be a felhasználó nevét (például **Brittasimon**).

    b. Az **e-mail** szövegmezőbe írja be a felhasználó, például a **brittasimon\@contoso.com**-e-mail-címét.
    
    c. Kattintson a **Create** (Létrehozás) gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a PureCloud by Genesys csempe elemre kattint, automatikusan be kell jelentkeznie a PureCloud olyan Genesys, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

