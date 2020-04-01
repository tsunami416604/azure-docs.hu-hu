---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Pega Systemsrel | Microsoft dokumentumok'
description: Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Pega Systems között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 31acf80f-1f4b-41f1-956f-a9fbae77ee69
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: bd54466e054055ff84cd5bb2b28c5cc074ac0017
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72026803"
---
# <a name="tutorial-azure-active-directory-integration-with-pega-systems"></a>Oktatóanyag: Az Azure Active Directory integrációja a Pega Systems-szel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Pega Systemst az Azure Active Directoryval (Azure AD).

Ez az integráció a következő előnyöket nyújtja:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a Pega Systemshez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a Pega Systemsbe (egyszeri bejelentkezés) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti: az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa [el az Alkalmazásokra való egyszeri bejelentkezés az Azure Active Directoryban című témakört.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a kezdés előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció pega-i rendszerekkel való konfigurálásához a következőkre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Egy Azure AD-környezettel, regisztrálhat egy [egyhónapos próbaverzióra.](https://azure.microsoft.com/pricing/free-trial/)
* Pega Systems-előfizetés, amelynek engedélyezve van az egyszeri bejelentkezés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Pega Systems támogatja az SP által kezdeményezett és az IdP által kezdeményezett egyszeri szolgáltató rendszert.

## <a name="add-pega-systems-from-the-gallery"></a>Pega Systems hozzáadása a galériából

A Pega Systems Azure AD-be való integrálásának beállításához hozzá kell adnia a Pega Systems-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Nyissa meg **a Vállalati alkalmazások** > **Minden alkalmazás**.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

3. Alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget az ablak tetején:

    ![Új alkalmazás kiválasztása](common/add-new-app.png)

4. A keresőmezőbe írja be a Pega Systems ( **Pega Systems**) mezőbe. A keresési eredmények között válassza a **Pega Systems** elemet, majd a **Hozzáadás**lehetőséget.

     ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Pega Systems-szel egy Britta Simon nevű tesztfelhasználó használatával.
Egyszeri bejelentkezés engedélyezéséhez létre kell hoznia egy azure-felhasználó és a pega systems megfelelő felhasználója közötti kapcsolatot.

Az Azure AD egyszeri bejelentkezéskonfigurálásához és teszteléséhez a Pega Systems segítségével hajtsa végre az alábbi lépéseket:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** a szolgáltatás felhasználók számára való engedélyezéséhez.
2. **[Konfigurálja a Pega Systems egyszeri bejelentkezését](#configure-pega-systems-single-sign-on)** az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez.
4. **[Rendelje hozzá az Azure AD tesztfelhasználót](#assign-the-azure-ad-test-user)** az Azure AD egyszeri bejelentkezés engedélyezéséhez a felhasználó számára.
5. **[Hozzon létre egy Pega Systems tesztfelhasználót,](#create-a-pega-systems-test-user)** amely a felhasználó Azure AD-ábrázolásához kapcsolódik.
6. **[Tesztelje az egyszeri bejelentkezést](#test-single-sign-on)** a konfiguráció működésének ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezése a Pega Systems rendszerrel történő konfigurálásához tegye a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Pega Systems** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget:

    ![Válassza az Egyszeri bejelentkezés lehetőséget](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza az **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez:

    ![Egyetlen bejelentkezési módszer kiválasztása](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** párbeszédpanelen, ha az alkalmazást IdP által kezdeményezett módban szeretné konfigurálni, hajtsa végre az alábbi lépéseket.

    ![Egyszerű SAML konfiguráció párbeszédpanel](common/idp-intiated.png)

    1. Az **Azonosító** mezőbe írjon be egy URL-címet a következő mintába:

       `https://<customername>.pegacloud.io:443/prweb/sp/<instanceID>`

    1. A **Válasz URL-címe** mezőbe írjon be egy URL-címet a következő mintába:

       `https://<customername>.pegacloud.io:443/prweb/PRRestService/WebSSO/SAML/AssertionConsumerService`

5. Ha az alkalmazást SP által kezdeményezett módban szeretné konfigurálni, válassza **a További URL-ek beállítása** lehetőséget, és hajtsa végre a következő lépéseket.

    ![A Pega Systems Domain és URL-címek egyszeri bejelentkezési információi](common/both-advanced-urls.png)

    1. A **Bejelentkezési URL-cím** mezőbe írja be a bejelentkezési URL-értéket.

    1. A **Továbbítási állapot** mezőbe írjon be egy URL-címet a következő mintába:`https://<customername>.pegacloud.io/prweb/sso`

    > [!NOTE]
    > Az itt megadott értékek helyőrzők. A tényleges azonosítót, a válasz URL-címét, az URL-címet és a továbbítási állapot URL-címét kell használnia. Az azonosítót és a válasz URL-értékeit egy Pega alkalmazásból szerezheti be, amint azt az oktatóanyag későbbi részében ismertetettük. A relé állapotértékének lefelvételéhez lépjen kapcsolatba a [Pega Systems támogatási csapatával.](https://www.pega.com/contact-us) Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

6. A Pega Systems alkalmazásnak szüksége van az SAML állításokat, hogy egy adott formátumban. Ahhoz, hogy a megfelelő formátumban kapja meg őket, egyéni attribútumleképezéseket kell hozzáadnia az SAML token attribútumok konfigurációjához. A következő képernyőképen láthatók az alapértelmezett attribútumok. A **Felhasználói attribútumok** párbeszédpanel megnyitásához kattintson a **Szerkesztés** ikonra:

    ![Felhasználói attribútumok](common/edit-attribute.png)

7. Az előző képernyőképen látható attribútumokon kívül a Pega Systems alkalmazás nak szüksége van még néhány attribútumra az SAML válaszban. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek** szakaszában hajtsa végre az alábbi lépéseket az alábbi SAML tokenattribútumok hozzáadásához:

    
   - `uid`
   - `cn`
   - `mail`
   - `accessgroup`  
   - `organization`  
   - `orgdivision`
   - `orgunit`
   - `workgroup`  
   - `Phone`

    > [!NOTE]
    > Ezek az értékek a szervezetre jellemzőek. Adja meg a megfelelő értékeket.

    1. A **Felhasználói jogcímek kezelése** párbeszédpanel megnyitásához válassza az Új **jogcím hozzáadása** lehetőséget:

    ![Válassza az Új jogcím hozzáadása lehetőséget.](common/new-save-attribute.png)

    ![Felhasználói jogcímek kezelése párbeszédpanel](common/new-attribute-details.png)

    1. A **Név** mezőbe írja be a sorattribútum nevét.

    1. Hagyja üresen a **Névtér** mezőt.

    1. A **Forrás**területen válassza az **Attribútum**lehetőséget.

    1. A **Forrás attribútumlistában** válassza ki az adott sorhoz megjelenített attribútumértéket.

    1. Válassza az **Ok gombot.**

    1. Kattintson a **Mentés** gombra.

8. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** válassza a Letöltés hivatkozást az **összevonási metaadat-XML**mellett, a követelményeknek megfelelően, és mentse a tanúsítványt a számítógépre: **Download**

    ![Tanúsítvány letöltési hivatkozása](common/metadataxml.png)

9. A **Pega-rendszerek beállítása** szakaszban másolja a megfelelő URL-címeket az Ön igényei nek megfelelően.

    ![A konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL.**

    1. **Az Azure Hirdetési azonosítója**.

    1. **Kijelentkezési URL-cím**.

### <a name="configure-pega-systems-single-sign-on"></a>A Pega Systems egyszeri bejelentkezéskonfigurálása

1. Az egyszeri bejelentkezés beállításához a **Pega Systems** oldalán jelentkezzen be a Pega portálra egy rendszergazdai fiókkal egy másik böngészőablakban.

2. Válassza**a SysAdmin** > **hitelesítési szolgáltatás** **létrehozása** > lehetőséget:

    ![Hitelesítési szolgáltatás kiválasztása](./media/pegasystems-tutorial/tutorial_pegasystems_admin.png)
    
3. Hajtsa végre az alábbi lépéseket a **Hitelesítési szolgáltatás létrehozása** képernyőn.

    ![Hitelesítési szolgáltatás létrehozása képernyő](./media/pegasystems-tutorial/tutorial_pegasystems_admin1.png)

    1. A **Típus** listában válassza az **SAML 2.0**lehetőséget.

    1. A **Név** mezőbe írjon be bármilyen nevet (például **Azure AD SSO).**

    1. Az **Rövid leírás** mezőbe írjon be egy leírást.  

    1. Válassza **a Létrehozás és megnyitás**lehetőséget.
    
4. Az **Identitásszolgáltató (IdP) adatai** csoportban válassza **az IdP-metaadatok importálása** lehetőséget, és keresse meg az Azure Portalról letöltött metaadatfájlt. A metaadatok betöltéséhez kattintson a **Küldés** gombra:

    ![Identitásszolgáltató (IdP) információs szakasza](./media/pegasystems-tutorial/tutorial_pegasystems_admin2.png)
    
    Az importálás feltölti az IdP-adatokat az itt látható módon:

    ![Importált IdP-adatok](./media/pegasystems-tutorial/tutorial_pegasystems_admin3.png)
    
6. Hajtsa végre a **Szolgáltató (SP) beállításai** szakaszkövetkező lépéseit.

    ![Szolgáltató beállításai](./media/pegasystems-tutorial/tutorial_pegasystems_admin4.png)

    1. Másolja az **entitásazonosító** értékét, és illessze be az Azure Portal **alapszintű SAML-konfigurációja** **szakaszazonosító** mezőjébe.

    1. Másolja a **helyességi feltétel fogyasztói szolgáltatás (ACS) helyértékét,** és illessze be a **Válasz URL-cím** mezőjébe az Azure Portal **alapszintű SAML-konfigurációja** szakaszban.

    1. Válassza **a Kérelem aláírásának letiltása**lehetőséget.

7. Kattintson a **Mentés** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű tesztfelhasználót hoz létre az Azure Portalon.

1. Az Azure Portalon válassza az **Azure Active Directory** lehetőséget a bal oldali ablaktáblában, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget:

    ![Válassza a Minden felhasználó lehetőséget](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején:

    ![Új felhasználó kiválasztása](common/new-user.png)

3. A **Felhasználó** párbeszédpanelen hajtsa végre az alábbi lépéseket.

    ![Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőbe írja be **brittasimon@\<\< vállalattartomány>. kiterjesztés>. ** (Például.) BrittaSimon@contoso.com

    c. Válassza **a Jelszó megjelenítése**lehetőséget, majd írja le a **Jelszó** mezőbe írt értéket.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezheti Britta Simon számára, hogy egyetlen bejelentkezéssel használja az Azure egyszeri bejelentkezést, ha hozzáférést biztosít neki a Pega Systemshez.

1. Az Azure Portalon válassza a **Nagyvállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **Pega Systems**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Pega Systems**lehetőséget.

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali ablaktáblában válassza a **Felhasználók és csoportok**lehetőséget:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** **párbeszédpanelen** a Felhasználók és csoportok lehetőséget.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen jelölje ki **a Britta Simon** elemet a felhasználók listájában, majd kattintson a képernyő alján található **Kijelölés** gombra.

6. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. Kattintson a **kijelölés** gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

### <a name="create-a-pega-systems-test-user"></a>Pega Systems tesztfelhasználó létrehozása

Ezután létre kell hoznia egy Britta Simon nevű felhasználót a Pega Systemsben. A Felhasználók létrehozásához működjön együtt a [Pega Systems támogatási csapatával.](https://www.pega.com/contact-us)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Most kell tesztelniaz Azure AD egyszeri bejelentkezési konfiguráció a hozzáférési panel használatával.

Amikor a Hozzáférési panelen kiválasztja a Pega Systems csempét, automatikusan be kell jelentkeznie a Pega Systems példányba, amelyhez az SSO-t beállította. További információt az Alkalmazások portálon lévő [Alkalmazások elérése és használata című témakörben](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)talál.

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)