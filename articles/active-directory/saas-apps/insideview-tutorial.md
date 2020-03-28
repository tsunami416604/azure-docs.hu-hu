---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az InsideView-val | Microsoft dokumentumok'
description: Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az InsideView között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c489a7ab-6b1f-4efb-8a66-8bc13bca78c3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/20/2019
ms.author: jeedes
ms.openlocfilehash: 2149b8410104b39652b176895a31b42e094265f5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67100091"
---
# <a name="tutorial-azure-active-directory-integration-with-insideview"></a>Oktatóanyag: Az Azure Active Directory integrációja az InsideView-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az InsideView-t az Azure Active Directoryval (Azure AD).
Ez az integráció a következő előnyöket nyújtja:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá az InsideView-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az InsideView-ba (egyszeri bejelentkezés) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti: az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa [el az Alkalmazásokra való egyszeri bejelentkezés az Azure Active Directoryban című témakört.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció ininsideview-val való konfigurálásához a következőkre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/)kaphat.
* InsideView-előfizetés, amelynek egyszeri bejelentkezése engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az InsideView támogatja az IdP által kezdeményezett egyszeri szolgáltatót.

## <a name="add-insideview-from-the-gallery"></a>InsideView hozzáadása a galériából

Az InsideView azure AD-be való integrációjának beállításához hozzá kell adnia az InsideView-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Ugrás **az Enterprise applications** > **Összes alkalmazásra:**

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

3. Alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget az ablak tetején:

    ![Új alkalmazás kiválasztása](common/add-new-app.png)

4. A keresőmezőbe írja be az **InsideView kifejezést.** A keresési eredmények között válassza az **InsideView** elemet, majd a **Hozzáadás**lehetőséget.

    ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az InsideView-val egy Britta Simon nevű tesztfelhasználó használatával.
Egyszeri bejelentkezés engedélyezéséhez létre kell hoznia egy kapcsolatot egy Azure AD-felhasználó és a megfelelő felhasználó InsideView.

Az Azure AD egyszeri bejelentkezéskonfigurálásához és teszteléséhez az InsideView segítségével az alábbi lépéseket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** a szolgáltatás felhasználók számára való engedélyezéséhez.
2. **[Konfigurálja az InsideView egyszeri bejelentkezést](#configure-insideview-single-sign-on)** az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez.
4. **[Rendelje hozzá az Azure AD tesztfelhasználót](#assign-the-azure-ad-test-user)** az Azure AD egyszeri bejelentkezés engedélyezéséhez a felhasználó számára.
5. **[Hozzon létre egy InsideView tesztfelhasználót,](#create-an-insideview-test-user)** amely a felhasználó Azure AD-ábrázolásához kapcsolódik.
6. **[Tesztelje az egyszeri bejelentkezést](#test-single-sign-on)** a konfiguráció működésének ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésnek az InsideView-val történő konfigurálásához tegye a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az InsideView alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget:

    ![Egyszeri bejelentkezés kiválasztása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza az **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez:

    ![Egyetlen bejelentkezési módszer kiválasztása](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** párbeszédpanelen tegye a következő lépéseket.

    ![Egyszerű SAML konfiguráció párbeszédpanel](common/idp-reply.png)

    A **Válasz URL-címe** mezőbe írjon be egy URL-címet a következő mintába:

    `https://my.insideview.com/iv/<STS Name>/login.iv`

    > [!NOTE]
    > Ez az érték helyőrző. A tényleges válasz URL-címét kell használnia. Az érték lefelvételéhez lépjen kapcsolatba az [InsideView támogatási csapatával.](mailto:support@insideview.com) Az Azure Portal **alapszintű SAML-konfiguráció** párbeszédpanelén látható mintákra is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában válassza a **Tanúsítvány (Raw)** melletti **Letöltés** hivatkozást , és mentse a tanúsítványt a számítógépre:

    ![Tanúsítvány letöltési hivatkozása](common/certificateraw.png)

6. Az **InsideView beállítása** szakaszban másolja a megfelelő URL-címeket az Ön igényei nek megfelelően:

    ![A konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL.**

    1. **Az Azure Hirdetési azonosítója**.

    1. **Kijelentkezési URL-cím**.

### <a name="configure-insideview-single-sign-on"></a>Az InsideView egyszeri bejelentkezéskonfigurálása

1. Egy új böngészőablakban jelentkezzen be az InsideView vállalati webhelyére rendszergazdaként.

1. Az ablak tetején válassza a **Rendszergazda**, **SingleSignOn-beállítások**lehetőséget, majd adja hozzá az **SAML elemet.**
   
   ![SAML egyszeri bejelentkezési beállítások](./media/insideview-tutorial/ic794135.png "SAML egyszeri bejelentkezési beállítások")

1. Az **Új SAML hozzáadása** szakaszban tegye a következő lépéseket.

    ![Új SAML szakasz hozzáadása](./media/insideview-tutorial/ic794136.png "Új SAML szakasz hozzáadása")

    1. Az **STS-név** mezőbe írja be a konfiguráció nevét.

    1. A **SamlP/WS-Fed Kéretlen EndPoint** mezőbe illessze be az Azure Portalról másolt **bejelentkezési URL-értéket.**

    1. Nyissa meg az Azure Portalról letöltött Raw tanúsítványt. Másolja a tanúsítvány tartalmát a vágólapra, majd illessze be a tartalmat az **STS tanúsítvány** mezőbe.

    1. A **Crm felhasználói azonosító leképezése** mezőbe írja be a mezőt. **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

    1. A **Crm e-mail hozzárendelése** mezőbe írja be a mezőt. **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

    1. A **Crm utónév leképezése** mezőbe írja be a mezőt. **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**

    1. A **Crm vezetéknév leképezése** mezőbe írja be a mezőt. **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**  

    1. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezheti Britta Simon számára, hogy egyetlen bejelentkezéssel használja az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít neki az InsideView-hoz.

1. Az Azure Portalon válassza a **Nagyvállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd az **InsideView**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **InsideView lehetőséget.**

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali ablaktáblában válassza a **Felhasználók és csoportok**lehetőséget:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** **párbeszédpanelen** a Felhasználók és csoportok lehetőséget.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen jelölje ki **a Britta Simon** elemet a felhasználók listájában, majd kattintson az ablak alján található **Kijelölés** gombra.

6. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. Kattintson az ablak alján található **Kijelölés** gombra.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

### <a name="create-an-insideview-test-user"></a>InsideView tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek az InsideView-ba, hozzá kell adnia őket az InsideView nézethez. Manuálisan kell hozzáadnia őket.

Ha felhasználókat vagy névjegyeket szeretne létrehozni az InsideView nézetben, lépjen kapcsolatba az [InsideView támogatási csapatával.](mailto:support@insideview.com)

> [!NOTE]
> Az InsideView által biztosított felhasználói fiók-létrehozási eszköz vagy API segítségével azure AD felhasználói fiókokat hozhat létre.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Most kell tesztelniaz Azure AD egyszeri bejelentkezési konfiguráció a hozzáférési panel használatával.

Amikor a Hozzáférési panelen kiválasztja az InsideView csempét, automatikusan be kell jelentkeznie az Intonézet példányba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt az [Alkalmazások portálon található Alkalmazások elérése és használata című témakörben](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)talál.

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
