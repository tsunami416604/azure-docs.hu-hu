---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Adobe Sign |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az Adobe Sign között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8942ebf3f006c2e1cc72b322dd243d46bf69f04d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57888127"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Oktatóanyag: Az Azure Active Directory integrációja az Adobe Sign

Ebben az oktatóanyagban elsajátíthatja, hogyan Adobe Sign integrálása az Azure Active Directory (Azure AD).
Az Adobe Sign integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá az Adobe Sign Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy a rendszer automatikusan bejelentkezett az Adobe Sign (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az Adobe Sign, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Az Adobe Sign egy bejelentkezési engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Támogatja az Adobe Sign **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-adobe-sign-from-the-gallery"></a>Az Adobe Sign hozzáadása a katalógusból

Az Azure AD-be az Adobe Sign integráció konfigurálásához, hozzá kell Adobe Sign a galériából a felügyelt SaaS-alkalmazások listájára.

**Az Adobe Sign hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Adobe Sign**, jelölje be **Adobe Sign** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az Adobe Sign a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Adobe Sign nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezés működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó az Adobe Sign hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés az Adobe Sign tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Adobe Sign egyszeri bejelentkezés konfigurálása](#configure-adobe-sign-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre az Adobe Sign tesztfelhasználó](#create-adobe-sign-test-user)**  - a-megfelelője a Britta Simon rendelkezik az Adobe Sign, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Konfigurálja az Azure AD egyszeri bejelentkezés az Adobe Sign, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), az a **Adobe Sign** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Az Adobe Sign tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<companyname>.echosign.com/`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<companyname>.echosign.com`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és azonosító ezeket az értékeket. Kapcsolattartó [az Adobe Sign ügyfél-támogatási csapatának](https://helpx.adobe.com/in/contact/support.html) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **beállítása az Adobe Sign** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-adobe-sign-single-sign-on"></a>Az Adobe Sign egyszeri bejelentkezés konfigurálása

1. Mielőtt konfigurációs, lépjen kapcsolatba a [az Adobe Sign ügyfél-támogatási csapatának](https://helpx.adobe.com/in/contact/support.html) az engedélyezési listára saját tartományt az Adobe Sign az. Íme a tartomány hozzáadása:

    a. A [az Adobe Sign ügyfél-támogatási csapatának](https://helpx.adobe.com/in/contact/support.html) küld Önnek egy véletlenszerűen létrehozott tokent. A tartomány a jogkivonat az alábbihoz hasonló lesz: **adobe-bejelentkezés-ellenőrző = xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Az ellenőrzési token közzététele a DNS-szöveges rekord, és értesítheti a [az Adobe Sign ügyfél-támogatási csapatának](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Ez is igénybe vehet néhány nap, vagy már. Vegye figyelembe, hogy a DNS-propagálás késések jelenti azt, hogy egy érték közzététele a DNS-ben nem lesz látható a tartozó egy vagy több óráig. A rendszergazdának kell lennie a token közzététele a DNS-szöveges rekord alapos ismerete.
    
    c. Ha értesíteni a [az Adobe Sign ügyfél-támogatási csapatának](https://helpx.adobe.com/in/contact/support.html) keresztül egy támogatási jegyet a token közzététele után, a tartomány érvényesítése, és adja hozzá a fiókjához.
    
    d. Általában a következő közzététele a DNS-rekord a jogkivonatot:

    * Jelentkezzen be a tartományi fiók
    * A DNS-rekord frissítésére szolgáló lap található. Ez a lap neve lehet DNS-kezelést, a névkiszolgáló kezelése vagy a Speciális beállítások.
    * Find the TXT records for your domain.
    * Adjon hozzá egy txt típusú rekordot a teljes token Adobe által megadott értékkel.
    * Mentse a módosításokat.

1. Egy másik böngészőablakban jelentkezzen be az Adobe Sign vállalati hely rendszergazdaként.

1. A SAML menüben válassza ki a **fiókbeállításokat** > **SAML-beállítások**.
   
    ![Képernyőkép az Adobe Sign SAML-beállítások oldal](./media/adobe-echosign-tutorial/ic789520.png "fiók")

1. Az a **SAML-beállítások** szakaszban, hajtsa végre az alábbi lépéseket:
  
   ![Képernyőkép a SAML-beállítások](./media/adobe-echosign-tutorial/ic789521.png "SAML-beállítások")
   
   ![Képernyőkép a SAML-beállítások](./media/adobe-echosign-tutorial/ic789522.png "SAML-beállítások")

   a. A **SAML mód**válassza **SAML kötelező**.
   
   b. Válassza ki **Echosign Fiókrendszergazdák engedélyezése Echosign hitelesítő adataival bejelentkezni**.
   
   c. A **felhasználó létrehozása**válassza **elvégezte a hitelesítést SAML felhasználóinak automatikus hozzáadása**.

   d. Beillesztés **Azure Ad-azonosító**, azokat az Azure Portalról másolt a **identitásszolgáltató Entitásazonosító** szövegmező.
    
   e. Beillesztés **bejelentkezési URL-cím**, az Azure Portalról másolt a **identitásszolgáltató bejelentkezési URL-cím** szövegmezőben.
   
   f. Beillesztés **kijelentkezési URL-címe**, az Azure Portalról másolt a **identitásszolgáltató kijelentkezési URL-címe** szövegmezőben.

   g. Nyissa meg a letöltött **Certificate(Base64)** fájlt a Jegyzettömbben. A tartalmát a vágólapra másolja és illessze be azt a **identitásszolgáltató tanúsítvány** szövegmezőben.

   h. Válassza ki **módosítások mentése**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be **brittasimon\@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával az Adobe Sign Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Adobe Sign**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **Adobe Sign**.

    ![Az alkalmazások listáját az Adobe Sign hivatkozásra](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-adobe-sign-test-user"></a>Az Adobe Sign tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelentkezzen be Adobe Sign, akkor ki kell építeni az Adobe Sign. Ez a manuális feladat.

>[!NOTE]
>Az Adobe Sign felhasználói fiók létrehozása eszközöket és az Adobe Sign által biztosított API-k segítségével az Azure AD-felhasználói fiókok kiépítése. 

1. Jelentkezzen be a **Adobe Sign** rendszergazdaként a vállalati webhely.

2. A felső menüben válassza ki a **fiók**. Majd a bal oldali panelen válassza ki **felhasználók és csoportok** > **hozzon létre egy új felhasználót**.
   
    ![Képernyőkép az Adobe Sign hely, a fiók, felhasználók és csoportok, vállalati, és hozzon létre egy új felhasználót kiemelt](./media/adobe-echosign-tutorial/ic789524.png "fiók")
   
3. Az a **új felhasználó létrehozása** szakaszban, hajtsa végre az alábbi lépéseket:
   
    ![Képernyőfelvétel az új felhasználó létrehozása szakaszban](./media/adobe-echosign-tutorial/ic789525.png "felhasználó létrehozása")
   
    a. Írja be a **E-mail cím**, **Utónév**, és **Vezetéknév** egy érvényes Azure AD-fiók létrehozásához a kapcsolódó szövegmezőket szeretne.
   
    b. Válassza ki **felhasználó létrehozása**.

>[!NOTE]
>Az Azure Active Directory fióktulajdonos kap egy e-mailt, amelyben a hivatkozást a fiók megerősítéséhez, mielőtt aktívvá válik. 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen az Adobe Sign csempére kattint, meg kell lehet automatikusan bejelentkezett az Adobe Sign, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

