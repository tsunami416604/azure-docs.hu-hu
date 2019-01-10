---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a helyszíni SharePoint |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a helyszíni SharePoint-között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/24/2018
ms.author: jeedes
ms.openlocfilehash: 789f58699f39f4b7eac453f4cf79ea55a5bfc8d3
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54159502"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Oktatóanyag: Az Azure Active Directory-integráció a helyszíni SharePoint

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a helyszíni SharePoint az Azure Active Directory (Azure AD).
Helyszíni SharePoint-integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a helyszíni SharePoint az Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve a helyi Sharepointot (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a helyszíni SharePoint, a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Helyszíni SharePoint-egyszeri bejelentkezés engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Helyszíni SharePoint-támogatja **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Helyszíni SharePoint-Hozzáadás a katalógusból

Az Azure AD-be a helyszíni SharePoint-integráció konfigurálásához, hozzá kell a helyszíni SharePoint a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Helyszíni SharePoint-hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **helyszíni SharePoint-**, jelölje be **helyszíni SharePoint-** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![A helyi Sharepointot a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a SharePoint helyszíni nevű tesztfelhasználó alapján **Britta Simon**.
Az egyszeri bejelentkezés működéséhez egy Azure AD-felhasználót és a kapcsolódó felhasználó a Sharepointban hivatkozás kapcsolata a helyszíni kell létrehozni.

Az Azure AD egyszeri bejelentkezés a helyszíni SharePoint tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[A helyszíni SharePoint egyszeri bejelentkezés konfigurálása](#configure-sharepoint-on-premises-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[A hozzáférési jogot a SharePoint helyszíni tesztfelhasználó](#grant-access-to-sharepoint-on-premises-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon a helyszíni SharePoint-, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

Az Azure AD egyszeri bejelentkezés konfigurálása a helyszíni SharePoint, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **helyszíni SharePoint-** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![A SharePoint a helyszíni tartomány és URL-címeket egyetlen bejelentkezési adatait](common/sp-identifier-reply.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. Az a **azonosító** mezőbe írja be a következő minta használatával URL-cím: `urn:sharepoint:federation`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<YourSharePointServerURL>/_trust/default.aspx`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-azonosító és válasz URL-cím. Kapcsolattartó [helyszíni SharePoint-ügyfél-támogatási csapatának](https://support.office.com/) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

    > [!Note]
    > Megjegyzés: a fájl elérési útja, amelybe letöltötte a tanúsítványfájlt. Később a PowerShell-parancsfájlt a fájl a konfiguráció szükséges.

6. Az a **beállítása a helyszíni SharePoint-** területén másolja megfelelően a követelmény a megfelelő URL-címe. A **egyszeri bejelentkezési szolgáltatás URL-cím**, az érték a következő mintának: `https://login.microsoftonline.com/_my_directory_id_/wsfed` 

    > [!Note]
    > _my_directory_id_ a bérlő azonosítója, az Azure Ad-előfizetés.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Az Azure Ad-azonosító

    c. Kijelentkezési URL

    > [!NOTE]
    > Helyszíni SharePoint-alkalmazás a SAML 1.1-es használja a SAML 1.1-es általa token, így az Azure ad-ben a WS-Fed kérelmet a SharePoint-kiszolgáló és a hitelesítés után vár. token.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>A helyszíni SharePoint egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a SharePoint helyszíni vállalati webhely rendszergazdaként.

2. **A SharePoint Server 2016 egy új megbízható identitásszolgáltatóra konfigurálása**

    Jelentkezzen be a SharePoint Server 2016-kiszolgálón, és nyissa meg a SharePoint 2016 felügyeleti rendszerhéjat. $Wsfedurl (egyszeri bejelentkezési szolgáltatás URL-cím), és $filepath $realm (azonosító szakaszból származó értékre a SharePoint a helyszíni tartomány és URL-címek az Azure Portalon), az értékét adja meg (a fájl elérési útja, amelyhez a tanúsítvány-fájl letöltése) az Azure Portalon, és futtatása a következő parancsokat egy új megbízható identitásszolgáltatóra konfigurálásához.

    > [!TIP]
    > Ha használja a PowerShell-lel vagy a PowerShell működésével kapcsolatos további tudnivalókat a kívánt [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps). 

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3,$map4 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Ezután kövesse az alábbi lépéseket az alkalmazás megbízható identitásszolgáltatóra engedélyezése:

    a. Lépjen a központi felügyelet **webalkalmazás kezelése** , és válassza ki a webalkalmazást, amely meg szeretne védeni az Azure ad-ben.

    b. A menüszalagon kattintson **hitelesítésszolgáltatók** , és válassza a zónában, amelyet használni kíván.

    c. Válassza ki **megbízható identitásszolgáltató** , és válassza ki az identitás-szolgáltató csak regisztrált nevű *AzureAD*.

    d. Válassza ki a bejelentkezési oldal URL-cím beállítás **egyéni bejelentkezési oldalon** , és a "/_trust/" értéket.

    e. Kattintson az **OK** gombra.

    ![A hitelesítésszolgáltató konfigurálása](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

    > [!NOTE]
    > Néhány külső felhasználók nem tudják Felhasználónevük lesz összekeveredett értékének például az egyszeri bejelentkezés integráció használandó `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Hamarosan lehetővé teszi a Microsoft egyéni alkalmazáskonfigurációjából kezeli az egyszerű felhasználónév a felhasználó típusától függően. Ezt követően a vendégfelhasználók egyszeri Bejelentkezéssel a szervezeti alkalmazottak zökkenőmentesen adatokként képesnek kell lennie.

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

### <a name="grant-access-to-sharepoint-on-premises-test-user"></a>Hozzáférés engedélyezése a SharePoint helyszíni tesztfelhasználó számára

A felhasználók, akik jelentkezzen be az Azure AD és a SharePoint eléréséhez fog hozzáférést kell adni az alkalmazáshoz az alábbi lépések segítségével állítsa be az engedélyeket a webalkalmazáshoz való hozzáférés.

1. Kattintson a központi felügyelet **Alkalmazáskezelés**.

2. Az a **Alkalmazáskezelés** lap a **webalkalmazások** területén kattintson **webalkalmazások kezelése**.

3. Kattintson a megfelelő webalkalmazás, majd a **felhasználói házirend**.

4. Kattintson a webalkalmazás-házirend **felhasználó hozzáadása**.

    ![A jogcím nevét a felhasználó keresése](./media/sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

5. Az a **felhasználó hozzáadása** párbeszédpanelen kattintson a megfelelő zónához az **zónák**, és kattintson a **tovább**.

6. A a **házirend-webalkalmazás** párbeszédpanel a **felhasználók kiválasztása** területén kattintson a **Tallózás** ikonra.

7. Az a **található** szövegmezőbe írja be a **felhasználó egyszerű felhasználóneve** érték, amelyhez az Azure AD-ban beállított a SharePoint helyszíni alkalmazás, és kattintson a **keresési**. </br>Példa: *brittasimon@contoso.com*.

8. Az Azure ad a listanézet cím alatt válassza ki a name tulajdonság, majd kattintson **Hozzáadás** kattintson **OK** a párbeszédpanel bezárásához.

9. Az engedélyek párbeszédpanelen kattintson a **teljes hozzáférés**.

    ![Teljes hozzáférés biztosítása a felhasználói igények](./media/sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

10. Kattintson a **Befejezés**, és kattintson a **OK**.

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Egy megbízható identitásszolgáltatóra több webalkalmazás konfigurálása

A konfiguráció egyetlen webalkalmazás működik, de további konfigurálást igényel, ha több webalkalmazás ugyanazon a megbízható identitásszolgáltatóra használni szeretne. Tegyük fel például, egy webalkalmazás, az URL-cím használatára volt kiterjesztettük `https://portal.contoso.local` , és most szeretné hitelesíteni a felhasználókat `https://sales.contoso.local` is. Ehhez az identitásszolgáltató tartsa tiszteletben a WReply paramétert, és frissítse az alkalmazás regisztrálása az Azure ad-ben a válasz URL-cím hozzáadása frissíteni kell.

1. Az Azure Portalon nyissa meg az Azure AD-címtárban. Kattintson a **alkalmazásregisztrációk**, majd kattintson a **minden alkalmazás megtekintése**. Kattintson a korábban létrehozott alkalmazásra (SharePoint SAML-integráció).

2. Kattintson a **beállítások**.

3. A beállítások panelen kattintson a **válasz URL-címek**. 

4. A további webalkalmazás URL-Címének hozzáadása `/_trust/default.aspx` hozzáfűzi az URL-címet (például `https://sales.contoso.local/_trust/default.aspx`), és kattintson a **mentése**.

5. A SharePoint-kiszolgálóra, nyissa meg a **SharePoint 2016 felügyeleti rendszerhéj** , és hajtsa végre a következő parancsokat, a korábban használt jogkivonat megbízható kibocsátó neve.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
6. A központi felügyelet nyissa meg a webalkalmazást, és engedélyezze a meglévő megbízható identitásszolgáltatóra. Ne felejtse el a bejelentkezési oldal URL-címet is konfigurálhat egy egyéni bejelentkezési oldalon `/_trust/`.

7. A központi felügyelet a webes alkalmazásban kattintson, és válassza **felhasználói házirend**. Adja hozzá a megfelelő engedélyekkel rendelkező felhasználó, ahogyan korábban ebben a cikkben is.

### <a name="fixing-people-picker"></a>Személy kijelölése kijavítása

Felhasználók, már bejelentkezhetnek a SharePoint 2016 identitásokat az Azure ad-ből, de továbbra is vannak, a felhasználói élmény fokozása lehetőségeit. Például egy felhasználó keresése jeleníti több keresési eredmények a személy kijelölése. Nincs minden jogcím leképezése létrehozott 3 jogcímtípusok egy keresési eredményben. Válassza ki a felhasználó a személyválasztója segítségével, kell pontosan írja be a felhasználónevet, és válassza ki a **neve** jogcím-eredményt.

![Jogcímek keresési eredmények](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Nem történik ellenőrzés az értékek, keresse meg, ami elírások vagy véletlenül a nem a megfelelő választás felhasználók jogcím típusa például hozzárendelése a **Vezetéknév** jogcím. Ez megakadályozhatja, hogy felhasználók sikeresen elérik az erőforrásokat.

Ebben a forgatókönyvben nyújtanak, van egy nyílt forráskódú megoldást [AzureCP](https://yvand.github.io/AzureCP/) , amely egy egyéni jogcímszolgáltatótól biztosít a SharePoint 2016. Mi felhasználók adja meg, és hajtsa végre fogja használni az Azure AD Graph érvényesítése. További információ: [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon ehhez biztosítson hozzáférést a helyszíni SharePoint Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **helyszíni SharePoint-**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **helyszíni SharePoint-**.

    ![A helyszíni SharePoint-hivatkozás alkalmazásainak listájában](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-sharepoint-on-premises-test-user"></a>A SharePoint helyszíni tesztfelhasználó létrehozása

Ebben a szakaszban egy helyszíni SharePoint Britta Simon nevű felhasználó létrehozásához. Együttműködve [helyszíni SharePoint-támogatási csoportjának](https://support.office.com/) a felhasználók hozzáadása a SharePoint helyszíni platform. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen, a SharePoint helyszíni csempére kattint, meg kell lehet automatikusan bejelentkezett a helyszíni SharePoint-, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
