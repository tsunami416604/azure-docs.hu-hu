---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Predictix árjelentéssel | Microsoft dokumentumok'
description: Ebben az oktatóanyagban megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Predictix árjelentés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 691d0c43-3aa1-4220-9e46-e7a88db234ad
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/26/2019
ms.author: jeedes
ms.openlocfilehash: 808b2d964bb39af6b410a84563717102ebece454
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67094109"
---
# <a name="tutorial-azure-active-directory-integration-with-predictix-price-reporting"></a>Oktatóanyag: Az Azure Active Directory integrációja a Predictix árjelentésével

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Predictix árjelentési az Azure Active Directory (Azure AD).

Ez az integráció a következő előnyöket nyújtja:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a Predictix árjelentéshez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek a Predictix Árjelentésbe (egyszeri bejelentkezés) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti: az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa [el az Alkalmazásokra való egyszeri bejelentkezés az Azure Active Directoryban című témakört.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a kezdés előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció és a Predictix árjelentés konfigurálásához a következőkre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Egy Azure AD-környezettel, regisztrálhat egy [egy hónapos próba-előfizetésre.](https://azure.microsoft.com/pricing/free-trial/)
* Predictix price reporting előfizetés, amely nek engedélyezve van az egyszeri bejelentkezés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* A Predictix Price Reporting támogatja az SP által kezdeményezett egyszeri szolgáltatót.

## <a name="adding-predictix-price-reporting-from-the-gallery"></a>Predictix árjelentés hozzáadása a galériából

A Predictix árjelentés integrálásának beállításához hozzá kell adnia a Predictix árjelentésa a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget:

    ![Válassza az Azure Active Directory elemet.](common/select-azuread.png)

2. Ugrás **az Enterprise applications** > **Összes alkalmazásra:**

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Alkalmazás hozzáadásához válassza az **Új alkalmazás** lehetőséget az ablak tetején:

    ![Új alkalmazás kiválasztása](common/add-new-app.png)

4. A keresőmezőbe írja be a **Predictix árjelentés című jelölőnégyzetet.** A keresési eredmények között válassza a **Predictix árjelentés** lehetőséget, majd a **Hozzáadás**lehetőséget.

     ![Keresési eredmények](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezést a Predictix árjelentésével egy Britta Simon nevű tesztfelhasználó használatával.
Az egyszeri bejelentkezés engedélyezéséhez létre kell hoznia egy kapcsolatot egy Azure AD-felhasználó és a megfelelő felhasználó a Predictix árjelentés.

Az Azure AD egyszeri bejelentkezéskonfigurálásához és teszteléséhez a Predictix árjelentéssel hajtsa végre az alábbi lépéseket:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** a szolgáltatás felhasználók számára való engedélyezéséhez.
2. **[Konfigurálja a Predictix Árjelentés egyszeri bejelentkezését](#configure-predictix-price-reporting-single-sign-on)** az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez.
4. **[Rendelje hozzá az Azure AD tesztfelhasználót](#assign-the-azure-ad-test-user)** az Azure AD egyszeri bejelentkezés engedélyezéséhez a felhasználó számára.
5. **[Hozzon létre egy Predictix árjelentési tesztfelhasználót,](#create-a-predictix-price-reporting-test-user)** amely a felhasználó Azure AD-ábrázolásához kapcsolódik.
6. **[Tesztelje az egyszeri bejelentkezést](#test-single-sign-on)** a konfiguráció működésének ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezése a Predictix árjelentéssel történő egyszeri bejelentkezéshez tegye a következőket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Predictix Árjelentés** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget:

    ![Válassza az Egyszeri bejelentkezés lehetőséget](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza az **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez:

    ![Egyetlen bejelentkezési módszer kiválasztása](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához:

    ![Szerkesztés ikon](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** párbeszédpanelen hajtsa végre az alábbi lépéseket.

    ![Egyszerű SAML konfiguráció párbeszédpanel](common/sp-identifier.png)

    1. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő mintába:

       `https://<companyname-pricing>.predictix.com/sso/request`

    1. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő mintába:

        | |
        |--|
        | `https://<companyname-pricing>.predictix.com` |
        | `https://<companyname-pricing>.dev.predictix.com` |
        | |

    > [!NOTE]
    > Ezek az értékek helyőrzők. A tényleges bejelentkezési URL-címet és azonosítót kell használnia. Az értékek leése érdekében lépjen kapcsolatba a [Predictix Price Reporting támogatási csapatával.](https://www.infor.com/company/customer-center/) Az Azure Portal **alapszintű SAML-konfiguráció** párbeszédpanelén látható mintákra is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában válassza a **Tanúsítvány (Base64)** melletti **Letöltés** hivatkozást, és mentse a tanúsítványt a számítógépre:

    ![Tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Predictix árjelentés beállítása** csoportban másolja a megfelelő URL-címeket az Ön igényei nek megfelelően.

    ![A konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    1. **Bejelentkezési URL.**

    1. **Az Azure Hirdetési azonosítója**.

    1. **Kijelentkezési URL-cím**.

### <a name="configure-predictix-price-reporting-single-sign-on"></a>Predictix árjelentés egyszeri bejelentkezéskonfigurálása

Az egyszeri bejelentkezés konfigurálásához a Predictix Árjelentés idoben el kell küldenie a letöltött tanúsítványt és az Azure Portalról másolt URL-címeket a [Predictix Price Reporting támogatási csapatának.](https://www.infor.com/company/customer-center/) Ez a csapat biztosítja, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű tesztfelhasználót hoz létre az Azure Portalon.

1. Az Azure Portalon válassza az **Azure Active Directory** lehetőséget a bal oldali ablaktáblában, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget:

    ![Válassza a Minden felhasználó lehetőséget](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején:

    ![Új felhasználó kiválasztása](common/new-user.png)

3. A **Felhasználó** párbeszédpanelen tegye a következő lépéseket.

    ![Felhasználó párbeszédpanel](common/user-properties.png)

    1. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    1. A **Felhasználónév** mezőbe írja be **BrittaSimon@\<\< vállalattartomány>. kiterjesztés>. ** (Például.) BrittaSimon@contoso.com

    1. Válassza **a Jelszó megjelenítése**lehetőséget, majd írja le a **Jelszó** mezőbe írt értéket.

    1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon számára az Azure AD egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít neki a Predictix árjelentéshez.

1. Az Azure Portalon válassza a **Nagyvállalati alkalmazások**lehetőséget, válassza a **Minden alkalmazás**lehetőséget, majd válassza a **Predictix árjelentés lehetőséget.**

    ![Vállalati alkalmazások](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Predictix árjelentés lehetőséget.**

    ![Alkalmazások listája](common/all-applications.png)

3. A bal oldali ablaktáblában válassza a **Felhasználók és csoportok**lehetőséget:

    ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

4. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** **párbeszédpanelen** a Felhasználók és csoportok lehetőséget.

    ![Felhasználó hozzáadása kiválasztása](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen jelölje ki **a Britta Simon** elemet a felhasználók listájában, majd kattintson a képernyő alján található **Kijelölés** gombra.

6. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. Kattintson a **kijelölés** gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen válassza a **Hozzárendelés lehetőséget.**

### <a name="create-a-predictix-price-reporting-test-user"></a>Predictix árjelentési tesztfelhasználó létrehozása

Ezután létre kell hoznia egy Britta Simon nevű felhasználót a Predictix árjelentésben. A [Predictix Price Reporting támogatási csapatával](https://www.infor.com/company/customer-center/) együttműködve vehet fel felhasználókat. A felhasználókat létre kell hozni és aktiválni kell az egyszeri bejelentkezés használata előtt.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Most kell tesztelniaz Azure AD egyszeri bejelentkezési konfiguráció a hozzáférési panel használatával.

Ha a Hozzáférési panelen kiválasztja a Predictix árjelentés csempét, automatikusan be kell jelentkeznie a Predictix árjelentési példányba, amelyhez beállítja az SSO-t. További információt az Alkalmazások portálon lévő [Alkalmazások elérése és használata című témakörben](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)talál.

## <a name="additional-resources"></a>További források

- [Oktatóanyagok SaaS-alkalmazások az Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)