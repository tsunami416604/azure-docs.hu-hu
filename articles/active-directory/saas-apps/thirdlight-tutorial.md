---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a ThirdLight-tal | Microsoft dokumentumok'
description: Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a ThirdLight között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 168aae9a-54ee-4c2b-ab12-650a2c62b901
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/25/2019
ms.author: jeedes
ms.openlocfilehash: 448d46cd21a63488c4f567d5555fe6406fc0fa73
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67089090"
---
# <a name="tutorial-azure-active-directory-integration-with-thirdlight"></a>Oktatóanyag: Az Azure Active Directory integrációja a ThirdLight-tal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a ThirdLight szolgáltatást az Azure Active Directoryval (Azure AD). Ez az integráció a következő előnyöket nyújtja:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a ThirdLight-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkeznek a ThirdLight (egyszeri bejelentkezés) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti: az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el az Azure Active Directory alkalmazásokra való egyszeri bejelentkezés című [témakört.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a ThirdLight segítségével a következőkre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kaphat.
* ThirdLight-előfizetés, amelynek egyszeri bejelentkezése engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A ThirdLight támogatja az SP által kezdeményezett egyszeri szolgáltatást.

## <a name="add-thirdlight-from-the-gallery"></a>ThirdLight hozzáadása a galériából

A ThirdLight azure AD-be való integrációjának beállításához hozzá kell adnia a ThirdLight-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Ugrás **az Enterprise applications** > **Összes alkalmazásra:**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

3. Alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget az ablak tetején:

    ![Új alkalmazás kiválasztása](common/add-new-app.png)

4. A keresőmezőbe írja be a **ThirdLight ( Harmadik fény ) mezőbe.** A keresési eredmények között válassza a **ThirdLight** elemet, majd a **Hozzáadás lehetőséget.**

     ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezést a ThirdLight szolgáltatással egy Britta Simon nevű tesztfelhasználó használatával.
Egyszeri bejelentkezés engedélyezéséhez létre kell hoznia egy kapcsolatot egy Azure AD-felhasználó és a megfelelő felhasználó a ThirdLight.

