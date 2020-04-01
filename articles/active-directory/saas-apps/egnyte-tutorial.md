---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Egnyte-vel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Egnyte között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 8c2101d4-1779-4b36-8464-5c1ff780da18
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f8eb0f0d566d656436da11141ea7f6c521b7b82
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76983690"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-egnyte"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az Egnyte-vel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Egnyte-t az Azure Active Directoryval (Azure AD). Ha integrálja az Egnyte-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az Egnyte.Control in Azure AD who has access to Egnyte.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve Egnyte az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Egnyte egyszeri bejelentkezés (SSO) engedélyezve van előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Egnyte támogatja **SP** kezdeményezett SSO
* Az Egnyte konfigurálása után kényszerítheti a Munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-egnyte-from-the-gallery"></a>Egnyte hozzáadása a galériából

Az Egnyte Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az Egnyte-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be **az Egnyte** kifejezést a keresőmezőbe.
1. Válassza **az Egnyte** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az Egnyte-vel egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés a munka, az Azure AD-felhasználó és a kapcsolódó felhasználó egnyte közötti kapcsolat létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az Egnyte-vel a következő építőelemeket kell végrehajtania:

Az Azure AD SSO konfigurálásához és teszteléséhez az Egnyte segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Állítsa be az Egnyte Egyszeri bejelentkezést](#configure-egnyte-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre Egnyte teszt felhasználó](#create-egnyte-test-user)** - egy megfelelője B.Simon az Egnyte, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **Egnyte** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Egnyte Domain és URL-ek egyszeri bejelentkezési információk](common/sp-signonurl.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.egnyte.com`

    > [!NOTE]
    > Az érték nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Lépjen kapcsolatba [az Egnyte ügyféltámogatási csapatával](https://www.egnyte.com/corp/contact_egnyte.html) az érték értéséhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

5. Az **Egnyte beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mezőbe írja be **a\@brittasimon yourcompanydomain.extension típusú felhasználónév mezőt.**  
    Például: BrittaSimon@contoso.com

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés az Egnyte használatával.

1. Az Azure Portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd az **Egnyte**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Egnyte**lehetőséget.

    ![Az Egnyte hivatkozás az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-egnyte-sso"></a>Az Egnyte SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az Egnyte vállalati webhelyére rendszergazdaként.

2. Kattintson a **Beállítások** elemre.
   
    ![Beállítások](./media/egnyte-tutorial/ic787819.png "Beállítások")

3. A menüben kattintson a **Beállítások parancsra.**

    ![Beállítások](./media/egnyte-tutorial/ic787820.png "Beállítások")

4. Kattintson a **Konfiguráció** fülre, majd a **Biztonság**gombra.

    ![Biztonság](./media/egnyte-tutorial/ic787821.png "Biztonság")

5. Az **egyszeri bejelentkezéshitelesítés szakaszban hajtsa** végre az alábbi lépéseket:

    ![Egyszeres bejelentkezés a hitelesítésen](./media/egnyte-tutorial/ic787822.png "Egyszeres bejelentkezés a hitelesítésen")   
    
    a. **Egyszeri bejelentkezési hitelesítésként**válassza az **SAML 2.0**lehetőséget.
   
    b. **Identitásszolgáltatóként**válassza az **AzureAD**lehetőséget.
   
    c. Illessze be az Azure Portalról másolt **bejelentkezési URL-címet** az **Identitásszolgáltató bejelentkezési URL-címmezőjébe.**
   
    d. Illessze be **az Azure AD-azonosítót,** amelyet az Azure Portalról másolt az **Identitásszolgáltató entitásazonosító** szövegmezőjébe.
      
    e. Nyissa meg az alap-64 kódolású tanúsítványt az Azure Portalról letöltött jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be az **Identitásszolgáltató tanúsítványának** szövegmezőjébe.
   
    f. Alapértelmezett **felhasználói leképezésként**válassza **az E-mail cím**lehetőséget.
   
    g. A **Tartományspecifikus kiállítói érték használata mezőben**válassza **a letiltva**lehetőséget.
   
    h. Kattintson a **Mentés** gombra.

### <a name="create-egnyte-test-user"></a>Egnyte tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek az Egnyte-be, ki kell építeni őket az Egnyte-be. Egnyte esetén kiépítése manuális feladat.

**Felhasználói fiókok létesítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be **az Egnyte** vállalati webhelyére rendszergazdaként.

2. Nyissa meg a **Beállítások \> felhasználók & csoportok lehetőséget.**

3. Kattintson **az Új felhasználó hozzáadása gombra,** majd válassza ki a hozzáadni kívánt felhasználó típusát.
   
    ![Felhasználók](./media/egnyte-tutorial/ic787824.png "Felhasználók")

4. Az **Új energiafelhasználó** csoportban hajtsa végre az alábbi lépéseket:
    
    ![Új általános jogú felhasználó](./media/egnyte-tutorial/ic787825.png "Új általános jogú felhasználó")   

    a. Az **E-mail** szöveg mezőbe írja be a felhasználó e-mail címét, mint **a\@Brittasimon contoso.com**.

    b. A **Felhasználónév** mezőbe írja be a felhasználó felhasználónevét, például **Brittasimon**.

    c. Válassza **az Egyszeri bejelentkezés** **hitelesítési típusként lehetőséget.**
   
    d. Kattintson a **Mentés** gombra.
    
    >[!NOTE]
    >Az Azure Active Directory-fiók tulajdonosa kap egy értesítő e-mailt.
    >

>[!NOTE]
>Az Egnyte felhasználói fiók-létrehozási eszközeit vagy API-jait az Egnyte bármely más, Az Azure AD felhasználói fiókok kiépítéséhez használhatja.
>

### <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen az Egnyte csempére kattint, automatikusan be kell jelentkeznie az Egnyte-be, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
