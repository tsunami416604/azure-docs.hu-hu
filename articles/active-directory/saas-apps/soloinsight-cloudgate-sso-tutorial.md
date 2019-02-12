---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Soloinsight-CloudGate SSO-val |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Soloinsight-CloudGate egyszeri bejelentkezés és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/07/2019
ms.author: jeedes
ms.openlocfilehash: 00a3a462ebdac49f0801205f7105a32914416d23
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56007995"
---
# <a name="tutorial-azure-active-directory-integration-with-soloinsight-cloudgate-sso"></a>Oktatóanyag: Az Azure Active Directory-integráció Soloinsight-CloudGate SSO-val

Ebben az oktatóanyagban elsajátíthatja, hogyan Soloinsight-CloudGate SSO integrálása az Azure Active Directory (Azure AD).
Soloinsight-CloudGate SSO integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá Soloinsight-CloudGate egyszeri bejelentkezés az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve Soloinsight-CloudGate egyszeri bejelentkezés (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Soloinsight-CloudGate SSO-val, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Soloinsight-CloudGate egyszeri bejelentkezés az egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Soloinsight-CloudGate egyszeri Bejelentkezést támogatja **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Soloinsight-CloudGate SSO hozzáadása a katalógusból

Soloinsight-CloudGate SSO integrálása az Azure AD beállítása, hozzá kell Soloinsight-CloudGate SSO a galériából a felügyelt SaaS-alkalmazások listájára.

**Soloinsight-CloudGate SSO hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Soloinsight-CloudGate SSO**, jelölje be **Soloinsight-CloudGate SSO** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![Az eredmények listájában Soloinsight-CloudGate SSO](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés alapján nevű tesztfelhasználó Soloinsight-CloudGate SSO-val **Britta Simon**.
Az egyszeri bejelentkezés működjön az Azure AD-felhasználót és a kapcsolódó felhasználó Soloinsight-CloudGate egyszeri bejelentkezési hivatkozás kapcsolata kell hozható létre.

Az Azure AD egyszeri bejelentkezés Soloinsight-CloudGate SSO-val tesztelése és konfigurálása, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Egyszeri bejelentkezés Soloinsight-CloudGate egyszeri bejelentkezés konfigurálása](#configure-soloinsight-cloudgate-sso-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Soloinsight-CloudGate SSO tesztfelhasználót](#create-soloinsight-cloudgate-sso-test-user)**  – egy megfelelője a Britta Simon Soloinsight-CloudGate SSO, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása Soloinsight-CloudGate SSO-val, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Soloinsight-CloudGate SSO** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Soloinsight-CloudGate SSO tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<SUBDOMAIN>.sigateway.com/login`

    b. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<SUBDOMAIN>.sigateway.com/process/sso`

    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és a később ismertetett azonosító ezeket az értékeket a **konfigurálása Soloinsight-CloudGate SSO egyszeri bejelentkezés** az oktatóanyag szakaszában.

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. Az a **Soloinsight-CloudGate egyszeri bejelentkezés beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-soloinsight-cloudgate-sso-single-sign-on"></a>Configure Soloinsight-CloudGate SSO Single Sign-On

1. Jelentkezzen be a CloudGate webes portálra, majd a hitelesítő adatok használatával, amelyek az Azure Portalon alapszintű SAML konfigurálása közben beillesztésre váró értékének lekéréséhez, nyissa meg az egyszeri bejelentkezési beállításainak, amely a következő elérési úton található **Kezdőlap > Administration > System Beállítások > Általános**.

    ![CloudGate SSO Settings](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

2. **SAML-ügyfél URL-címe**

    * Elleni elérhető hivatkozásokra kattintva másolja a **Saml ügyfél URL-címe** és a **átirányítási URL-cím** mezőket, és illessze be őket az Azure Portalon **alapszintű SAML-konfigurációja** akövetkezőszakaszban **Azonosító (entityid)** és **válasz URL-cím** mezők jelölik.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

3. **SAML-aláíró tanúsítvány**

    * Nyissa meg a tanúsítvány (Base64) Azure Portal SAML-aláíró tanúsítvány listák és kattintson rá a jobb gombbal a letöltött fájl forrása. Válasszon **Szerkesztés a Jegyzettömb ++** lehetőséget a listából. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Másolja a tartalmat a tanúsítvány (Base64) Jegyzettömb ++ fájlban.

        ![Tanúsítvány másolása](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Illessze be a CloudGate webes portál egyszeri bejelentkezési beállításainak **tanúsítvány** mezőbe, majd kattintson a Mentés gombra.

        ![Tanúsítvány-portál](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

4. **Alapértelmezett csoport**

    * Válassza ki **üzleti felügyeleti** a legördülő listából a **alapértelmezett csoport** CloudGate webes portálon való lehetőség

        ![Alapértelmezett csoport](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

5. **AD-azonosítóját, valamint a bejelentkezési URL-címe**

    * A másolt **bejelentkezési URL-cím** az Azure Portalról **Soloinsight-CloudGate egyszeri bejelentkezés beállítása** konfigurációk a következők megadni a CloudGate webes portál egyszeri bejelentkezési beállítások szakaszban. 

    * Illessze be a **bejelentkezési URL-cím** az Azure Portalról CloudGate webes portálon való hivatkozás **AD bejelentkezési URL-cím** mező.
     
    * Illessze be a **az Azure AD-azonosító** az Azure Portalról CloudGate webes portálon való hivatkozás **AD azonosító** mező

        ![Ad-bejelentkezés](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** írja be a következőt **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be a **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Soloinsight-CloudGate SSO Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Soloinsight-CloudGate SSO**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listájában jelölje ki a **Soloinsight-CloudGate SSO**.

    ![Az alkalmazások listáját a Soloinsight-CloudGate egyszeri bejelentkezési hivatkozás](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Soloinsight-CloudGate SSO tesztfelhasználó létrehozása

Hozzon létre egy tesztfelhasználót, jelölje be **alkalmazottak** a főmenüből a CloudGate webes portálon, és töltse ki az új hozzáadása alkalmazott képernyőt. A szolgáltató szintje, amely hozzá kell rendelni a tesztfelhasználó számára **üzleti felügyeleti** kattintson a **létrehozás** után az összes kötelező mezőt ki vannak töltve.

![Alkalmazott teszt](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Soloinsight-CloudGate SSO csempére kattint, meg kell kell automatikusan bejelentkezett a Soloinsight-CloudGate SSO, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

