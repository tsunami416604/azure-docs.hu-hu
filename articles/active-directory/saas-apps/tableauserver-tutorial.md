---
title: 'Oktatóanyag: Azure Active Directory-integráció a tabló-kiszolgálóval | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a tabló-kiszolgáló között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: c1917375-08aa-445c-a444-e22e23fa19e0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/22/2019
ms.author: jeedes
ms.openlocfilehash: f9ef179c1a93d8b2f97c47eb4c68d0312d55d3d1
ms.sourcegitcommit: 3073581d81253558f89ef560ffdf71db7e0b592b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68825975"
---
# <a name="tutorial-azure-active-directory-integration-with-tableau-server"></a>Oktatóanyag: Azure Active Directory-integráció a tabló-kiszolgálóval

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a tabló-kiszolgálót Azure Active Directory (Azure AD) használatával.
A tabló-kiszolgáló az Azure AD-vel való integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a tabló-kiszolgálóhoz.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a tabló-kiszolgálóra (egyszeri bejelentkezés) az Azure AD-fiókkal.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció a tabló-kiszolgálóval való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* Tabló-kiszolgáló egyszeri bejelentkezésre engedélyezett előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A tabló-kiszolgáló támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-tableau-server-from-the-gallery"></a>Tabló-kiszolgáló hozzáadása a katalógusból

A tabló-kiszolgáló az Azure AD-be való integrálásának konfigurálásához fel kell vennie a tabló-kiszolgálót a katalógusból a felügyelt SaaS-alkalmazások listájára.

