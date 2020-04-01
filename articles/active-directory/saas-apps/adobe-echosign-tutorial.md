---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Adobe Sign alkalmazással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Adobe Sign között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: f9385723-8fe7-4340-8afb-1508dac3e92b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/19/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 788c1292b844088f171479b40fc566ff5cfc8a57
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73154023"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-sign"></a>Oktatóanyag: Az Azure Active Directory integrációja az Adobe Sign alkalmazással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Adobe Signt az Azure Active Directoryval (Azure AD).
Az Adobe Sign integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az Adobe Signhoz.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az Adobe Sign (Single Sign-On) alkalmazásba az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció és az Adobe Sign konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Az Adobe Sign egyszeri bejelentkezéssel rendelkező előfizetése

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az Adobe Sign támogatja az **SP** által kezdeményezett sso-t

## <a name="adding-adobe-sign-from-the-gallery"></a>Adobe Sign hozzáadása a galériából

Az Adobe Sign Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az Adobe Signt a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha az Adobe Signt a galériából szeretné felvenni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **Adobe Sign**( Adobe Sign ) parancsot, válassza az Adobe **Sign** elemet az eredménypanelről, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Az Adobe Sign az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD egyszeri bejelentkezését az Adobe Sign segítségével egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés, hogy működjön, létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó az Adobe Sign ban létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az Adobe Sign alkalmazással a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az Adobe Sign Single Sign-On](#configure-adobe-sign-single-sign-on)** --hoz configure az egyszeri bejelentkezés beállításait az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Adobe Sign tesztfelhasználót](#create-adobe-sign-test-user)** – ha az Adobe Signban britta Simon megfelelője szeretne lenni, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének az Adobe Sign segítségével való konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **Adobe Sign** alkalmazásintegrációs lapon válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![Az Adobe Sign Domain és az URL-címek egyszeri bejelentkezési adatai](common/sp-identifier.png)

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<companyname>.echosign.com/`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<companyname>.echosign.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Ezeknek az értékeknek a beszereznie az [Adobe Sign ügyféltámogatási csapatát.](https://helpx.adobe.com/in/contact/support.html) Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. Az **Adobe Sign beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelménynek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-adobe-sign-single-sign-on"></a>Az Adobe Sign single sign-on konfigurálása

1. A konfiguráció előtt lépjen kapcsolatba az [Adobe Sign ügyfél támogatási csapatával,](https://helpx.adobe.com/in/contact/support.html) és vegye fel a tartományt az Adobe Sign engedélyezési listájára. A következőképpen veheti fel a tartományt:

    a. Az [Adobe Sign ügyféltámogatási csapata](https://helpx.adobe.com/in/contact/support.html) véletlenszerűen generált jogkivonatot küld Önnek. A domain, a token lesz, mint a következő: **adobe-sign-verification= xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx**

    b. Tegye közzé az ellenőrző tokent egy DNS-szövegrekordban, és értesítse az [Adobe Sign ügyféltámogatási csapatát](https://helpx.adobe.com/in/contact/support.html).
    
    > [!NOTE]
    > Ez eltarthat néhány napig, vagy tovább. Ne feledje, hogy a DNS propagálási késleltetése azt jelenti, hogy a DNS-ben közzétett érték egy óráig vagy tovább nem látható. A rendszergazdának ismernie kell, hogyan teheti közzé ezt a jogkivonatot egy DNS-szövegrekordban.
    
    c. Amikor a támogatási jegyen keresztül értesíti az [Adobe Sign Ügyfél támogatási csapatát,](https://helpx.adobe.com/in/contact/support.html) a jogkivonat közzététele után ellenőrzik a tartományt, és hozzáadják a fiókjához.
    
    d. Általában a következőképpen teheti közzé a jogkivonatot egy DNS-rekordon:

    * Bejelentkezés a tartományi fiókba
    * Keresse meg a DNS-rekord frissítéséhez szolgáló lapot. Ezt a lapot dns-kezelésnek, névkiszolgáló-kezelésnek vagy speciális beállításoknak lehet nevezni.
    * Keresse meg a tartomány TXT rekordjait.
    * Adjon hozzá egy TXT rekordot az Adobe által biztosított teljes jogkivonat-értékkel.
    * Mentse a módosításokat.

1. Egy másik böngészőablakban jelentkezzen be az Adobe Sign vállalati webhelyére rendszergazdaként.

1. Az SAML menüben válassza a **Fiókbeállítások** > **SAML-beállítások parancsát.**
   
    ![Képernyőkép az Adobe Sign SAML beállítások lapjáról](./media/adobe-echosign-tutorial/ic789520.png "Fiók")

1. Az **SAML-beállítások** szakaszban hajtsa végre az alábbi lépéseket:
  
   ![Az SAML beállításainak képernyőképe](./media/adobe-echosign-tutorial/ic789521.png "SAML-beállítások")
   
   ![Az SAML beállításainak képernyőképe](./media/adobe-echosign-tutorial/ic789522.png "SAML-beállítások")

   a. Saml **mód alatt**válassza az **SAML Kötelező**lehetőséget.
   
   b. Válassza **az Echosign-fiók adminisztrátorai számára a bejelentkezés engedélyezése az Echosign hitelesítő adataikkal**lehetőséget.
   
   c. A **Felhasználó létrehozása**csoportban válassza az **SAML-en keresztül hitelesített felhasználók automatikus hozzáadása**lehetőséget.

   d. Illessze be az **Azure Hirdetési azonosítót**, amelyet az Azure Portalról másolt az **Idp-entitásazonosító** szövegmezőjébe.
    
   e. Illessze be **a bejelentkezési URL-címet,** amelyet az Azure Portalról másolt az **Idp bejelentkezési URL-cím** mezőjébe.
   
   f. Illessze **be a kijelentkezési URL-címet,** amelyet az Azure Portalról másolt az **Idp-kijelentkezési URL-cím** szövegmezőjébe.

   g. Nyissa meg a letöltött **tanúsítványfájlt(Base64)** a Jegyzettömbben. Másolja a tartalmát a vágólapra, majd illessze be az **IdP-tanúsítvány** szövegmezőjébe.

   h. Válassza a **Módosítások mentése lehetőséget.**

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

Ebben a szakaszban engedélyezheti Britta Simon számára, hogy egyetlen bejelentkezéssel használja az Azure egyszeri bejelentkezést az Adobe Sign szolgáltatáshoz való hozzáférés biztosításával.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd az Adobe **Sign**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be és válassza az **Adobe Sign**lehetőséget.

    ![Az Adobe Sign hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-adobe-sign-test-user"></a>Adobe Sign-tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek az Adobe Signba, ki kell építeni őket az Adobe Sign ba. Ez egy manuális feladat.

>[!NOTE]
>Az Adobe Sign felhasználói fiókkészítő eszközeinek vagy API-inak az Adobe Sign által biztosított bármely más használatával azure AD felhasználói fiókokat hozhat létre. 

1. Jelentkezzen be az **Adobe Sign** vállalati webhelyére rendszergazdaként.

2. A felső menüben válassza a **Fiók lehetőséget.** Ezután a bal oldali ablaktáblában válassza a **Felhasználók & csoportok** > **új felhasználó létrehozása lehetőséget.**
   
    ![Képernyőkép az Adobe Sign vállalati webhelyéről, amelyen a Fiók, a Felhasználók &csoportok és az Új felhasználó létrehozása kiemelt](./media/adobe-echosign-tutorial/ic789524.png "Fiók")
   
3. Az **Új felhasználó létrehozása** csoportban hajtsa végre az alábbi lépéseket:
   
    ![Képernyőkép: Új felhasználó létrehozása szakasz](./media/adobe-echosign-tutorial/ic789525.png "Felhasználó létrehozása")
   
    a. Írja be a kapcsolódó szövegmezőkbe kiépíteni kívánt érvényes Azure AD-fiók **e-mail címét,** **utónevét**és **vezetéknevét.**
   
    b. Válassza **a Felhasználó létrehozása**lehetőséget.

>[!NOTE]
>Az Azure Active Directory-fiók tulajdonosa kap egy e-mailt, amely tartalmaz egy hivatkozást, hogy erősítse meg a fiókot, mielőtt aktívvá válik. 

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen az Adobe Sign csempére kattint, automatikusan be kell jelentkeznie arra az Adobe Sign-ba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

