---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Adobe Creative Cloud |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Adobe Creative Cloud és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: c199073f-02ce-45c2-b515-8285d4bbbca2
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: ccb593e198e4dff8f30bddfe26071523ea7a893d
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56179229"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-creative-cloud"></a>Oktatóanyag: Az Adobe Creative Cloud az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható az Adobe Creative Cloud szolgáltatásban az Azure Active Directoryval (Azure AD).
Az Adobe Creative Cloud szolgáltatásban integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, aki hozzáféréssel rendelkezik az Adobe Creative cloud szolgáltatásban az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett az Adobe Creative cloud szolgáltatásban (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Adobe Creative Cloud, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Az Adobe Creative Cloud szolgáltatásban egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja az Adobe Creative Cloud **SP és IDP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-adobe-creative-cloud-from-the-gallery"></a>Az Adobe Creative Cloud szolgáltatásban hozzáadása a katalógusból

Az Adobe Creative Cloud szolgáltatásban integrálása az Azure AD beállítása, hozzá kell az Adobe Creative Cloud szolgáltatásban a galériából a felügyelt SaaS-alkalmazások listájára.

**Az Adobe Creative Cloud szolgáltatásban hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Adobe Creative Cloud**, jelölje be **Adobe Creative Cloud** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az Adobe Creative Cloud szolgáltatásban a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Adobe Creative Cloud nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és az Adobe Creative Cloud szolgáltatásban a kapcsolódó felhasználó közötti kapcsolat kapcsolatot kell hozható létre.

Az Azure AD egyszeri bejelentkezés az Adobe Creative Cloud tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Adobe Creative Cloud egyszeri bejelentkezés konfigurálása](#configure-adobe-creative-cloud-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre az Adobe Creative Cloud szolgáltatásban tesztfelhasználót](#create-adobe-creative-cloud-test-user)**  - a-megfelelője a Britta Simon szerepel az Adobe Creative Cloud szolgáltatásban, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezés az Adobe Creative Cloud, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Adobe Creative Cloud** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha a konfigurálni kívánt **Identitásszolgáltató** intiated mód hajtsa végre a következő lépéseket:

    ![Az Adobe Creative Cloud tartomány és URL-címek egyszeri bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://www.okta.com/saml2/service-provider/<token>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<company name>.okta.com/auth/saml20/accauthlinktest`

    > [!NOTE]
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóval, válasz URL-cím. Kapcsolattartó [az Adobe Creative Cloud ügyfél-támogatási csapatának](https://www.adobe.com/au/creativecloud/business/teams/plans.html) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    Az a **bejelentkezési URL-** szöveg írja be az értéket: `https://adobe.com`.

    ![Az Adobe Creative Cloud tartomány és URL-címek egyszeri bejelentkezési adatait](common/metadata-upload-additional-signon.png)

6. Az Adobe Creative Cloudba feltöltött alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

7. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
 
    | Name (Név) | Adatforrás-attribútum|
    |----- | --------- |
    | FirstName | user.givenname |
    | LastName | user.surname |
    | E-mail | user.mail

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > Felhasználók kell licenccel kell rendelkeznie egy érvényes Office 365 ExO e-mailek jogcím értéke a SAML-válasz kell feltöltenie.

8. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

9. Az a **beállítása az Adobe Creative Cloud** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-adobe-creative-cloud-single-sign-on"></a>Az Adobe Creative Cloud szolgáltatásban egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban, jelentkezzen be [Adobe felügyeleti konzol](https://adminconsole.adobe.com) rendszergazdaként.

2. Lépjen a **beállítások** a felső navigációs sávot, és válassza a **identitás**. Megnyílik a tartományok listáját. Kattintson a **konfigurálása** hivatkozásra a tartomány alapján. Hajtsa végre az alábbi lépéseket a **egyszeri bejelentkezést a konfiguráció szükséges** szakaszban. További információkért lásd: [tartomány beállítása](https://helpx.adobe.com/enterprise/using/set-up-domain.html)

    ![Beállítások](https://helpx.adobe.com/content/dam/help/en/enterprise/using/configure-microsoft-azure-with-adobe-sso/_jcr_content/main-pars/procedure_719391630/proc_par/step_3/step_par/image/edit-sso-configuration.png "beállításai")

    a. Kattintson a **Tallózás** feltöltése az Azure AD-ből a letöltött tanúsítvány **Identitásszolgáltató tanúsítvány**.

    b. Az a **Identitásszolgáltató kibocsátója** szövegmezőbe put értékét **SAML entitásazonosító** a másolt **bejelentkezés konfigurálása** szakaszban az Azure Portalon.

    c. Az a **Identitásszolgáltató bejelentkezési URL-cím** szövegmezőbe put értékét **SAML egyszeri bejelentkezési szolgáltatás URL-címe** a másolt **bejelentkezés konfigurálása** szakaszban az Azure Portalon.

    d. Válassza ki **HTTP - átirányítási** , **Identitásszolgáltató kötés**.

    e. Válassza ki **E-mail-cím** , **felhasználói bejelentkezési beállítás**.

    f. Kattintson a **mentése** gombra.

3. Az irányítópulton, most már megjelenik az XML-fájl **"Metaadatok letöltése"** fájlt. Adobe EntityDescriptor és AssertionConsumerService URL-címe tartalmazza. Nyissa meg a fájlt, és konfigurálja őket az Azure AD-alkalmazás.

    ![Egyszeri bejelentkezés az alkalmazás oldalán konfigurálása](./media/adobe-creative-cloud-tutorial/tutorial_adobe-creative-cloud_003.png)

    a. Használja az Adobe a megadott EntityDescriptor értéket **azonosító** a a **Alkalmazásbeállítások konfigurálása** párbeszédpanel.

    b. Használja az Adobe a megadott AssertionConsumerService értéket **válasz URL-cím** a a **Alkalmazásbeállítások konfigurálása** párbeszédpanel.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használandó Azure egyszeri bejelentkezést a hozzáférés biztosításával az Adobe Creative cloud szolgáltatásban.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Adobe Creative Cloud**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Adobe Creative Cloud**.

    ![Az alkalmazások listáját az Adobe Creative Cloud hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-adobe-creative-cloud-test-user"></a>Az Adobe Creative Cloud szolgáltatásban tesztfelhasználó létrehozása

Ahhoz, hogy jelentkezzen be Adobe Creative Cloud az Azure AD-felhasználók, akkor ki kell építeni az Adobe Creative Cloud szolgáltatásban. Az Adobe Creative Cloud, esetén kézi tevékenység kiépítése.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>A felhasználói fiókok kiépítése, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a [Adobe felügyeleti konzol](https://adminconsole.adobe.com) hely rendszergazdaként.

2. Összevont ID Adobe konzolon belül a felhasználó hozzáadása, és hozzárendelheti őket egy termék profilt. Felhasználók hozzáadásával kapcsolatos részletes információkért lásd: [-felhasználók hozzáadása az Adobe felügyeleti konzol](https://helpx.adobe.com/enterprise/using/users.html#Addusers) 

3. Ezen a ponton az Adobe bejelentkezési űrlapot, nyomja le a tab, írjon be az e-mail cím egyszerű és kell összevonva az Azure AD vissza:
    * A webes elérés: www.adobe.com > jelentkezzen be
    * Az asztali alkalmazás segédprogram belül > jelentkezzen be
    * Az alkalmazáson belül > Súgó > jelentkezzen be

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen az Adobe Creative Cloud csempére kattint, akkor kell automatikusan megtörténik a az Adobe Creative cloud szolgáltatásban, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
  
- [Tartomány (Adobe.com webhelyre) beállítása](https://helpx.adobe.com/enterprise/using/set-up-domain.html)
  
- [Az Adobe egyszeri bejelentkezés (Adobe.com webhelyre) való használatra Azure konfigurálása](https://helpx.adobe.com/enterprise/kb/configure-microsoft-azure-with-adobe-sso.html)
