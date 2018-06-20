---
title: 'Oktatóanyag: Azure Active Directory-integráció a helyszíni SharePoint |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a helyszíni SharePoint között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 85b8d4d0-3f6a-4913-b9d3-8cc327d8280d
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/25/2018
ms.author: jeedes
ms.openlocfilehash: 8cae1e2670ba449c0db28209c54b740e927bbb73
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36210377"
---
# <a name="tutorial-azure-active-directory-integration-with-sharepoint-on-premises"></a>Oktatóanyag: Azure Active Directory-integráció a helyszíni SharePoint

Ebben az oktatóanyagban elsajátíthatja a helyi integrálása az Azure Active Directory (Azure AD).

A helyi integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér a helyi Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett SharePoint a helyi (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a helyszíni SharePoint, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A SharePoint a helyi bejelentkezés engedélyezett előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A helyi hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-sharepoint-on-premises-from-the-gallery"></a>A helyi hozzáadása a gyűjteményből
Az Azure AD integrálása a helyszíni SharePoint konfigurálásához kell hozzáadnia a helyi a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a helyi a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **helyszíni SharePoint**, jelölje be **helyszíni SharePoint** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![A helyi az eredménylistában](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezést a helyi "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD számára a helyszíni SharePoint megfelelőjére felhasználó egy felhasználó számára az Azure AD kell. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a SharePoint közötti kapcsolat kapcsolatot helyszíni kell létrehozni.

Az Azure AD egyszeri bejelentkezést a helyszíni SharePoint tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[A helyszíni SharePoint tesztfelhasználó létrehozása](#create-a-sharePoint-on-premises-test-user)**  - kell rendelkeznie a megfelelője a Britta Simon helyszíni SharePoint, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a SharePoint a helyszíni alkalmazásban egyszeri bejelentkezés konfigurálása.

**A helyszíni SharePoint konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **helyszíni SharePoint** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés párbeszédpanel](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_samlbase.png)

3. Az a **a helyi tartomány és az URL-címek** területen tegye a következőket:

    ![A helyi tartomány és az URL-címek egyetlen bejelentkezés információk](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_url1.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<YourSharePointServerURL>/_trust/default.aspx`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `urn:sharepoint:<YourSharePointServerURL>`

    > [!NOTE]
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [SharePoint helyszíni ügyfél-támogatási csoport](https://support.office.com/) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítványa** kattintson **metaadatainak XML-kódja** , és mentse a .cer kiterjesztésű fájlt a számítógépen a metaadat. Másolja és illessze be a letöltött metaadatait tartalmazó fájl elérési útját a Jegyzettömbbe.

    ![A tanúsítvány letöltési hivatkozását](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_certificate.png)

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/sharepoint-on-premises-tutorial/tutorial_general_400.png)

6. A a **a helyi konfigurációs** kattintson **SharePoint konfigurálása a helyszíni** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![A SharePoint a helyi konfigurációs](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_configure.png)

    > [!NOTE]
    > A helyi alkalmazás SAML 1.1 használja a SAML 1.1 általa token, ezért az Azure AD vár WS Fed kérelmet a SharePoint-kiszolgáló és a hitelesítés után. token.

7. Egy másik webes böngészőablakban jelentkezzen be a SharePoint a helyi vállalati webhely rendszergazdaként.

8. **A SharePoint Server 2016 egy új megbízható identitásszolgáltató konfigurálása**

    Jelentkezzen be a SharePoint Server 2016-kiszolgáló, és nyissa meg a SharePoint 2016 felügyeleti rendszerhéjat. Töltse ki a $realm, $wsfedurl és $filepath Azure-portálon, és futtassa a következő parancsokat egy új megbízható identitásszolgáltató konfigurálása.

    > [!TIP]
    > Ha ismerkedik a PowerShell használatával, vagy PowerShell működésével kapcsolatos tudnivalókért lásd: [SharePoint PowerShell](https://docs.microsoft.com/en-us/powershell/sharepoint/overview?view=sharepoint-ps). 

    ```
    $realm = "<Identifier value from the SharePoint on-premises Domain and URLs section in the Azure portal>"
    $wsfedurl="<SAML single sign-on service URL value which you have copied from the Azure portal>"
    $filepath="<Full path to SAML signing certificate file which you have copied from the Azure portal>"
    $cert = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2($filepath)
    New-SPTrustedRootAuthority -Name "AzureAD" -Certificate $cert
    $map = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name" -IncomingClaimTypeDisplayName "name" -LocalClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"
    $map2 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname" -IncomingClaimTypeDisplayName "GivenName" -SameAsIncoming
    $map3 = New-SPClaimTypeMapping -IncomingClaimType "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname" -IncomingClaimTypeDisplayName "SurName" -SameAsIncoming
    $ap = New-SPTrustedIdentityTokenIssuer -Name "AzureAD" -Description "SharePoint secured by Azure AD" -realm $realm -ImportTrustCertificate $cert -ClaimsMappings $map,$map2,$map3 -SignInUrl $wsfedurl -IdentifierClaim "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"
    ```

    Ezután kövesse az alábbi lépéseket ahhoz, hogy az alkalmazás megbízható identitásszolgáltató:

    a. A központi felügyelet navigáljon **webalkalmazás kezelése** válassza ki a webalkalmazás az Azure ad-vel biztonságos kívánt.

    b. Kattintson a menüszalagon található **hitelesítésszolgáltatókat** , és válassza a zónában, amelyet használni kíván.

    c. Válassza ki **megbízható identitásszolgáltató** és a nevű csak regisztrált azonosítása szolgáltató *AzureAD*.

    d. A bejelentkezési oldal URL-cím beállításnál válassza ki a **egyéni bejelentkezési oldal** , és adja meg a "/_trust/" értéket.

    e. Kattintson az **OK** gombra.

    ![A hitelesítésszolgáltató konfigurálása](./media/sharepoint-on-premises-tutorial/fig10-configauthprovider.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/sharepoint-on-premises-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/sharepoint-on-premises-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/sharepoint-on-premises-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/sharepoint-on-premises-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-sharepoint-on-premises-test-user"></a>A helyszíni SharePoint tesztfelhasználó létrehozása

1. Kattintson a központi felügyelet **Alkalmazáskezelés**.

2. A a **Alkalmazáskezelés** lap a **webalkalmazások** kattintson **webes alkalmazásokat kezeléséhez**.

3. Kattintson a megfelelő webalkalmazás, majd a **felhasználói házirend**.

4. Kattintson a webalkalmazás-házirend **felhasználó hozzáadása**.

    ![A felhasználó által a név jogcímet keresése](./media/sharepoint-on-premises-tutorial/fig11-searchbynameclaim.png)

5. A a **felhasználó hozzáadása** párbeszédpanelen kattintson a megfelelő zónához **zónák**, és kattintson a **következő**.

6. Az a **webes alkalmazás-házirend** párbeszédpanel a **felhasználók kiválasztása** területen kattintson a **Tallózás** ikonra.

7. Az a **található** szövegmező, írja be a felhasználó bejelentkezési nevét a könyvtárban, és kattintson a **keresési**. </br>Példa: *demouser@blueskyabove.onmicrosoft.com*.

8. A listanézetben AzureAD fejléc alatt jelölje ki a name tulajdonság, majd kattintson **Hozzáadás** kattintson **OK** bezárhatja a párbeszédpanelt.

9. Az engedélyek párbeszédpanelen kattintson **teljes hozzáférés**.

    ![Teljes hozzáférés biztosítása a felhasználói igények](./media/sharepoint-on-premises-tutorial/fig12-grantfullcontrol.png)

10. Kattintson a **Befejezés**, és kattintson a **OK**.

### <a name="fixing-people-picker"></a>Kijavítása személyek kiválasztása

Felhasználók most már be tud jelentkezni a SharePoint 2016 identitásokat az Azure AD, de még vannak a felhasználói élmény fokozása lehetőségeit. Például egy felhasználó keresése közli több keresési eredmények a személyek kiválasztása. Nincs a keresési eredmény az egyes 3 jogcímtípusok, amelyek a jogcím leképezésben lettek létrehozva. Válassza ki azt a felhasználót, a személyek objektumválasztó használata, kell pontosan írja be a felhasználónevet, és válassza ki a **neve** jogcím eredménye.

![A keresési eredményekben jogcímek](./media/sharepoint-on-premises-tutorial/fig16-claimssearchresults.png)

Nem lehet érvényesíteni a megadott értékek keres, ami talál helyesírási hibát vagy a felhasználók véletlenül a nem a megfelelő választás jogcím típusa például hozzárendelni a **vezetékneve** jogcímek. Ez azt előfordulhat, hogy a felhasználók sikeresen hozzáférését az erőforrásokhoz.

Ez a forgatókönyv segít, van egy nyílt forráskódú megoldást kínál [AzureCP](https://yvand.github.io/AzureCP/) biztosít egy egyéni jogcím-szolgáltató a SharePoint 2016 számára. Mi felhasználók adja meg, és hajtsa végre fogja használni az Azure AD Graph érvényesítése. További tudnivalókért olvassa el [AzureCP](https://yvand.github.io/AzureCP/).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon szerint a helyi való hozzáférés biztosítása Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**A helyi Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listában válassza ki a **helyszíni SharePoint**.

    ![Az alkalmazások listáját a SharePoint-hivatkozás](./media/sharepoint-on-premises-tutorial/tutorial_sharepointonpremises_app.png)

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a SharePoint a helyi csempe a hozzáférési panelen gombra kattint, szerezheti be automatikusan bejelentkezett a SharePoint a helyi alkalmazásba.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sharepoint-on-premises-tutorial/tutorial_general_01.png
[2]: ./media/sharepoint-on-premises-tutorial/tutorial_general_02.png
[3]: ./media/sharepoint-on-premises-tutorial/tutorial_general_03.png
[4]: ./media/sharepoint-on-premises-tutorial/tutorial_general_04.png

[100]: ./media/sharepoint-on-premises-tutorial/tutorial_general_100.png

[200]: ./media/sharepoint-on-premises-tutorial/tutorial_general_200.png
[201]: ./media/sharepoint-on-premises-tutorial/tutorial_general_201.png
[202]: ./media/sharepoint-on-premises-tutorial/tutorial_general_202.png
[203]: ./media/sharepoint-on-premises-tutorial/tutorial_general_203.png