**A tabló-kiszolgáló katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **tabló kiszolgáló**kifejezést, válassza a **tabló kiszolgáló** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![Tabló-kiszolgáló az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a tabló-kiszolgálóval konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a tabló-kiszolgáló kapcsolódó felhasználója közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés a tabló-kiszolgálóval való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. A **[tabló-kiszolgáló egyszeri bejelentkezésének konfigurálása](#configure-tableau-server-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. A **[tabló-kiszolgáló tesztelési felhasználójának létrehozása](#create-tableau-server-test-user)** – hogy a Britta Simon a tabló-kiszolgálón legyen, amely a felhasználó Azure ad-képviseletéhez van társítva.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés a tabló-kiszolgálóval való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/)a **tabló kiszolgáló** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    ![Tabló kiszolgáló tartomány és URL-címek egyszeri bejelentkezési adatai](common/sp-identifier-reply.png)

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://azure.<domain name>.link`

    b. Az **azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://azure.<domain name>.link`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://azure.<domain name>.link/wg/saml/SSO/index.html`

    > [!NOTE]
    > Az előző értékek nem valós értékek. Frissítse az értékeket a tabló-kiszolgáló konfigurációs oldalának tényleges URL-címével és azonosítójával, amelyet az oktatóanyag későbbi részében ismertet.

5. A tabló-kiszolgáló alkalmazás egy egyéni jogcím -felhasználónevet vár, amelyet az alábbi módon kell meghatározni. Ez felhasználói azonosítóként használatos az egyedi felhasználói azonosító jogcímek helyett. Ezen attribútumok értékeit az alkalmazás-integráció lapon lévő **felhasználói attribútumok &** jogcímek szakaszban kezelheti. Kattintson a **Szerkesztés** gombra a **felhasználói attribútumok &** jogcímek párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A felhasználók **attribútumai &** jogcímek párbeszédpanel **felhasználói** jogcímek szakaszában konfigurálja az SAML-jogkivonat attribútumot a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    | Name (Név) | Forrás attribútum | Névtér |
    | ---------------| --------------- | ----------- |
    | username | user.userprincipalname | `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | | |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Adja meg a **névtér** értékét.

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

7. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

8. A **tabló-kiszolgáló beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-tableau-server-single-sign-on"></a>A tabló-kiszolgáló egyszeri bejelentkezésének konfigurálása

1. Ha be szeretné állítani az egyszeri bejelentkezést az alkalmazáshoz, rendszergazdaként be kell jelentkeznie a tabló-kiszolgáló bérlőbe.

2. A **konfiguráció** lapon válassza a **felhasználói identitás & hozzáférés**lehetőséget, majd válassza a **hitelesítési** módszer lapot.

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial-tableauserver-auth.png)

3. A **konfiguráció** lapon hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/tableauserver-tutorial/tutorial-tableauserver-config.png)

    a. A **hitelesítési módszer**beállításnál válassza az SAML lehetőséget.

    b. Jelölje be az **SAML-hitelesítés engedélyezése a kiszolgálón**jelölőnégyzetet.

    c. Tabló-kiszolgáló visszatérési URL-címe – a tabló-kiszolgáló felhasználói által elérhető URL <http://tableau_server>-cím, például:. A `http://localhost` használata nem ajánlott. A záró perjelet (például `http://tableau_server/`) használó URL-cím használata nem támogatott. A **tabló-kiszolgáló visszatérési URL** -címének másolása és beillesztése az **URL-cím** szövegmezőbe az alapszintű **SAML-konfiguráció** szakaszának Azure Portal

    d. SAML-entitás azonosítója – az entitás azonosítója egyedileg azonosítja a tabló-kiszolgáló telepítését a identitásszolgáltató. Itt megadhatja a tabló-kiszolgáló URL-címét, ha szeretné, de nem kell a tabló-kiszolgáló URL-címének lennie. Az **SAML-entitás azonosítójának** másolása és beillesztése az **azonosító** szövegmezőbe az alapszintű **saml-konfiguráció** szakaszának Azure Portal

    e. Kattintson az **XML letöltése metaadat** -fájlra, és nyissa meg a szövegszerkesztő alkalmazásban. Keresse meg az állítási fogyasztói szolgáltatás URL-címét http post és index 0 értékkel, és másolja az URL-címet. Most illessze be a **Válasz URL** szövegmezőbe az **ALAPszintű SAML-konfiguráció** szakaszának Azure Portal

    f. Keresse meg Azure Portalról letöltött összevonási metaadat-fájlt, majd töltse fel az **SAML-identitásszolgáltató metaadat**-fájljába.

    g. Adja meg azoknak az attribútumoknak a nevét, amelyeket a identitásszolgáltató a felhasználónevek, a megjelenítendő nevek és az e-mail-címek tárolására használ.

    h. Kattintson a **Save** (Mentés) gombra

    > [!NOTE]
    > Az ügyfélnek fel kell töltenie minden tanúsítványt a tabló-kiszolgáló SAML SSO-konfigurációjában, és a rendszer figyelmen kívül hagyja az SSO-folyamat során. Ha segítségre van szüksége az SAML a tabló-kiszolgálón való konfigurálásához, tekintse meg ezt a cikket az [SAML konfigurálása](https://help.tableau.com/current/server/en-gb/saml_config_steps_tsm_ui.htm)című cikkben.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt:`brittasimon@yourcompanydomain.extension`  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon az Azure egyszeri bejelentkezés használatára teszi lehetővé, hogy hozzáférést biztosítson a tabló-kiszolgálóhoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **tabló-kiszolgáló**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **tabló-kiszolgáló**elemet.

    ![A tabló kiszolgáló hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-tableau-server-test-user"></a>Tabló-kiszolgáló tesztelési felhasználójának létrehozása

Ennek a szakasznak a célja egy Britta Simon nevű felhasználó létrehozása a tabló-kiszolgálón. A tabló-kiszolgálón ki kell építenie az összes felhasználót.

A felhasználó felhasználónevének egyeznie kell azzal az értékkel, amelyet a **Felhasználónév**egyéni Azure ad-attribútumában konfigurált. A megfelelő leképezéssel az integrációnak működnie kell az Azure AD egyszeri bejelentkezés konfigurálásával.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a cég tabló-kiszolgáló rendszergazdájával.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a tabló-kiszolgáló csempére kattint, automatikusan be kell jelentkeznie a tabló-kiszolgálóra, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

