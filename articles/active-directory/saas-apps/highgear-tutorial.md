---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező HighGear |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és HighGear között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 55dcd2fb-96b7-46ec-9e69-eee71c535773
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/16/2019
ms.author: jeedes
ms.openlocfilehash: 71e95e0c4197ba53eaf641259858d3229036ee0c
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54808766"
---
# <a name="tutorial-azure-active-directory-integration-with-highgear"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező HighGear

Ebben az oktatóanyagban elsajátíthatja a HighGear integrálása az Azure Active Directory (Azure AD) is.
HighGear integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá HighGear Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve HighGear (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

HighGear az Azure AD-integráció konfigurálásához a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Olyan HighGear rendszer, amely egy vállalati vagy korlátlan licenc

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban elsajátíthatja a tesztelési környezetben az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása is.

* Támogatja a HighGear **SP és IdP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-highgear-from-the-gallery"></a>HighGear hozzáadása a katalógusból

Az Azure AD integrálása a HighGear konfigurálásához hozzá kell HighGear a katalógusból a felügyelt SaaS-alkalmazások listájára.

**HighGear hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, a bal oldali navigációs panelen, kattintson a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **HighGear**válassza **HighGear** eredmény panelen, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában HighGear](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban megismerheti a konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés az nevű tesztfelhasználó alapuló HighGear rendszer **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó a HighGear rendszer közötti kapcsolat kapcsolatot kell hozható létre.

Az Azure AD egyszeri bejelentkezés HighGear rendszerével tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[HighGear egyszeri bejelentkezés konfigurálása](#configure-highgear-single-sign-on)**  – HighGear alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálásához.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre HighGear tesztfelhasználót](#create-highgear-test-user)**  – egy megfelelője a Britta Simon HighGear, amely a felhasználó Azure ad-ben reprezentációja van csatolva van. 
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban megismerheti az Azure AD egyszeri bejelentkezés az Azure Portalon engedélyezéséhez.

Az Azure AD egyszeri bejelentkezés konfigurálása HighGear rendszerével, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **HighGear** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![HighGear tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szöveg mezőbe illessze be az értékét a **Service Provider Entitásazonosító** mezőben, hogy az egyszeri bejelentkezés beállításai lapon a HighGear rendszerben.

    ![A Service Provider Entitásazonosító mező](media/highgear-tutorial/service-provider-entity-id-field.png)
    
    > [!NOTE]
    > Jelentkezzen be az egyszeri bejelentkezés beállításai lap eléréséhez HighGear rendszerhez kell. Miután bejelentkezett, vigye az egérmutatót az HighGear felügyeleti lapot, és az egyszeri bejelentkezési beállításainak menüpontra.
    
    ![Az egyszeri bejelentkezési beállításainak menüpont](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

    b. Az a **válasz URL-cím** szöveg mezőbe illessze be az értékét a **helyességi feltétel fogyasztói Service (ACS) URL-cím** az egyszeri bejelentkezés beállításai lapon a HighGear rendszerben.

    ![A helyességi feltétel fogyasztói Service (ACS) URL-cím mező](media/highgear-tutorial/assertion-consumer-service-url-field.png)

    c. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

     ![HighGear tartomány és URL-címeket egyetlen bejelentkezési adatait](common/metadata-upload-additional-signon.png)

     Az a **bejelentkezési URL-** szöveg mezőbe illessze be az értékét a **Service Provider Entitásazonosító** mezőben, hogy az egyszeri bejelentkezés beállításai lapon a HighGear rendszerben. (Az entitás azonosítója a is az alap URL-címét a Szolgáltató által kezdeményezett bejelentkezéshez használandó HighGear rendszer.)

    ![A Service Provider Entitásazonosító mező](media/highgear-tutorial/service-provider-entity-id-field.png)

    > [!NOTE]
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-címet, a **egyszeri bejelentkezési beállításainak** lap a HighGear rendszerben. Ha segítségre van szüksége, lépjen kapcsolatba a [HighGear támogatási csapatának](mailto:support@highgear.com).

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** , és mentse a számítógépre. Az egyszeri bejelentkezés konfiguráció egy későbbi lépésben szüksége lehet.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **HighGear beállítása** szakaszban, vegye figyelembe a következő URL-címek helyét.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL-címe. Ez az érték a 2. lépés alatt kell **konfigurálása HighGear egyszeri bejelentkezés** alatt.

    b. Az Azure AD-azonosítója. Ez az érték 3. lépésben alatt kell **konfigurálása HighGear egyszeri bejelentkezés** alatt.

    c. Kijelentkezési URL-címe. Ez az érték a 4. lépés alatt kell **konfigurálása HighGear egyszeri bejelentkezés** alatt.

### <a name="configure-highgear-single-sign-on"></a>HighGear egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés HighGear konfigurálásához jelentkezzen be a HighGear rendszer. Miután bejelentkezett, vigye az egérmutatót az HighGear felügyeleti lapot, és az egyszeri bejelentkezési beállításainak menüpontra.

![Az egyszeri bejelentkezési beállításainak menüpont](media/highgear-tutorial/single-sign-on-settings-menu-item.png)

1. Az a **identitásszolgáltató neve**, írja be egy rövid leírást, amely megjelenik majd a bejelentkezési oldalon HighGear az egyszeri bejelentkezés gombot. Példa: Azure AD

2. Az a **egyszeri bejelentkezéses (SSO) URL-cím** HighGear mezőbe illessze be az értéket a **bejelentkezési URL-cím** lévő mező kerül a **HighGear beállítása** szakaszban az Azure-ban.

3. Az a **Identity Provider Entitásazonosító** HighGear mezőbe illessze be az értéket a **az Azure AD-azonosító** lévő mező kerül a **HighGear beállítása** szakaszban az Azure-ban.

4. Az a **egyszeri kijelentkezés (slo) Szolgáltatásának URL-cím** HighGear mezőbe illessze be az értéket a **kijelentkezési URL-címe** lévő mező kerül a **HighGear beállítása** szakaszban az Azure-ban.

5. Nyissa meg a tanúsítványt, amelyikbe kibontotta a Jegyzettömb alkalmazással a **SAML-aláíró tanúsítvány** szakaszban az Azure-ban. Érdemes már letöltötte a **tanúsítvány (Base64)** formátumban. Másolja a tartalmat, a tanúsítvány a Jegyzettömb, és illessze be azt a **szolgáltató Identitástanúsítványt** HighGear mezőbe.

6. E-mailt a [HighGear támogatási csapatának](mailto:support@highgear.com) a HighGear tanúsítványt kér. Kövesse az utasításokat, kapott kérik tőle a **HighGear tanúsítvány** és **HighGear tanúsítvány jelszava** mezőket.

7. Kattintson a **mentése** gombra kattintva mentse a HighGear egyszeri bejelentkezés konfigurációt.

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

    c. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés HighGear Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**, majd válassza ki **HighGear**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **HighGear**.

    ![Az alkalmazások listáját a HighGear hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-highgear-test-user"></a>HighGear tesztfelhasználó létrehozása

Az egyszeri bejelentkezés konfigurációjának tesztelése, jelentkezzen be a HighGear rendszer HighGear tesztfelhasználó létrehozása.

1. Kattintson a **hozzon létre új ügyfél** gombra.

    ![Az új ügyfél létrehozása gomb](media/highgear-tutorial/create-new-contact-button.png)

    Lehetővé teszi a létrehozni kívánt kapcsolat fajtája menü jelenik meg.

2. Kattintson a **egyes** menüpont HighGear felhasználó létrehozásához.

    A panel becsúszik jobb úgy, hogy az új felhasználó adatait a megadható.  
    ![Az új ügyfél űrlap](media/highgear-tutorial/new-contact-form.png)

3. Az a **neve** mezőbe írja be a kapcsolattartó nevét. Példa: Britta Simon

4. Kattintson a **további beállítások** menüre, majd válassza a **fiókadatok** menüpontot.

    ![Kattintson a fiók adatai menüelem](media/highgear-tutorial/account-info-menu-item.png)

5. Állítsa be a **is bejelentkezés** mezőben Igen.

    A **engedélyezése egyszeri bejelentkezés** mező lesz automatikusan Igen értékre kell állítani is.

6. Az a **egyszeri bejelentkezéses felhasználói azonosító** mezőbe írja be a felhasználói azonosítóját. Például:BrittaSimon@contoso.com

    A fiókadatok szakaszban kell kinéznie ehhez hasonló:  
    ![A befejezett fiókadatok szakasz](media/highgear-tutorial/finished-account-info-section.png)

7. A partner mentéséhez kattintson a **mentése** gombra a panel alján.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a HighGear csempére kattint, meg kell lehet automatikusan bejelentkezett a HighGear, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

