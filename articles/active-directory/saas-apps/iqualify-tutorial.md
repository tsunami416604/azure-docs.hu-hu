---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az iQualify LMS-szel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az iQualify LMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: a1db4784eb63df14b7e7971d0273512ba657df96
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68944991"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Oktatóanyag: Az Azure Active Directory integrációja az iQualify LMS-szel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az iQualify LMS-t az Azure Active Directoryval (Azure AD).
Az iQualify LMS integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az iQualify LMS-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az iQualify LMS (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az iQualify LMS rendszerrel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* iQualify LMS egyszeri bejelentkezéssel rendelkező előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az iQualify LMS támogatja az **SP és az IDP** által kezdeményezett sso-t
* Az iQualify LMS támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-iqualify-lms-from-the-gallery"></a>IQualify LMS hozzáadása a galériából

Az iQualify LMS Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az iQualify LMS-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha hozzá szeretné adni az iQualify LMS-t a galériából, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **iQualify LMS**parancsot, válassza az **iQualify LMS** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![iQualify LMS az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az iQualify LMS-szel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó közötti kapcsolat iQualify LMS létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az iQualify LMS szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az iQualify LMS Single Sign-On](#configure-iqualify-lms-single-sign-on)** --t az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre iQualify LMS tesztfelhasználó](#create-iqualify-lms-test-user)** – egy megfelelője Britta Simon az iQualify LMS, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához az iQualify LMS szolgáltatással hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **iQualify LMS-alkalmazásintegrációs** lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban Ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépéseket:

    ![az iQualify LMS-tartomány és url-ek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:
    | |
    |--|--|
    | Termelési környezet:`https://<yourorg>.iqualify.com/`|
    | Tesztkörnyezet:`https://<yourorg>.iqualify.io`|

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:
    | |
    |--|--|
    | Termelési környezet:`https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Tesztkörnyezet:`https://<yourorg>.iqualify.io/auth/saml2/callback` |

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![az iQualify LMS-tartomány és url-ek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:
    | |
    |--|--|
    | Termelési környezet:`https://<yourorg>.iqualify.com/login` |
    | Tesztkörnyezet:`https://<yourorg>.iqualify.io/login` |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [az iQualify LMS ügyféltámogatási csapatával,](https://www.iqualify.com/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

6. Az iQualify LMS-alkalmazás egy adott formátumban várja az SAML-állításokat, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **Felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

7. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek** szakaszában a **Szerkesztés ikon** használatával szerkesztse a jogcímeket, vagy adja hozzá a jogcímeket az **Új jogcím hozzáadása** paragrafussal az SAML token attribútum konfigurálásához a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    | Név | Forrás attribútuma|
    | --- | --- |
    | e-mail | user.userprincipalname |
    | first_name | user.givenname |
    | last_name | user.vezetéknév |
    | person_id | "az ön tulajdonsága" |

    a. Kattintson **az Új jogcím hozzáadása** gombra a Felhasználói **jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **Név** mezőbe írja be a sor attribútumnevét.

    c. Hagyja üresen a **névteret.**

    d. Válassza a Forrás **attribútumként lehetőséget.**

    e. A **Forrás attribútumlistában** írja be a sor attribútumértékét.

    f. Kattintson **az Ok gombra**

    g. Kattintson a **Mentés** gombra.

    > [!Note]
    > A **person_id** attribútum **nem kötelező**

8. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

9. Az **iQualify LMS beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-iqualify-lms-single-sign-on"></a>Az iQualify LMS egyszeri bejelentkezéskonfigurálása

1. Nyisson meg egy új böngészőablakot, majd jelentkezzen be rendszergazdaként az iQualify környezetbe.

1. Miután bejelentkezett, kattintson az avatar a jobb felső sarokban, majd kattintson a **Fiók beállításai**

    ![Fiókbeállítások](./media/iqualify-tutorial/setting1.png)

1. A fiókbeállítások területen kattintson a menüszalag menüjére a bal oldalon, és kattintson az **INTEGRÁCIÓK**

    ![Integrációk](./media/iqualify-tutorial/setting2.png)

1. Az INTEGRÁCIÓK csoportban kattintson az **SAML** ikonra.

    ![SAML ikon](./media/iqualify-tutorial/setting3.png)

1. Az **SAML hitelesítési beállítások** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![SAML hitelesítési beállítások](./media/iqualify-tutorial/setting4.png)

    a. Az **SAML SINGLE SIGN-ON SERVICE URL-címmezőbe** illessze be az Azure AD alkalmazás konfigurációs ablakából másolt **bejelentkezési URL-címet.**

    b. Az **SAML LOGOUT URL-címmezőbe** illessze be az Azure AD-alkalmazás konfigurációs ablakából másolt **kijelentkezési URL-értéket.**

    c. Nyissa meg a letöltött tanúsítványfájlt a jegyzettömbben, másolja a tartalmat, majd illessze be a **NYILVÁNOS TANÚSÍTVÁNY** mezőbe.

    d. A **LOGIN BUTTON LABEL (A/ bejelentkezési gomb címkéje)** mezőbe írja be a bejelentkezési oldalon megjelenítendő gomb nevét.

    e. Kattintson **a MENTÉS gombra.**

    f. Kattintson **a FRISSÍTÉS gombra.**

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőtípusban**brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést az iQualify LMS-hez való hozzáférés biztosításával.

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az **iQualify LMS**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **iQualify LMS**lehetőséget.

    ![Az iQualify LMS hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-iqualify-lms-test-user"></a>IQualify LMS tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre az iQualify LMS-ben. Az iQualify LMS támogatja a just-in-time felhasználói kiépítést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nincs műveletelem. Ha a felhasználó még nem létezik az iQualify LMS-ben, a hitelesítés után egy új jön létre.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a hozzáférési panelen az iQualify LMS csempére kattint, megkapja az iQualify LMS alkalmazás bejelentkezési lapját. 

   ![bejelentkezési oldal](./media/iqualify-tutorial/login.png) 

Kattintson **a Bejelentkezés az Azure AD** gombbal gombra, és automatikusan be kell jelentkeznie az iQualify LMS-alkalmazásába.

A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](../user-help/active-directory-saas-access-panel-introduction.md) 

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)