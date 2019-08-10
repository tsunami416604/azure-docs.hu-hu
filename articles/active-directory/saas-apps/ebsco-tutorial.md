---
title: 'Oktatóanyag: Azure Active Directory integráció a EBSCO-szel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és EBSCO között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 144f7f65-69e9-4016-a151-fe1104fd6ba8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/01/2019
ms.author: jeedes
ms.openlocfilehash: 35cb408473da8c6397c5034ae20ac0a50b0953ea
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68944722"
---
# <a name="tutorial-azure-active-directory-integration-with-ebsco"></a>Oktatóanyag: Azure Active Directory integráció a EBSCO

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a EBSCO a Azure Active Directory (Azure AD) szolgáltatással.
A EBSCO és az Azure AD integrálásával a következő előnyöket nyújtja:

* Az Azure AD-ben beállíthatja, hogy ki férhet hozzá a EBSCO.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek a EBSCO (egyszeri bejelentkezés) az Azure AD-fiókokkal.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse [meg a mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció EBSCO való konfigurálásához a következő elemek szükségesek:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [ingyenes fiókot](https://azure.microsoft.com/free/) szerezhet be
* EBSCO egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* A EBSCO támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO

* A EBSCO **csak időben támogatja a** felhasználók kiépítési folyamatát

## <a name="adding-ebsco-from-the-gallery"></a>EBSCO hozzáadása a gyűjteményből

A EBSCO Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a EBSCO a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha EBSCO szeretne hozzáadni a katalógusból, hajtsa végre a következő lépéseket:**

1. A **[Azure Portal](https://portal.azure.com)** a bal oldali navigációs panelen kattintson a **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson a párbeszédpanel tetején található **új alkalmazás** gombra.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **EBSCO**kifejezést, válassza az **EBSCO** elemet az eredmény panelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![EBSCO az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezést az EBSCO-mel konfigurálja és teszteli a **Britta Simon**nevű tesztelési felhasználó alapján.
Az egyszeri bejelentkezés működéséhez az Azure AD-felhasználó és a EBSCO kapcsolódó felhasználó közötti kapcsolat létesítésére van szükség.

Az Azure AD egyszeri bejelentkezés EBSCO való konfigurálásához és teszteléséhez a következő építőelemeket kell végrehajtania:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[EBSCO egyszeri bejelentkezés konfigurálása](#configure-ebsco-single-sign-on)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre EBSCO-teszt felhasználót](#create-ebsco-test-user)** – hogy a EBSCO Britta, a felhasználó Azure ad-képviseletéhez kapcsolódó partnerrel rendelkezzen.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezheti az Azure AD egyszeri bejelentkezést a Azure Portal.

Az Azure AD egyszeri bejelentkezés EBSCO való konfigurálásához hajtsa végre a következő lépéseket:

1. A [Azure Portal](https://portal.azure.com/) **EBSCO** alkalmazás-integráció lapján válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az egyszeri bejelentkezés **módszerének kiválasztása** párbeszédpanelen válassza az **SAML/ws-fed** üzemmód lehetőséget az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson a **Szerkesztés** ikonra az alapszintű SAML- **konfiguráció** párbeszédpanel megnyitásához.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az alapszintű **SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépést:

    ![EBSCO tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-identifier.png)

    Az **azonosító** szövegmezőbe írja be az URL-címet:`pingsso.ebscohost.com`

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    ![image](common/both-preintegrated-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://search.ebscohost.com/login.aspx?authtype=sso&custid=<unique EBSCO customer ID>&profile=<profile ID>`

    > [!NOTE]
    > A bejelentkezési URL-cím értéke nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az értékek lekéréséhez forduljon a [EBSCO](mailto:sso@ebsco.com) ügyfélszolgálati csapatához. Az Azure Portal alapszintű **SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

    o **egyedi elemek:**  

    o **CustId** = egyedi EBSCO-ügyfél azonosítójának megadása 

    o **profil** = az ügyfelek testre szabhatók a hivatkozás, hogy egy adott profilhoz irányítsák a felhasználókat (attól függően, hogy mit vásárolnak a EBSCO). Megadhatnak egy adott profil-azonosítót. A fő azonosítók az EDS (EBSCO Discovery Service) és a ehost (EBSOCOhost-adatbázisok). Az itt megjelenő utasításokat [itt](https://help.ebsco.com/interfaces/EBSCOhost/EBSCOhost_FAQs/How_do_I_set_up_direct_links_to_EBSCOhost_profiles_and_or_databases#profile)találja.

6. A EBSCO alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a **felhasználói attribútumok** párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

     > [!Note]
    > A **Name** attribútum kötelező, és a EBSCO alkalmazásban a **Name Identifier érték** van leképezve. Ez alapértelmezés szerint hozzá van adva, így nem kell manuálisan hozzáadnia.

7. A fentieken kívül a EBSCO alkalmazás néhány további attribútumot vár, amelyeket az SAML-válaszban vissza kell adni. A **felhasználó attribútumai** párbeszédpanel **felhasználói** jogcímek szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon: 

    | Name (Név) | Forrás attribútum|
    | ---------------| --------------- |    
    | FirstName   | User. givenName |
    | LastName   | felhasználó. vezetéknév |
    | Email   | user.mail |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson a **Save** (Mentés) gombra.

8. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra az **összevonási metaadatok XML-** fájljának a megadott beállítások alapján történő letöltéséhez, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

9. A **EBSCO beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-ebsco-single-sign-on"></a>EBSCO egyszeri bejelentkezés konfigurálása

Ha az egyszeri bejelentkezést szeretné konfigurálni a **EBSCO** oldalon, el kell küldenie a letöltött **metaadatok XML** -fájlját és a megfelelő másolt url-címeket a Azure Portalról a [EBSCO támogatási csapatához](mailto:sso@ebsco.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. A név mezőbe írja be a **BrittaSimon** **nevet** .
  
    b. A **Felhasználónév** mezőbe írja be brittasimon@yourcompanydomain.extensiona nevet. Például: BrittaSimon@contoso.com

    c. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a jelszó mezőben megjelenő értéket.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést a EBSCO hozzáférésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, válassza a **minden alkalmazás**lehetőséget, majd válassza a **EBSCO**lehetőséget.

    ![Vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában válassza a **EBSCO**lehetőséget.

    ![Az EBSCO hivatkozás az alkalmazások listájában](common/all-applications.png)

3. A bal oldali menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza a **felhasználók és csoportok** lehetőséget a **hozzárendelés hozzáadása** párbeszédpanelen.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha az SAML-kijelentésben az egyik szerepkör értékét várja, akkor a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.

7. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-ebsco-test-user"></a>EBSCO-tesztelési felhasználó létrehozása

A EBSCO esetében a felhasználók kiosztása automatikus.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

Az Azure AD továbbítja a szükséges EBSCO alkalmazást. A EBSCO felhasználó-kiépítés lehet automatikus, vagy egy egyszeri űrlapra van szükség. Attól függ, hogy az ügyfél rendelkezik-e sok olyan meglévő EBSCOhost-fiókkal, amelyeken személyes beállítások lettek mentve. Ugyanezt a [EBSCO támogatási csapatával](mailto:sso@ebsco.com) is megvitathatja a megvalósítás során. Mindkét esetben az ügyfélnek nem kell EBSCOhost-fiókokat létrehoznia a tesztelés előtt.

   >[!Note]
   >Automatizálhatja a EBSCOhost-felhasználók üzembe helyezését vagy személyre szabását. Az igény szerinti felhasználói üzembe helyezéssel kapcsolatban forduljon a [EBSCO ügyfélszolgálatához](mailto:sso@ebsco.com) . 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

1. Ha a hozzáférési panelen a EBSCO csempére kattint, a rendszer automatikusan bejelentkezett a EBSCO alkalmazásba.
További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/active-directory-saas-access-panel-introduction.md).

2. Miután bejelentkezett az alkalmazásba, kattintson a jobb felső sarokban található **Bejelentkezés** gombra.

    ![Az EBSCO-bejelentkezés az alkalmazások listájában](./media/ebsco-tutorial/tutorial_ebsco_signin.png)
 
3. A rendszer egyszeri kérést kap az intézményi/SAML-bejelentkezés összepárosításához, amely egy **meglévő MyEBSCOhost-fiókjának az intézmény-fiókjához való csatolását** veszi igénybe, vagy **létrehoz egy új MyEBSCOhost-fiókot, és összekapcsolja azt az intézmény fiókjával**. A fiók a EBSCOhost alkalmazás személyre szabására szolgál. Válassza az **új fiók létrehozása** lehetőséget, és látni fogja, hogy a személyre szabás űrlapja előre be van töltve az SAML-válaszból származó értékekkel, ahogy az alábbi képernyőképen is látható. A kijelölés mentéséhez kattintson a **"Folytatás"** gombra.
    
     ![A EBSCO felhasználó az alkalmazások listájában](./media/ebsco-tutorial/tutorial_ebsco_user.png)

1. A fenti beállítás befejezése után törölje a cookie-k/gyorsítótár és a bejelentkezés újbóli beírását. Nem kell manuálisan bejelentkeznie, és a rendszer megjegyezi a személyre szabási beállításokat.

## <a name="additional-sesources"></a>További sesources

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

