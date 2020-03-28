---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Sprinklr-rel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Sprinklr között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/07/2019
ms.author: jeedes
ms.openlocfilehash: 9e4025d040783bff1cd85fb46d571e3a89967892
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67089656"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Oktatóanyag: Az Azure Active Directory integrációja a Sprinklr-rel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Sprinklr-t az Azure Active Directoryval (Azure AD).
A Sprinklr és az Azure AD integrálása a következő előnyöket nyújtja:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Sprinklr.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve sprinklr (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció sprinklr konfigurálásához a következő elemekre van szüksége:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Sprinklr egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Sprinklr támogatja **SP** kezdeményezett SSO

## <a name="adding-sprinklr-from-the-gallery"></a>Sprinklr hozzáadása a galériából

A Sprinklr azure-beli AD-be való integrálásának konfigurálásához hozzá kell adnia a Sprinklr-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A Sprinklr hozzáadásához hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Sprinklr**kifejezést , válassza a **Sprinklr** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Sprinklr az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését a Sprinklr-rel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat közötti kapcsolat egy Azure AD-felhasználó és a kapcsolódó felhasználó Sprinklr létre kell hozni.

Konfigurálása és tesztelése az Azure AD egyszeri bejelentkezés a Sprinklr, a következő építőelemek:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja Sprinklr Single Sign-On](#configure-sprinklr-single-sign-on)** -, hogy konfigurálja az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Sprinklr tesztfelhasználó létrehozása](#create-sprinklr-test-user)** - a Sprinklr-ben lévő Britta Simon megfelelőjének létrehozásához, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Sprinklr** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Sprinklr Domain és URL-ek egyszeri bejelentkezési információk](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<subdomain>.sprinklr.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<subdomain>.sprinklr.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a Sprinklr ügyféltámogatási csapatával,](https://www.sprinklr.com/contact-us/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **Sprinklr beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-sprinklr-single-sign-on"></a>Sprinklr egyszeri bejelentkezés konfigurálása

1. Egy másik böngésző ablakban jelentkezzen be a Sprinklr cég webhelyére rendszergazdaként.

1. Nyissa meg a **Felügyeleti \> beállítások lapot.**

    ![Felügyelet](./media/sprinklr-tutorial/ic782907.png "Adminisztráció")

1. Nyissa meg a **Partner \> Single Sign kezelése** lehetőséget a bal oldali ablaktáblából.

    ![Partner kezelése](./media/sprinklr-tutorial/ic782908.png "Partner kezelése")

1. Kattintson **a +Single Sign Ons hozzáadása gombra.**

    ![Egyszeri bejelentkezések](./media/sprinklr-tutorial/ic782909.png "Egyszeri bejelentkezések")

1. Az **Egyszeri bejelentkezés a lapon** hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezések](./media/sprinklr-tutorial/ic782910.png "Egyszeri bejelentkezések")

    a. A **Név** mezőbe írja be a konfiguráció nevét (például: *WAADSSOTest*).

    b. Válassza az **Engedélyezve** lehetőséget.

    c. Válassza **az Új SSO-tanúsítvány használata lehetőséget.**

    d. Nyissa meg az alap-64 kódolású tanúsítványt a jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be az **identitásszolgáltató tanúsítványának** szövegmezőjébe.

    e. Illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt az **entitásazonosító** szövegmezőjébe.

    f. Illessze be az Azure Portalról másolt **bejelentkezési URL-címet** az **Identitásszolgáltató bejelentkezési URL-címmezőjébe.**

    g. Illessze be a **kijelentkezési URL-értéket,** amelyet az Azure Portalról másolt az **Identitásszolgáltató kijelentkezési URL-címmezőjébe.**

    h. **Saml felhasználói azonosító típusként**válassza a **Helyességi feltétel a felhasználó sprinklr.com felhasználónevét**.

    i. Saml **felhasználói azonosító helyként**válassza a Felhasználói azonosító elem **névazonosító elemében található Tulajdonos utasítás**.

    j. Kattintson a **Mentés** gombra.

    ![SAML](./media/sprinklr-tutorial/ic782911.png "SAML")

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

Ebben a szakaszban engedélyezheti Britta Simon azure egyszeri bejelentkezés használatával hozzáférést biztosít a Sprinklr.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a **Sprinklr**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Sprinklr**lehetőséget.

    ![A Sprinklr link az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-sprinklr-test-user"></a>Sprinklr tesztfelhasználó létrehozása

1. Jelentkezzen be a Sprinklr cég honlapján, mint egy rendszergazda.

1. Nyissa meg a **Felügyeleti \> beállítások lapot.**

    ![Felügyelet](./media/sprinklr-tutorial/ic782907.png "Adminisztráció")

1. Nyissa meg **az Ügyfélfelhasználók \> kezelése** lehetőséget a bal oldali ablaktáblából.

    ![Beállítások](./media/sprinklr-tutorial/ic782914.png "Beállítások")

1. Kattintson az **Add User** (Felhasználó hozzáadása) elemre.

    ![Beállítások](./media/sprinklr-tutorial/ic782915.png "Beállítások")

1. A **Felhasználó szerkesztése** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Felhasználó szerkesztése](./media/sprinklr-tutorial/ic782916.png "Felhasználó szerkesztése")

    a. Az **E-mail**, **Utónév** és **vezetéknév** szövegmezőbe írja be a kiépíteni kívánt Azure AD felhasználói fiók adatait.

    b. Válassza a **Jelszó letiltva**lehetőséget.

    c. Válassza a **Nyelv**lehetőséget.

    d. Válassza a **Felhasználótípus lehetőséget**.

    e. Kattintson a **Frissítés** parancsra.

    > [!IMPORTANT]
    > **A jelszó letiltása** lehetőséget kell választani ahhoz, hogy a felhasználó identitásszolgáltatón keresztül belehessen jelentkezni. 

1. Nyissa meg a **Szerepkör**, majd hajtsa végre az alábbi lépéseket:

    ![Partneri szerepkörök](./media/sprinklr-tutorial/ic782917.png "Partneri szerepkörök")

    a. A **Globális** listában válassza **a ALL_Permissions**lehetőséget.  

    b. Kattintson a **Frissítés** parancsra.

> [!NOTE]
> A Sprinklr felhasználói fiók létrehozásához szükséges bármely más eszköz vagy API-k, amelyeket a Sprinklr biztosít az Azure AD felhasználói fiókok kiépítéséhez.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha rákattint a Sprinklr csempe a hozzáférési panelen, akkor automatikusan bejelentkezett a Sprinklr, amelyre beállította SSO. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
