---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a PerformanceCentre-rel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a PerformanceCentre között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 65288c32-f7e6-4eb3-a6dc-523c3d748d1c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/14/2019
ms.author: jeedes
ms.openlocfilehash: 08f0f1c1e9bbfcd27f97cd58fa6c6e40f448f9f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094638"
---
# <a name="tutorial-azure-active-directory-integration-with-performancecentre"></a>Oktatóanyag: Az Azure Active Directory integrációja a PerformanceCentre-rel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a PerformanceCentre-t az Azure Active Directoryval (Azure AD).
A PerformanceCentre és az Azure AD integrálása a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a PerformanceCentre-hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a PerformanceCentre (Single Sign-On) szolgáltatásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a PerformanceCentre-rel a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* PerformanceCentre egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A PerformanceCentre támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-performancecentre-from-the-gallery"></a>A PerformanceCentre hozzáadása a galériából

A PerformanceCentre Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a PerformanceCentre-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha a Teljesítményközpontot a galériából szeretné hozzáadni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **PerformanceCentre**( **Teljesítményközpont** ) kifejezést az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![PerformanceCentre az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a PerformanceCentre szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó a PerformanceCentre közötti kapcsolat létrehozásához.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a PerformanceCentre szolgáltatással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a PerformanceCentre Single Sign-On --t](#configure-performancecentre-single-sign-on)** az egyszeri bejelentkezési beállítások alkalmazásoldali konfigurálásához.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre PerformanceCentre tesztfelhasználót](#create-performancecentre-test-user)** – ha a PerformanceCentre-ben britta Simon megfelelője szeretne lenni, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a PerformanceCentre szolgáltatással hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **PerformanceCentre** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![PerformanceCentre tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`http://<companyname>.performancecentre.com/saml/SSO`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`http://<companyname>.performancecentre.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a PerformanceCentre ügyféltámogatási csapatával,](https://www.performancecentre.com/contact-us/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **PerformanceCentre beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-performancecentre-single-sign-on"></a>A PerformanceCentre egyszeri bejelentkezésének konfigurálása

1. Jelentkezzen be **a PerformanceCentre** vállalati webhelyére rendszergazdaként.

2. A bal oldali lapon kattintson a **Konfigurálás gombra.**
   
    ![Az Azure AD egyszeri bejelentkezése][10]

3. A bal oldali lapon kattintson a Vegyes , majd **az Egyszeri bejelentkezés** **gombra.**
   
    ![Az Azure AD egyszeri bejelentkezése][11]

4. **Protokollként**válassza az **SAML**lehetőséget.
   
    ![Az Azure AD egyszeri bejelentkezése][12]

5. Nyissa meg a letöltött metaadatfájlt a jegyzettömbben, másolja a tartalmat, illessze be az **Identitásszolgáltató metaadatok** szövegmezőjébe, majd kattintson a **Mentés gombra.**
   
    ![Az Azure AD egyszeri bejelentkezése][13]

6. Ellenőrizze, hogy az **entitás alapURL-jének** és **az entitásazonosító URL-címének** értékei helyesek-e.
    
     ![Az Azure AD egyszeri bejelentkezése][14]

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

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést a PerformanceCentre-hez való hozzáférés biztosításával.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **PerformanceCentre**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **PerformanceCentre**lehetőséget.

    ![A PerformanceCentre hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-performancecentre-test-user"></a>PerformanceCentre tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy felhasználó nevű Britta Simon a PerformanceCentre.

**Britta Simon nevű felhasználó létrehozásához a PerformanceCentre-ben hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a PerformanceCentre vállalati webhelyére rendszergazdaként.

2. A bal oldali menüben kattintson az **Összefüggés gombra,** majd a **Résztvevő létrehozása parancsra.**
   
    ![Felhasználó létrehozása][400]

3. Az **Összefüggés - Résztvevő létrehozása** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Felhasználó létrehozása][401]
    
    a. Írja be britta Simon szükséges attribútumait a kapcsolódó szövegdobozokba.
    
    >[!IMPORTANT]
    >A Britta PerformanceCentre felhasználói névattribútumának meg kell egyeznie az Azure AD-ben lévő felhasználónévvel.
    
    b. Válassza az **Ügyfélrendszergazda** lehetőséget **szerepkör kiválasztása ként.**
    
    c. Kattintson a **Mentés** gombra. 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelEn a PerformanceCentre csempére kattint, automatikusan be kell jelentkeznie arra a PerformanceCentre-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

<!--Image references-->

[10]: ./media/performancecentre-tutorial/tutorial_performancecentre_06.png
[11]: ./media/performancecentre-tutorial/tutorial_performancecentre_07.png
[12]: ./media/performancecentre-tutorial/tutorial_performancecentre_08.png
[13]: ./media/performancecentre-tutorial/tutorial_performancecentre_09.png
[14]: ./media/performancecentre-tutorial/tutorial_performancecentre_10.png
[400]: ./media/performancecentre-tutorial/tutorial_performancecentre_11.png
[401]: ./media/performancecentre-tutorial/tutorial_performancecentre_12.png