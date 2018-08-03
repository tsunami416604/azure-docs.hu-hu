---
title: 'Oktatóanyag: Azure Active Directory-integráció a helyszíni SharePoint |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a helyszíni SharePoint-között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/12/2018
ms.author: jeedes
ms.openlocfilehash: f30b2356b9d3d8ecf7afcdd8ad039a1f02c47550
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438239"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Oktatóanyag: Azure Active Directory-integráció a helyszíni SharePoint

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a helyszíni SharePoint az Azure Active Directory (Azure AD).

Helyszíni SharePoint-integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a helyszíni SharePoint az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a SharePoint helyszíni (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a helyszíni SharePoint, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy helyszíni SharePoint-egyszeri bejelentkezés engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Helyszíni SharePoint-Hozzáadás a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>Helyszíni SharePoint-Hozzáadás a katalógusból
Az Azure AD-be a helyszíni SharePoint-integráció konfigurálásához, hozzá kell a helyszíni SharePoint a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Helyszíni SharePoint-hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **helyszíni SharePoint-**, jelölje be **helyszíni SharePoint-** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![A helyi Sharepointot a találatok listájában](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés a helyszíni SharePoint "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a helyszíni SharePoint-partner felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Sharepointban hivatkozás kapcsolata a helyszíni kell létrehozni.

Az Azure AD egyszeri bejelentkezés a helyszíni SharePoint tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[A hozzáférési jogot a SharePoint helyszíni tesztfelhasználó](#grant-access-to-sharePoint-on-premises-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon a helyszíni SharePoint-, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a SharePoint egy helyszíni alkalmazásba az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása a helyszíni SharePoint, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **helyszíni SharePoint-** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_samlbase.png)

1. Az a **helyszíni SharePoint-tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![A SharePoint a helyszíni tartomány és URL-címeket egyetlen bejelentkezési adatait](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_url1.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. Az a **azonosító** szövegmezőbe írja be az URL-cím: `urn:sharepoint:federation`

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_certificate.png)

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media\sharepoint-on-premises-tutorial/tutorial_general_400.png)

1. Az a **helyszíni SharePoint-konfiguráció** területén kattintson **SharePoint konfigurálása a helyszíni** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![A SharePoint helyszíni konfigurálása](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_configure.png)

    > [!NOTE]
    > Helyszíni SharePoint-alkalmazás a SAML 1.1-es használja a SAML 1.1-es általa token, így az Azure ad-ben a WS-Fed kérelmet a SharePoint-kiszolgáló és a hitelesítés után vár. token.

1. Egy másik böngészőablakban jelentkezzen be a SharePoint helyszíni vállalati webhely rendszergazdaként.

1. **A SharePoint Server 2016 egy új megbízható identitásszolgáltatóra konfigurálása**

    Jelentkezzen be a SharePoint Server 2016-kiszolgálón, és nyissa meg a SharePoint 2016 felügyeleti rendszerhéjat. Adja meg az értékeket $realm, $wsfedurl és az Azure Portalról $filepath, és futtassa a következő parancsokat egy új megbízható identitásszolgáltatóra konfigurálásához.

    > [!TIP]
    > Ha használja a PowerShell-lel vagy a PowerShell működésével kapcsolatos további tudnivalókat a kívánt [SharePoint PowerShell](https://docs.microsoft.com/en-us/powershell/sharepoint/overview?view=sharepoint-ps). 

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have copied from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $map4 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress" -IncomingClaimTypeDisplayName "Email" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Ezután kövesse az alábbi lépéseket az alkalmazás megbízható identitásszolgáltatóra engedélyezése:

    a. Lépjen a központi felügyelet **webalkalmazás kezelése** , és válassza ki a webalkalmazást, amely meg szeretne védeni az Azure ad-ben.

    b. A menüszalagon kattintson **hitelesítésszolgáltatók** , és válassza a zónában, amelyet használni kíván.

    c. Válassza ki **megbízható identitásszolgáltató** , és válassza ki az identitás-szolgáltató csak regisztrált nevű *AzureAD*.

    d. Válassza ki a bejelentkezési oldal URL-cím beállítás **egyéni bejelentkezési oldalon** , és a "/_trust/" értéket.

    e. Kattintson az **OK** gombra.

    ![A hitelesítésszolgáltató konfigurálása](./media\sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media\sharepoint-on-premises-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media\sharepoint-on-premises-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media\sharepoint-on-premises-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media\sharepoint-on-premises-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="grant-access-to-sharepoint-on-premises-test-user"></a>Hozzáférés engedélyezése a SharePoint helyszíni tesztfelhasználó számára

A felhasználók, akik jelentkezzen be az Azure AD és a SharePoint eléréséhez fog hozzáférést kell adni az alkalmazáshoz az alábbi lépések segítségével állítsa be az engedélyeket a webalkalmazáshoz való hozzáférés.

1. Kattintson a központi felügyelet **Alkalmazáskezelés**.

1. Az a **Alkalmazáskezelés** lap a **webalkalmazások** területén kattintson **webalkalmazások kezelése**.

1. Kattintson a megfelelő webalkalmazás, majd a **felhasználói házirend**.

1. Kattintson a webalkalmazás-házirend **felhasználó hozzáadása**.

    ![A jogcím nevét a felhasználó keresése](./media\sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

1. Az a **felhasználó hozzáadása** párbeszédpanelen kattintson a megfelelő zónához az **zónák**, és kattintson a **tovább**.

1. A a **házirend-webalkalmazás** párbeszédpanel a **felhasználók kiválasztása** területén kattintson a **Tallózás** ikonra.

1. Az a **található** szövegmezőbe írja be a **felhasználó egyszerű felhasználóneve** érték, amelyhez az Azure AD-ban beállított a SharePoint helyszíni alkalmazás, és kattintson a **keresési**. </br>Példa: *brittasimon@contoso.com*.

1. Az Azure ad a listanézet cím alatt válassza ki a name tulajdonság, majd kattintson **Hozzáadás** kattintson **OK** a párbeszédpanel bezárásához.

1. Az engedélyek párbeszédpanelen kattintson a **teljes hozzáférés**.

    ![Teljes hozzáférés biztosítása a felhasználói igények](./media\sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

1. Kattintson a **Befejezés**, és kattintson a **OK**.

### <a name="configuring-one-trusted-identity-provider-for-multiple-web-applications"></a>Egy megbízható identitásszolgáltatóra több webalkalmazás konfigurálása

A konfiguráció egyetlen webalkalmazás működik, de további konfigurálást igényel, ha több webalkalmazás ugyanazon a megbízható identitásszolgáltatóra használni szeretne. Tegyük fel például, egy webalkalmazás, az URL-cím használatára volt kiterjesztettük `https://portal.contoso.local` , és most szeretné hitelesíteni a felhasználókat `https://sales.contoso.local` is. Ehhez az identitásszolgáltató tartsa tiszteletben a WReply paramétert, és frissítse az alkalmazás regisztrálása az Azure ad-ben a válasz URL-cím hozzáadása frissíteni kell.

1. Az Azure Portalon nyissa meg az Azure AD-címtárban. Kattintson a **alkalmazásregisztrációk**, majd kattintson a **minden alkalmazás megtekintése**. Kattintson a korábban létrehozott alkalmazásra (SharePoint SAML-integráció).

1. Kattintson a **beállítások**.

1. A beállítások panelen kattintson a **válasz URL-címek**. 

1. A további webalkalmazás URL-Címének hozzáadása `/_trust/default.aspx` hozzáfűzi az URL-címet (például `https://sales.contoso.local/_trust/default.aspx`), és kattintson a **mentése**.

1. A SharePoint-kiszolgálóra, nyissa meg a **SharePoint 2016 felügyeleti rendszerhéj** , és hajtsa végre a következő parancsokat, a korábban használt jogkivonat megbízható kibocsátó neve.

    ```
    $t = Get-SPTrustedIdentityTokenIssuer "AzureAD"
    $t.UseWReplyParameter=$true
    $t.Update()
    ```
1. A központi felügyelet nyissa meg a webalkalmazást, és engedélyezze a meglévő megbízható identitásszolgáltatóra. Ne felejtse el a bejelentkezési oldal URL-címet is konfigurálhat egy egyéni bejelentkezési oldalon `/_trust/`.

1. A központi felügyelet a webes alkalmazásban kattintson, és válassza **felhasználói házirend**. Adja hozzá a megfelelő engedélyekkel rendelkező felhasználó, ahogyan korábban ebben a cikkben is.

### <a name="fixing-people-picker"></a>Személy kijelölése kijavítása

Felhasználók, már bejelentkezhetnek a SharePoint 2016 identitásokat az Azure ad-ből, de továbbra is vannak, a felhasználói élmény fokozása lehetőségeit. Például egy felhasználó keresése jeleníti több keresési eredmények a személy kijelölése. Nincs minden jogcím leképezése létrehozott 3 jogcímtípusok egy keresési eredményben. Válassza ki a felhasználó a személyválasztója segítségével, kell pontosan írja be a felhasználónevet, és válassza ki a **neve** jogcím-eredményt.

![Jogcímek keresési eredmények](./media\sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Nem történik ellenőrzés az értékek, keresse meg, ami elírások vagy véletlenül a nem a megfelelő választás felhasználók jogcím típusa például hozzárendelése a **Vezetéknév** jogcím. Ez megakadályozhatja, hogy felhasználók sikeresen elérik az erőforrásokat.

Ebben a forgatókönyvben nyújtanak, van egy nyílt forráskódú megoldást [AzureCP](https://yvand.github.io/AzureCP/) , amely egy egyéni jogcímszolgáltatótól biztosít a SharePoint 2016. Mi felhasználók adja meg, és hajtsa végre fogja használni az Azure AD Graph érvényesítése. További információ: [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon ehhez biztosítson hozzáférést a helyszíni SharePoint Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon rendel a helyi Sharepointot, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

1. Az alkalmazások listájában jelölje ki a **helyszíni SharePoint-**.

    ![Az alkalmazások listáját a SharePoint-hivatkozás](./media\sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_app.png)

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen, a SharePoint helyszíni csempére kattint, szerezheti be automatikusan bejelentkezett a SharePoint helyszíni alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [A SharePoint Server-hitelesítés az Azure AD-vel](https://docs.microsoft.com/en-us/office365/enterprise/using-azure-ad-for-sharepoint-server-authentication)

<!--Image references-->

[1]: ./media\sharepoint-on-premises-tutorial/tutorial_general_01.png
[2]: ./media\sharepoint-on-premises-tutorial/tutorial_general_02.png
[3]: ./media\sharepoint-on-premises-tutorial/tutorial_general_03.png
[4]: ./media\sharepoint-on-premises-tutorial/tutorial_general_04.png

[100]: ./media\sharepoint-on-premises-tutorial/tutorial_general_100.png

[200]: ./media\sharepoint-on-premises-tutorial/tutorial_general_200.png
[201]: ./media\sharepoint-on-premises-tutorial/tutorial_general_201.png
[202]: ./media\sharepoint-on-premises-tutorial/tutorial_general_202.png
[203]: ./media\sharepoint-on-premises-tutorial/tutorial_general_203.png

