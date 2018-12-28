---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Atlassian felhővel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az Atlassian felhő között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/20/2018
ms.author: jeedes
ms.openlocfilehash: 4ea8901db71176c0b4d378131ca2f351897e45eb
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53789432"
---
# <a name="tutorial-azure-active-directory-integration-with-atlassian-cloud"></a>Oktatóanyag: Az Azure Active Directory-integráció Atlassian felhővel

Ebben az oktatóanyagban elsajátíthatja, hogyan Atlassian Felhőbeli integrálása az Azure Active Directory (Azure AD).
Atlassian Felhőbeli integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Az Azure ad-ben Atlassian felhőbe való hozzáféréssel rendelkező szabályozhatja.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett Atlassian felhőbe (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Atlassian felhőalapú Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Atlassian felhőalapú egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja az Atlassian felhőalapú **SP és IDP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Atlassian felhő hozzáadása a katalógusból

Atlassian Felhőbeli integrálása az Azure AD beállítása, hozzá kell Atlassian felhőalapú a galériából a felügyelt SaaS-alkalmazások listájára.

**Atlassian felhő hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Atlassian felhőalapú**, jelölje be **Atlassian felhőalapú** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Atlassian felhő](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálni és Atlassian felhővel az Azure AD egyszeri bejelentkezés tesztelése alapján nevű tesztfelhasználó **Britta Simon**.
Az egyszeri bejelentkezés működjön egy Azure AD-felhasználót és a kapcsolódó felhasználó Atlassian felhőben hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés Atlassian felhővel tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Felhőalapú Atlassian egyszeri bejelentkezés konfigurálása](#configure-atlassian-cloud-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Hozzon létre Atlassian felhőalapú tesztfelhasználót](#create-atlassian-cloud-test-user)**  - a-megfelelője a Britta Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása Atlassian felhőben.
5. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Atlassian felhő, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Atlassian felhőalapú** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, hajtsa végre az alábbi lépéseket:

    ![[Alkalmazásnév] Tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-relay.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://auth.atlassian.com/saml/<unique ID>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Kattintson a **további URL-címet beállítani**.

    d. Az a **továbbítási állapot** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Az előző értékek nem valódi. Az értékeket módosítsa a tényleges azonosítóval, és a válasz URL-címe. Valódi ezeket az értékeket az oktatóanyag későbbi részében ismertetett Atlassian felhőalapú SAML-konfigurációja képernyőről fog kapni.

5. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![[Alkalmazásnév] Tartomány és URL-címeket egyetlen bejelentkezési adatait](common/both-signonurl.png)

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<instancename>.atlassian.net`

    > [!NOTE]
    > A fenti bejelentkezési URL-érték nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Atlassian felhőalapú ügyfél-támogatási csapatának](https://support.atlassian.com/) lekérni ezt az értéket.

6. Atlassian felhőalapú alkalmazás vár egy megadott formátumban, amelyhez egyéni attribútum-leképezéshez hozzá a SAML-jogkivonat attribútumai a SAML helyességi feltételek kereséséhez.

    Alapértelmezés szerint a **felhasználóazonosító** érték user.userprincipalname van leképezve. Ez az érték user.mail leképezése módosítható. Azt is beállíthatja bármely más megfelelő érték a szervezet telepítő megfelelően, de a legtöbb esetben működnie kell az e-mailt. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

7. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    a. Kattintson a **Szerkesztés ikon** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    ![image](./media/atlassian-cloud-tutorial/tutorial_usermail.png)

    ![image](./media/atlassian-cloud-tutorial/tutorial_usermailedit.png)

    b. Az a **forrásattribútum** listáról válassza ki **user.mail**.

    c. Kattintson a **Save** (Mentés) gombra.

8. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

9. Az a **Atlassian felhő beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Az Azure Ad-azonosító

    c. Kijelentkezési URL

### <a name="configure-atlassian-cloud-single-sign-on"></a>Atlassian felhőalapú egyszeri bejelentkezés konfigurálása

1. Egyszeri bejelentkezés az alkalmazáshoz konfigurált kapni, jelentkezzen be rendszergazdai hitelesítő adatait az Atlassian-portálra.

2. Ellenőrizze a tartományt egyszeri bejelentkezés konfigurálása előtt kell. További információkért lásd: [Atlassian tartomány-ellenőrzés](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) dokumentumot.

3. A bal oldali panelen válassza ki a **SAML egyszeri bejelentkezés**. Ha ezt még nem tette meg, az előfizetés Atlassian Identity Manager.

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_11.png)

4. Az a **hozzáadása SAML-konfigurációja** ablakban tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_12.png)

    a. Az a **identitásszolgáltató Entitásazonosító** mezőbe illessze be az SAML-entitás azonosítója az Azure Portalról másolt.

    b. Az a **identitásszolgáltató egyszeri bejelentkezési URL-cím** mezőbe illessze be a SAML egyszeri bejelentkezési szolgáltatás URL-cím, az Azure Portalról másolt.

    c. Nyissa meg a letöltött tanúsítvány egy .txt fájlban, az Azure portálról másolja az értéket (nélkül a *megkezdéséhez tanúsítvány* és *End Certificate* sorok), majd illessze be a a **nyilvános X509 tanúsítvány** mezőbe.

    d. Kattintson a **konfiguráció mentése**.

5. Győződjön meg arról, hogy meg van adva a megfelelő URL-címeket, frissítse az Azure AD-beállításokat az alábbiak szerint:

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_13.png)

    a. A SAML ablakban másolja a **SP Szolgáltatásidentitás azonosítója** , majd az Azure portal alatt Atlassian felhőalapú **tartomány és URL-címek**, illessze be a **azonosító** mezőbe.

    b. Az SAML ablakban másolja a **SP helyességi feltétel fogyasztói szolgáltatás URL-címe** , majd az Azure portal alatt Atlassian felhőalapú **tartomány és URL-címek**, illessze be a **válasz URL-cím** mezőbe. A bejelentkezési URL-bérlői URL-címét az Atlassian Felhőbeli.

    > [!NOTE]
    > Hogy egy meglévő ügyfél frissítése után a **SP Szolgáltatásidentitás azonosítója** és **SP helyességi feltétel fogyasztói szolgáltatás URL-címe** értékeket az Azure Portalon válassza ki **Igen, frissítse a konfigurációt**. Ha Ön új ügyfél, kihagyhatja ezt a lépést.

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

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával Atlassian felhőbe Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Atlassian felhőalapú**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **Atlassian felhőalapú**.

    ![Az alkalmazások listáját az Atlassian felhőalapú hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-atlassian-cloud-test-user"></a>Tesztfelhasználó Atlassian felhő létrehozása

Ahhoz, hogy jelentkezzen be a Atlassian felhőalapú Azure AD-felhasználók, a felhasználói fiókok manuális Atlassian felhőben kiosztani az alábbiak szerint:

1. Az a **felügyeleti** ablaktáblán válassza előbb **felhasználók**.

    ![A Felhőbeli felhasználók Atlassian-hivatkozás](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_14.png)

2. Egy felhasználó Atlassian felhőben létrehozásához válassza **meghívó felhasználói**.

    ![Hozzon létre egy Atlassian Felhőbeli felhasználó](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_15.png)

3. Az a **E-mail-cím** mezőbe írja be a felhasználó e-mail címét, és rendelje hozzá az alkalmazás-hozzáférést.

    ![Hozzon létre egy Atlassian Felhőbeli felhasználó](./media/atlassian-cloud-tutorial/tutorial_atlassiancloud_16.png)

4. Szeretne küldeni a felhasználónak e-mailes meghívót, válassza ki a **felhasználók meghívása**. E-mailt küld a felhasználónak, és elfogadja a meghívást, miután a felhasználó nem aktív, a rendszer.

> [!NOTE]
> Tömegesen is-felhasználók létrehozása kiválasztásával a **tömeges létrehozásához** gombra a **felhasználók** szakaszban.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Atlassian felhőalapú csempére kattint, akkor kell automatikusan megtörténik a a Atlassian felhőbe, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
   