Az Azure AD egyszeri bejelentkezéskonfigurálásához és teszteléséhez a ThirdLight szolgáltatással hajtsa végre az alábbi lépéseket:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** a szolgáltatás felhasználók számára való engedélyezéséhez.
2. **[Konfigurálja a ThirdLight egyszeri bejelentkezést](#configure-thirdlight-single-sign-on)** az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez.
4. **[Rendelje hozzá az Azure AD tesztfelhasználót](#assign-the-azure-ad-test-user)** az Azure AD egyszeri bejelentkezés engedélyezéséhez a felhasználó számára.
5. **[Hozzon létre egy ThirdLight tesztfelhasználót,](#create-a-thirdlight-test-user)** amely a felhasználó Azure AD-ábrázolásához kapcsolódik.
6. **[Tesztelje az egyszeri bejelentkezést](#test-single-sign-on)** a konfiguráció működésének ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezése a ThirdLight segítségével történő konfigurálásához tegye a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a ThirdLight alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget:

    ![Egyszeri bejelentkezés kiválasztása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza az **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez:

    ![Egyetlen bejelentkezési módszer kiválasztása](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** párbeszédpanelen hajtsa végre az alábbi lépéseket.

    ![Egyszerű SAML konfiguráció párbeszédpanel](common/sp-identifier.png)

    1. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő mintába:
    
          `https://<subdomain>.thirdlight.com/`

    1. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő mintába:

       `https://<subdomain>.thirdlight.com/saml/sp`

       > [!NOTE]
       > Ezek az értékek helyőrzők. A tényleges bejelentkezési URL-címet és azonosítót kell használnia. Az értékek értéséhez lépjen kapcsolatba a [ThirdLight támogatási csapatával.](https://www.thirdlight.com/support) Az Azure Portal **alapszintű SAML-konfiguráció** párbeszédpanelén látható mintákra is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** válassza a Letöltés hivatkozást az **összevonási metaadat-XML**mellett, a követelményeknek megfelelően, és mentse a fájlt a számítógépre: **Download**

    ![Tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. A **Harmadik fény beállítása** szakaszban másolja a megfelelő URL-címeket az Ön igényei nek megfelelően:

    ![A konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL.**

    1. **Az Azure Hirdetési azonosítója**.

    1. **Kijelentkezési URL-cím**.

### <a name="configure-thirdlight-single-sign-on"></a>A ThirdLight egyszeri bejelentkezés konfigurálása

1. Egy új böngészőablakban jelentkezzen be a ThirdLight vállalati webhelyére rendszergazdaként.

1. Tovább a **konfigurációs** > **rendszer felügyeletének** > **SAML2-re:**

    ![Rendszerfelügyelet](./media/thirdlight-tutorial/ic805843.png "Rendszerfelügyelet")

1. Az SAML2 konfigurációs szakaszban tegye meg a következő lépéseket.
  
    ![SAML2 konfigurációs szakasz](./media/thirdlight-tutorial/ic805844.png "SAML2 konfigurációs szakasz")

    1. Válassza **az SAML2 egyszeri bejelentkezés engedélyezése**lehetőséget.

    1. Az **IdP-metaadatok forrása**csoportban válassza **az IdP-metaadatok betöltése XML-ből**lehetőséget.

    1. Nyissa meg az Azure Portalról az előző szakaszban letöltött metaadatfájlt. Másolja a fájl tartalmát, és illessze be az **IdP metaadat-XML mezőbe.**

    1. Válassza **az SAML2-beállítások mentése lehetőséget.**

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű tesztfelhasználót hoz létre az Azure Portalon.

1. Az Azure Portalon válassza az **Azure Active Directory** lehetőséget a bal oldali ablaktáblában, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget:

    ![Válassza a Minden felhasználó lehetőséget](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget az ablak tetején:

    ![Új felhasználó kiválasztása](common/new-user.png)

3. A **Felhasználó** párbeszédpanelen tegye a következő lépéseket.

    ![Felhasználó párbeszédpanel](common/user-properties.png)

    1. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    1. A **Felhasználónév** mezőbe írja be **BrittaSimon@\<\< vállalattartomány>. kiterjesztés>. ** (Például.) BrittaSimon@contoso.com

    1. Válassza **a Jelszó megjelenítése**lehetőséget, majd írja le a **Jelszó** mezőbe írt értéket.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon számára az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít neki a ThirdLight-hoz.

1. Az Azure Portalon válassza a **Nagyvállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **ThirdLight**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **ThirdLight lehetőséget.**

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali ablaktáblában válassza a **Felhasználók és csoportok**lehetőséget:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** **párbeszédpanelen** a Felhasználók és csoportok lehetőséget.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen jelölje ki **a Britta Simon** elemet a felhasználók listájában, majd kattintson az ablak alján található **Kijelölés** gombra.

6. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. Kattintson az ablak alján található **Kijelölés** gombra.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

### <a name="create-a-thirdlight-test-user"></a>ThirdLight tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a ThirdLight-ba, hozzá kell adnia őket a ThirdLight-hoz. Manuálisan kell hozzáadnia őket.

Felhasználói fiók létrehozásához tegye a következőket:

1. Jelentkezzen be a ThirdLight vállalati webhelyére rendszergazdaként.

1. Nyissa meg a **Felhasználók** lapot.

1. Válassza a **Felhasználók és csoportok lehetőséget.**

1. Válassza **az Új felhasználó hozzáadása**lehetőséget.

1. Adja meg a felhasználónevet, a nevet vagy a leírást, valamint egy érvényes Azure AD-fiók e-mail címét, amelyet ki szeretne építeni. Válasszon egy készletet vagy új tagok csoportját.

1. Kattintson a **Létrehozás** gombra.

> [!NOTE]
> A ThirdLight által biztosított bármely felhasználói fiók-létrehozási eszközt vagy API-t használhatja az Azure AD felhasználói fiókok kiépítéséhez.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Most kell tesztelniaz Azure AD egyszeri bejelentkezési konfiguráció a hozzáférési panel használatával.

Amikor a Hozzáférési panelen kiválasztja a ThirdLight csempét, automatikusan be kell jelentkeznie a ThirdLight példányba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt az [Alkalmazások portálon található Alkalmazások elérése és használata című témakörben](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)talál.

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
