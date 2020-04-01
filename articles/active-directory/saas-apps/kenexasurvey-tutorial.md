---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az IBM Kenexa Survey Enterprise vállalattal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az IBM Kenexa Survey Enterprise között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c7aac6da-f4bf-419e-9e1a-16b460641a52
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/08/2019
ms.author: jeedes
ms.openlocfilehash: c649b966b3e210f6b026b06a9654761e0f97aea1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099053"
---
# <a name="tutorial-azure-active-directory-integration-with-ibm-kenexa-survey-enterprise"></a>Oktatóanyag: Az Azure Active Directory integrációja az IBM Kenexa Survey Enterprise vállalattal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az IBM Kenexa Survey Enterprise-t az Azure Active Directoryval (Azure AD).
Az IBM Kenexa Survey Enterprise és az Azure AD integrálása a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az IBM Kenexa Survey Enterprise-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az IBM Kenexa Survey Enterprise (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához az IBM Kenexa Survey Enterprise vállalattal a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* IBM Kenexa Survey Enterprise egyszeri bejelentkezéses előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az IBM Kenexa Survey Enterprise támogatja az **IDP** által kezdeményezett sso-t

## <a name="adding-ibm-kenexa-survey-enterprise-from-the-gallery"></a>Az IBM Kenexa Survey Enterprise hozzáadása a galériából

Az IBM Kenexa Survey Enterprise Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az IBM Kenexa Survey Enterprise-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha az IBM Kenexa Survey Enterprise-t a galériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **IBM Kenexa Survey Enterprise**kifejezést, válassza az IBM **Kenexa Survey Enterprise** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![IBM Kenexa Survey Enterprise az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az IBM Kenexa Survey Enterprise-szal egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés, hogy működjön, létre kell hozni egy kapcsolat közötti kapcsolat egy Azure AD-felhasználó és a kapcsolódó felhasználó az IBM Kenexa Survey Enterprise létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az IBM Kenexa Survey Enterprise vállalattal a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az IBM Kenexa Survey Enterprise Single Sign-On](#configure-ibm-kenexa-survey-enterprise-single-sign-on)** --t az egyszeri bejelentkezés i beállításainak konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre IBM Kenexa Survey Enterprise tesztfelhasználót](#create-ibm-kenexa-survey-enterprise-test-user)** – hogy az IBM Kenexa Survey Enterprise-ban britta Simon megfelelője legyen, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához az IBM Kenexa Survey Enterprise vállalattal hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **IBM Kenexa Survey Enterprise** alkalmazásintegrációs lapon válassza az Egyszeri **bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon hajtsa végre az alábbi lépéseket:

    ![IBM Kenexa Survey Enterprise Domain és URL-ek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://surveys.kenexa.com/<companycode>`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://surveys.kenexa.com/<companycode>/tools/sso.asp`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címével. Lépjen kapcsolatba [az IBM Kenexa Survey Enterprise ügyféltámogatási csapatával,](https://www.ibm.com/support/home/?lnk=fcw) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az IBM Kenexa Survey Enterprise alkalmazás arra számít, hogy egy adott formátumban megkapja a security assertions markup language (SAML) állításokat, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. A válaszban lévő felhasználói azonosító jogcím értékének meg kell egyeznie a Kenexa rendszerben konfigurált egyszeri bejelentkezési azonosítóval. Ha a szervezetben a megfelelő felhasználói azonosítót SSO Internet Datagram Protocol (IDP) protokollként szeretné leképezni, működjön együtt az [IBM Kenexa Survey Enterprise támogatási csapatával.](https://www.ibm.com/support/home/?lnk=fcw)

    Alapértelmezés szerint az Azure AD a felhasználói azonosítót az egyszerű felhasználónév (UPN) értékként állítja be. Ezt az értéket a Felhasználói attribútumok lapon **módosíthatja,** ahogy az az alábbi képernyőképen látható. Az integráció csak akkor működik, ha megfelelően befejezte a leképezést.

    ![image](common/edit-attribute.png)

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. Az **IBM Kenexa Survey Enterprise beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-ibm-kenexa-survey-enterprise-single-sign-on"></a>Az IBM Kenexa Survey Enterprise single sign-on konfigurálása

Az **IBM Kenexa Survey Enterprise** oldalon történő egyszeri bejelentkezés konfigurálásához el kell küldenie a letöltött **tanúsítványt (Base64)** és a megfelelő másolt URL-címeket az Azure Portalról az [IBM Kenexa Survey Enterprise támogatási csapatának.](https://www.ibm.com/support/home/?lnk=fcw) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

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

Ebben a szakaszban engedélyezheti Britta Simon számára, hogy egyetlen bejelentkezéssel használja az Azure egyszeri bejelentkezést az IBM Kenexa Survey Enterprise-hoz való hozzáférés biztosításával.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza az **IBM Kenexa Survey Enterprise lehetőséget.**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **IBM Kenexa Survey Enterprise**lehetőséget.

    ![Az IBM Kenexa Survey Enterprise hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-ibm-kenexa-survey-enterprise-test-user"></a>IBM Kenexa Survey Enterprise tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az IBM Kenexa Survey Enterprise alkalmazásban.

Ha felhasználókat szeretne létrehozni az IBM Kenexa Survey Enterprise rendszerben, és le szeretné képezni számukra az SSO-azonosítót, együttműködhet az [IBM Kenexa Survey Enterprise támogatási csapatával.](https://www.ibm.com/support/home/?lnk=fcw) Ezt az SSO-azonosító-azonosító t is le kell képeznie az Azure AD felhasználói azonosító értékéhez. Ezt az alapértelmezett beállítást az Attribútum lapon **módosíthatja.**

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen az IBM Kenexa Survey Enterprise csempére kattint, automatikusan be kell jelentkeznie az IBM Kenexa Survey Enterprise-ba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

