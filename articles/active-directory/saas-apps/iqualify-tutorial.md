---
title: 'Oktatóanyag: Azure Active Directory integráció a iQualify LMS szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a iQualify LMS között.
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
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944991"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Oktatóanyag: Azure Active Directory integráció a iQualify LMS-vel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a iQualify LMS Azure Active Directory (Azure AD) szolgáltatással.
A iQualify LMS és az Azure AD integrálásával a következő előnyöket nyújtja:

* A iQualify LMS szolgáltatáshoz hozzáféréssel rendelkező Azure AD-t is vezérelheti.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a iQualify LMS-be (egyszeri bejelentkezés) az Azure AD-fiókjával.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció iQualify LMS-sel való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha még nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) kérhet egy hónapos próbaverziót
* iQualify LMS egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* a iQualify LMS támogatja **az SP és a identitásszolgáltató** által kezdeményezett SSO-t
* a iQualify LMS **csak időben támogatja a** felhasználók üzembe helyezését

## <a name="adding-iqualify-lms-from-the-gallery"></a>IQualify LMS hozzáadása a katalógusból

A iQualify-LMS Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a iQualify-LMS-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

**A iQualify LMS a katalógusból való hozzáadásához hajtsa végre a következő lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)** , kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **IQUALIFY LMS**kifejezést, válassza a **iQualify LMS** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![iQualify LMS az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést a iQualify LMS szolgáltatással konfigurálja és teszteli a **Britta Simon**nevű teszt felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a iQualify LMS-beli kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés iQualify LMS-sel való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Konfigurálja a IQUALIFY LMS egyszeri bejelentkezést](#configure-iqualify-lms-single-sign-on)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre IQUALIFY LMS-teszt felhasználót](#create-iqualify-lms-test-user)** – hogy rendelkezzen egy, a felhasználó Azure ad-Britta összekapcsolt, a IQUALIFY-LMS-ben található Simon-beli partnerrel.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés az iQualify LMS-sel való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **iQualify LMS** -alkalmazás integrációja lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![iQualify LMS-tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:
    | |
    |--|--|
    | Éles környezet:`https://<yourorg>.iqualify.com/`|
    | Tesztkörnyezet:`https://<yourorg>.iqualify.io`|

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:
    | |
    |--|--|
    | Éles környezet:`https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Tesztkörnyezet:`https://<yourorg>.iqualify.io/auth/saml2/callback` |

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![iQualify LMS-tartomány és URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:
    | |
    |--|--|
    | Éles környezet:`https://<yourorg>.iqualify.com/login` |
    | Tesztkörnyezet:`https://<yourorg>.iqualify.io/login` |

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Az értékek megszerzéséhez forduljon a [IQUALIFY LMS ügyfél-támogatási csapatához](https://www.iqualify.com/) . Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

6. A iQualify LMS-alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

7. A **felhasználó attribútumai** párbeszédpanel **felhasználói** jogcímek szakaszában szerkessze a jogcímeket a **Szerkesztés ikon** használatával, vagy adja hozzá a jogcímeket az **új jogcím hozzáadása** paranccsal az SAML-token attribútum konfigurálásához a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    | Name (Név) | Forrás attribútum|
    | --- | --- |
    | email | user.userprincipalname |
    | first_name | User. givenName |
    | last_name | felhasználó. vezetéknév |
    | person_id | "az Ön attribútuma" |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

    > [!Note]
    > A **person_id** attribútum nem **kötelező**

8. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállítások alapján, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

9. A **iQualify-LMS beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-iqualify-lms-single-sign-on"></a>IQualify LMS egyszeri bejelentkezés konfigurálása

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a iQualify-környezetbe.

1. Miután bejelentkezett, kattintson a jobb felső sarokban található avatarra, majd a **Fiókbeállítások** lehetőségre.

    ![Fiókbeállítások](./media/iqualify-tutorial/setting1.png)

1. A Fiókbeállítások területen kattintson a bal oldali menüszalag menüre, majd az integrációk elemre .

    ![INTEGRÁCIÓK](./media/iqualify-tutorial/setting2.png)

1. Az INTEGRÁCIÓk területen kattintson az **SAML** ikonra.

    ![SAML ikon](./media/iqualify-tutorial/setting3.png)

1. Az **SAML hitelesítési beállítások** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![SAML-hitelesítési beállítások](./media/iqualify-tutorial/setting4.png)

    a. Az **SAML egyszeri bejelentkezési szolgáltatás URL-címe** mezőbe illessze be az Azure ad-alkalmazás konfigurációs ablakából másolt **bejelentkezési URL** -értéket.

    b. Az **SAML KIjelentkezési URL-címe** mezőbe illessze be az Azure ad-alkalmazás konfigurációs ablakából másolt kijelentkezési **URL-** értéket.

    c. Nyissa meg a letöltött tanúsítványfájl a Jegyzettömbben, másolja a tartalmat, majd illessze be a **nyilvános tanúsítvány** mezőbe.

    d. A **bejelentkezési gomb feliratában** adja meg a bejelentkezési oldalon megjeleníteni kívánt gomb nevét.

    e. Kattintson a **SAVE** (Mentés) gombra.

    f. Kattintson a **frissítés**gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be a következőt: **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést az iQualify LMS elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **iQualify LMS**elemet.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **IQUALIFY LMS**elemet.

    ![Az iQualify LMS hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-iqualify-lms-test-user"></a>IQualify LMS-teszt felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a iQualify LMS-ben. a iQualify LMS az igény szerinti felhasználói üzembe helyezést is támogatja, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a iQualify LMS-ben, a rendszer egy újat hoz létre a hitelesítés után.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a iQualify LMS csempére kattint, beolvassa a iQualify LMS-alkalmazás bejelentkezési lapját. 

   ![bejelentkezési oldal](./media/iqualify-tutorial/login.png) 

Kattintson a **Bejelentkezés az Azure ad-vel** gombra, és automatikusan be kell jelentkeznie a iQualify LMS-alkalmazásba.

További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)