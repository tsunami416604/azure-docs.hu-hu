---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Signagelive-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Signagelive között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: d923f0e7-ad31-4d59-a6fd-f0e895e1a32d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 1/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6dab2fd0ee2f25e835b4bd07a3534475d3d93b5e
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160925"
---
# <a name="tutorial-azure-active-directory-integration-with-signagelive"></a>Oktatóanyag: Az Azure Active Directory integrációja a Signagelive-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Signagelive-ot az Azure Active Directoryval (Azure AD).
A Signagelive integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Signagelive-hoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkeznek a Signagelive-ba (egyszeri bejelentkezés) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti: az Azure Portalon.

Az Azure AD-vel való SaaS-alkalmazások integrációjáról a [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Signagelive szolgáltatással a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Egy Azure AD-környezettel, [egy hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/)kaphat.
* Signagelive egyszeri bejelentkezést engedélyező előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Signagelive támogatja sp által kezdeményezett Egyszeri bejelentkezés.

## <a name="add-signagelive-from-the-gallery"></a>Signagelive hozzáadása a galériából

A Signagelive azure AD-be való integrálásának konfigurálásához először adja hozzá a Signagelive-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

Ha a Signagelive-ot a galériából szeretné hozzáadni, tegye a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com)a bal oldali ablaktáblában válassza ki az **Azure Active Directory** ikonját.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a Minden **alkalmazás lehetőséget.**

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Signagelive**kifejezést. 

     ![Signagelive az eredménylistában](common/search-new-app.png)

5. Válassza a **Signagelive** elemet az eredmények ablaktábláján, majd az alkalmazás hozzáadásához kattintson a **Hozzáadás** gombra.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Signagelive-val egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó signagelive.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Signagelive szolgáltatással először hajtsa végre a következő építőelemeket:

1. [Konfigurálja az Azure AD egyszeri bejelentkezést,](#configure-azure-ad-single-sign-on) hogy a felhasználók használhassák ezt a funkciót.
2. [Konfigurálja a Signagelive egyszeri bejelentkezést](#configure-signagelive-single-sign-on) az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
3. [Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezésének britta simonnal való teszteléséhez.
4. [Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user) hogy Britta Simon használhassa az Azure AD egyszeri bejelentkezést.
5. [Hozzon létre egy Signagelive teszt felhasználó,](#create-a-signagelive-test-user) hogy egy megfelelője Britta Simon signagelive, amely kapcsolódik a felhasználó Azure AD-ábrázolása.
6. [Tesztelje az egyszeri bejelentkezést](#test-single-sign-on) a konfiguráció működésének ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Signagelive szolgáltatással, tegye a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Signagelive** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza az **SAML** lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon válassza a **Szerkesztés** lehetőséget az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** csoportban kövesse az alábbi lépéseket:

    ![Signagelive tartomány és URL-címek egyszeri bejelentkezési információk](common/sp-signonurl.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet, amely a következő mintát használja:`https://login.signagelive.com/sso/<ORGANIZATIONALUNITNAME>`

    > [!NOTE]
    > Az érték nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték lefelvételéhez lépjen kapcsolatba a [Signagelive ügyféltámogatási csapatával.](mailto:support@signagelive.com) Az Azure Portal **alapszintű SAML-konfigurációszakaszában** látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában válassza a **Letöltés** lehetőséget a **tanúsítvány (Raw)** letöltéséhez a megadott beállításokból a követelménynek megfelelően. Ezután mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificateraw.png)

6. A **Signagelive beállítása** szakaszban másolja a szükséges URL-cím(eke)t.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-signagelive-single-sign-on"></a>Signagelive egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálásához a Signagelive oldalon küldje el a letöltött **tanúsítványt (raw)** és másolja az URL-címeket az Azure Portalról a [Signagelive támogatási csapatának.](mailto:support@signagelive.com) Biztosítják, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A **Felhasználó** párbeszédpanelen tegye a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőbe írjabrittasimon@yourcompanydomain.extensionbe a " " (Felhasználónév) mezőbe. Ebben az esetben például beírhatja a "BrittaSimon@contoso.com" ".

    c. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd jegyezze fel a Jelszó mezőben megjelenő értéket.

    d. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés használatával a Signagelive hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd a **Signagelive**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Signagelive**lehetőséget.

    ![A Signagelive hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Válassza a **Felhasználó hozzáadása** gombot. Ezután a **Hozzárendelés hozzáadása** párbeszédpanelen válassza a Felhasználók és **csoportok**lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanel **Felhasználók** listájában válassza **a Britta Simon**elemet. Ezután kattintson a **kijelölés** gombra a képernyő alján.

6. Ha az SAML-feltételben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából. Ezután kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-a-signagelive-test-user"></a>Signagelive tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Signagelive-ban. Együttműködve a [Signagelive támogatási csapatával](mailto:support@signagelive.com) a felhasználók hozzáadása a Signagelive platformon. Az egyszeri bejelentkezés használata előtt létre kell hoznia és aktiválnia kell a felhasználókat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a MyApps portál használatával teszteli.

Ha a **Signagelive** csempét választja a MyApps portálon, automatikusan be kell jelentkeznie. A MyApps portálról a [Mi a MyApps portál?](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directoryval való integrálásáról szóló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

