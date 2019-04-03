---
title: 'Oktatóanyag: Az Azure Active Directory-integráció a helyszíni SharePoint |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a helyszíni SharePoint-között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/21/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8ba9f4df36f753a1caf619ad90015fa073a00de3
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58883377"
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
4. **[Az Azure AD biztonsági csoportok létrehozása az Azure Portalon](#create-an-azure-ad-security-group-in-the-azure-portal)**  – új biztonsági csoport engedélyezése az egyszeri bejelentkezés az Azure AD-ben.
5. **[Hozzáférés engedélyezése a SharePoint a helyi biztonsági csoport](#grant-access-to-sharepoint-on-premises-security-group)**  – hozzáférést biztosít az adott csoport az Azure ad-hez.
6. **[Az Azure Portalon az Azure AD biztonsági csoport hozzárendelése](#assign-the-azure-ad-security-group-in-the-azure-portal)**  – az adott csoporthoz hozzárendelni az Azure AD-hitelesítés.
7. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

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
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-, azonosítóját és válasz URL-cím. Kapcsolattartó [helyszíni SharePoint-ügyfél-támogatási csapatának](https://support.office.com/) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **tanúsítvány (Base64)** a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

    > [!Note]
    > Vegye le a fájl elérési útját, amelyre letöltötte a tanúsítványfájl használata később a PowerShell-parancsfájlt a konfigurációhoz szükség szerint.

6. Az a **beállítása a helyszíni SharePoint-** területén másolja megfelelően a követelmény a megfelelő URL-címe. A **egyszeri bejelentkezési szolgáltatás URL-cím**, az érték a következő mintának: `https://login.microsoftonline.com/_my_directory_id_/wsfed`

    > [!Note]
    > _my_directory_id_ a bérlő azonosítója, az Azure Ad-előfizetés.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

    > [!NOTE]
    > Helyszíni SharePoint-alkalmazás a SAML 1.1-es használja a SAML 1.1-es általa token, így az Azure ad-ben a WS-Fed kérelmet a SharePoint-kiszolgáló és a hitelesítés után vár. token.

### <a name="configure-sharepoint-on-premises-single-sign-on"></a>A helyszíni SharePoint egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a SharePoint helyszíni vállalati webhely rendszergazdaként.

2. **A SharePoint Server 2016 egy új megbízható identitásszolgáltatóra konfigurálása**

    Jelentkezzen be a SharePoint Server 2016-kiszolgálón, és nyissa meg a SharePoint 2016 felügyeleti rendszerhéjat. $Wsfedurl (egyszeri bejelentkezési szolgáltatás URL-cím), és $filepath $realm (azonosító szakaszból származó értékre a SharePoint a helyszíni tartomány és URL-címek az Azure Portalon), az értékét adja meg (a fájl elérési útja, amelyhez a tanúsítvány-fájl letöltése) az Azure Portalon, és futtatása a következő parancsokat egy új megbízható identitásszolgáltatóra konfigurálásához.

    > [!TIP]
    > Ha használja a PowerShell-lel vagy a PowerShell működésével kapcsolatos további tudnivalókat a kívánt [SharePoint PowerShell](https://docs.microsoft.com/powershell/sharepoint/overview?view=sharepoint-ps).

    ```powershell
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have downloaded from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $map5 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.microsoft.com/ws/2008/06/identity/claims/role" -IncomingClaimTypeDisplayName "Role" -SameAsIncoming
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
    > A külső felhasználók némelyike nem képes az egyszeri bejelentkezés integráció egyszerű lesz összekeveredett értéket az alábbihoz hasonló módon lesz `MYEMAIL_outlook.com#ext#@TENANT.onmicrosoft.com`. Hamarosan a Microsoft lehetővé teszi az ügyfeleknek alkalmazáskonfigurációról hogyan kezeli az egyszerű felhasználónév a felhasználó típusától függően. Ezt követően a vendégfelhasználók egyszeri Bejelentkezéssel a szervezeti alkalmazottak zökkenőmentesen adatokként képesnek kell lennie.

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

### <a name="create-an-azure-ad-security-group-in-the-azure-portal"></a>Az Azure AD biztonsági csoportok létrehozása az Azure Portalon

1. Kattintson a **az Azure Active Directory > minden csoport**.

    ![Az Azure AD biztonsági csoport létrehozása](./media/sharepoint-on-premises-tutorial/allgroups.png)

2. Kattintson a **új csoport**:

    ![Az Azure AD biztonsági csoport létrehozása](./media/sharepoint-on-premises-tutorial/newgroup.png)

3. Töltse ki **csoporttípust**, **csoportnév**, **csoport leírása**, **tagságtípusának**. Kattintson a nyílra kattintva válassza ki a tagokat, majd keresse meg, vagy kattintson a tag lesz szeretne hozzáadni a csoporthoz a. Kattintson a **kiválasztása** szeretne hozzáadni a kijelölt tagok, majd kattintson a **létrehozás**.

    ![Az Azure AD biztonsági csoport létrehozása](./media/sharepoint-on-premises-tutorial/addingmembers.png)

    > [!NOTE]
    > Annak érdekében, hogy az Azure Active Directory biztonsági csoport hozzárendelése a helyszíni SharePoint, telepítéséhez és konfigurálásához szükség lesz [AzureCP](https://yvand.github.io/AzureCP/) a helyszíni SharePoint-farm vagy a fejlesztés és a egy másik egyéni jogcímek konfigurálása a SharePoint-szolgáltató.  Tekintse meg a további információk szakasza a saját egyéni jogcímek szolgáltató létrehozásához a dokumentum végén található, ha AzureCP nem használja.

### <a name="grant-access-to-sharepoint-on-premises-security-group"></a>Hozzáférés engedélyezése a SharePoint a helyi biztonsági csoport

**Az alkalmazás regisztrációját a biztonsági csoportok és -engedélyek konfigurálása**

1. Az Azure Portalon válassza ki a **Azure Active Directory**, majd **alkalmazásregisztrációk**.

    ![Vállalati alkalmazások panelen](./media/sharepoint-on-premises-tutorial/appregistrations.png)

2. Írja be a keresőmezőbe, és válassza ki **helyszíni SharePoint-**.

    ![A helyi Sharepointot a találatok listájában](./media/sharepoint-on-premises-tutorial/appsearch.png)

3. Kattintson a **Manifest**.

    ![A beállítás manifest](./media/sharepoint-on-premises-tutorial/manifest.png)

4. Módosítsa `groupMembershipClaims`: `NULL`, az `groupMembershipClaims`: `SecurityGroup`. Kattintson a Mentés

    ![Jegyzékfájl szerkesztése](./media/sharepoint-on-premises-tutorial/manifestedit.png)

5. Kattintson a **beállítások**, majd kattintson a **szükséges engedélyek**.

    ![Szükséges engedélyek](./media/sharepoint-on-premises-tutorial/settings.png)

6. Kattintson a **hozzáadása** , majd **API kiválasztása**.

    ![API Acess](./media/sharepoint-on-premises-tutorial/required_permissions.png)

7. Adja hozzá mindkettőt **Windows Azure Active Directory** és **Microsoft Graph API**, de ez csak akkor lehetséges, válassza ki egyenként.

    ![API kiválasztása](./media/sharepoint-on-premises-tutorial/permissions.png)

8. Válassza ki a Windows Azure Active Directory, ellenőrizze a directory-adatok olvasása, majd kattintson a kijelölés. Lépjen vissza, és adja hozzá a Microsoft Graph és is, válassza ki a címtáradatok olvasása.  Kattintson a kiválasztása, és kattintson a kész.

    ![Hozzáférés engedélyezése](./media/sharepoint-on-premises-tutorial/readpermission.png)

9. Most, a szükséges beállítások, jelölje ki a **engedélyek** és majd kattintson az Igen gombra az engedélyek megadása.

    ![Engedélyek megadása](./media/sharepoint-on-premises-tutorial/grantpermission.png)

    > [!NOTE]
    > Ellenőrizze az alapján határozza meg, ha az engedélyeket sikeresen megadta az értesítéseket.  Ha nem, a AzureCP nem fog megfelelően működni, és nem lehet a helyszíni SharePoint konfigurálása az Azure Active Directory biztonsági csoportokat.

10. A AzureCP konfigurálása a helyszíni SharePoint-farm vagy egy alternatív egyéni jogcímek többszolgáltatós megoldás.  Ebben a példában AzureCP használjuk.

    > [!NOTE]
    > Vegye figyelembe, hogy AzureCP nem egy Microsoft-termék vagy a Microsoft technikai támogatási által támogatott. Letöltéséhez, telepítéséhez és AzureCP / a helyszíni SharePoint-farm konfigurálása https://yvand.github.io/AzureCP/ 

11. **A hozzáférési jogot az Azure Active Directory biztonsági csoportot a helyszíni SharePoint** :-a csoportok hozzáférést kell biztosítani az alkalmazásnak a Sharepointban a-permise.  Az alábbi lépések segítségével állítsa be az engedélyeket a webalkalmazáshoz való hozzáférés.

12. A központi felügyelet eszközben kattintson a kezelés, webalkalmazások kezelése, majd válassza ki a webes alkalmazás aktiválása a menüszalagon, és kattintson a felhasználói házirend.

    ![Központi adminisztrációs](./media/sharepoint-on-premises-tutorial/centraladministration.png)

13. A házirend-webalkalmazás kattintson a felhasználók hozzáadása, majd válassza ki a zónához, kattintson a Tovább gombra.  Kattintson a címjegyzékbe.

    ![A házirend-webalkalmazás](./media/sharepoint-on-premises-tutorial/webapp-policy.png)

14. Ezután keresse meg és adja hozzá az Azure Active Directory biztonsági csoportot, és kattintson az OK gombra.

    ![Biztonsági csoport hozzáadása](./media/sharepoint-on-premises-tutorial/securitygroup.png)

15. Válassza ki a megfelelő engedélyeket, majd kattintson a Befejezés gombra.

    ![Biztonsági csoport hozzáadása](./media/sharepoint-on-premises-tutorial/permissions1.png)

16. Tekintse meg a házirend a webalkalmazás és az Azure Active Directory-csoporthoz hozzáadott.  A csoportos jogcímet a felhasználó nevét az Azure Active Directory biztonsági csoport objektumazonosítóját mutatja be.

    ![Biztonsági csoport hozzáadása](./media/sharepoint-on-premises-tutorial/addgroup.png)

17. Tallózással keresse meg a SharePoint-webhelycsoport, és adja hozzá a csoporthoz, is. Kattintson a hely beállításait, majd kattintson a hely engedélyek és engedélyek megadása.  Keresse meg a csoport szerepkör jogcím, rendelje hozzá a jogosultsági szintet, és kattintson a fájlmegosztás.

    ![Biztonsági csoport hozzáadása](./media/sharepoint-on-premises-tutorial/grantpermission1.png)

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Egy megbízható identitásszolgáltatóra több webalkalmazás konfigurálása

A konfiguráció egyetlen webalkalmazás működik, de további konfigurálást igényel, ha több webalkalmazás ugyanazon a megbízható identitásszolgáltatóra használni szeretne. Tegyük fel például, egy webalkalmazás, az URL-cím használatára volt kiterjesztettük `https://portal.contoso.local` , és most szeretné hitelesíteni a felhasználókat `https://sales.contoso.local` is. Ehhez az identitásszolgáltató tartsa tiszteletben a WReply paramétert, és frissítse az alkalmazás regisztrálása az Azure ad-ben a válasz URL-cím hozzáadása frissíteni kell.

1. Az Azure Portalon nyissa meg az Azure AD-címtárban. Kattintson a **alkalmazásregisztrációk**, majd kattintson a **minden alkalmazás megtekintése**. Kattintson a korábban létrehozott alkalmazásra (SharePoint SAML-integráció).

2. Kattintson a **beállítások**.

3. A beállítások panelen kattintson a **válasz URL-címek**.

4. A további webalkalmazás URL-Címének hozzáadása `/_trust/default.aspx` hozzáfűzi az URL-címet (például `https://sales.contoso.local/_trust/default.aspx`), és kattintson a **mentése**.

5. A SharePoint-kiszolgálóra, nyissa meg a **SharePoint 2016 felügyeleti rendszerhéj** , és hajtsa végre a következő parancsokat, a korábban használt jogkivonat megbízható kibocsátó neve.

    ```powershell
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

### <a name="assign-the-azure-ad-security-group-in-the-azure-portal"></a>Az Azure Portalon az Azure AD biztonsági csoport hozzárendelése

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **helyszíni SharePoint-**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **helyszíni SharePoint-**.

    ![A helyszíni SharePoint-hivatkozás alkalmazásainak listájában](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása**.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Keresse meg a kívánt szeretné használni, majd kattintson a csoport a jelölje be a tagok szakaszhoz adja hozzá a biztonsági csoport. Kattintson a **kiválasztása**, majd kattintson a **hozzárendelése**.

    ![Biztonsági csoport keresése](./media/sharepoint-on-premises-tutorial/securitygroup1.png)

    > [!NOTE]
    > Ellenőrizze az értesítéseket a menüsávon értesítést kapjon, hogy a csoport sikeresen hozzá lett rendelve a vállalati alkalmazás az Azure Portalon.

### <a name="create-sharepoint-on-premises-test-user"></a>A SharePoint helyszíni tesztfelhasználó létrehozása

Ebben a szakaszban egy helyszíni SharePoint Britta Simon nevű felhasználó létrehozásához. Együttműködve [helyszíni SharePoint-támogatási csoportjának](https://support.office.com/) a felhasználók hozzáadása a SharePoint helyszíni platform. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen, a SharePoint helyszíni csempére kattint, meg kell lehet automatikusan bejelentkezett a helyszíni SharePoint-, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)