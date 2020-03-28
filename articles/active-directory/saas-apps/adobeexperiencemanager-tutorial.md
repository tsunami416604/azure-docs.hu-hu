---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Adobe Experience Managerrel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Adobe Experience Manager között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 88a95bb5-c17c-474f-bb92-1f80f5344b5a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f39751f40b32c5da24e13d75d2607d7da0a57ad3
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73154115"
---
# <a name="tutorial-azure-active-directory-integration-with-adobe-experience-manager"></a>Oktatóanyag: Az Azure Active Directory integrációja az Adobe Experience Managerrel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Adobe Experience Managert az Azure Active Directoryval (Azure AD).
Az Adobe Experience Manager integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá az Adobe Experience Manager hez.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve legyenek az Adobe Experience Managerbe (Single Sign-On) az Azure AD-fiókjukkal.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció adobe experience managerrel való konfigurálásához a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, [itt](https://azure.microsoft.com/pricing/free-trial/) egy hónapos próbaverziót kaphat
* Egyszeri bejelentkezéssel rendelkező Adobe Experience Manager előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az Adobe Experience Manager támogatja az **SP és az IDP** által kezdeményezett sso-t

* Az Adobe Experience Manager támogatja **a Just In Time** felhasználói kiépítést

## <a name="adding-adobe-experience-manager-from-the-gallery"></a>Az Adobe Experience Manager hozzáadása a galériából

Az Adobe Experience Manager Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Adobe Experience Managert a katalógusból a felügyelt SaaS-alkalmazások listájához.

**Ha az Adobe Experience Managert a galériából szeretné felvenni, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az Adobe Experience Manager ( **Adobe Experience Manager**) kifejezést, válassza az Adobe Experience **Manager** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

     ![Az Adobe Experience Manager az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést az [Alkalmazás név] szolgáltatással egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó [Alkalmazás neve] létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez az [Alkalmazás neve] beállításához a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az Adobe Experience Manager Single Sign-On --hoz](#configure-adobe-experience-manager-single-sign-on)** konfigurálni az egyszeri bejelentkezés beállításait az alkalmazás oldalán.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre Adobe Experience Manager tesztfelhasználót](#create-adobe-experience-manager-test-user)** – ha az Adobe Experience Managerben britta Simon megfelelője is részt szeretne, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához az [Alkalmazás név] segítségével hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)az **Adobe Experience Manager** alkalmazásintegrációs lapján válassza az Egyszeri **bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban Ha az alkalmazást **IDP** által kezdeményezett módban kívánja konfigurálni, hajtsa végre a következő lépéseket:

    ![Az Adobe Experience Manager tartomány- és URL-címei egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy egyedi értéket, amelyet az AEM-kiszolgálón is megad.

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<AEM Server Url>/saml_login`

    > [!NOTE]
    > A Válasz URL-értéke nem valós. A válasz URL-értékének frissítése a tényleges válasz URL-címével. Ennek az értéknek az alkalmazásához lépjen kapcsolatba az [Adobe Experience Manager ügyfélszolgálati csapatával,](https://helpx.adobe.com/support/experience-manager.html) hogy megkapja ezt az értéket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    ![Az Adobe Experience Manager tartomány- és URL-címei egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írja be az Adobe Experience Manager kiszolgáló URL-címét.

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában kattintson a **Letöltés** gombra a **tanúsítvány (Base64)** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

7. Az **Adobe Experience Manager beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="configure-adobe-experience-manager-single-sign-on"></a>Az Adobe Experience Manager egyszeri bejelentkezésének konfigurálása

1. Egy másik böngészőablakban nyissa meg az **Adobe Experience Manager** felügyeleti portált.

2. Válassza a **Beállítások** > **biztonsági** > **felhasználók lehetőséget.**

    ![Az egyszeri bejelentkezési mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user.png)

3. Válassza **a Rendszergazda** vagy bármely más érintett felhasználó lehetőséget.

    ![Egyszeri bejelentkezés – mentés konfigurálása gomb](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin6.png)

4. Válassza **a Fiókbeállítások** > **manage TrustStore**lehetőséget.

    ![Egyszeri bejelentkezés – mentés konfigurálása gomb](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_managetrust.png)

5. A **Tanúsítvány hozzáadása a CER-fájlból csoportban**kattintson a **Tanúsítványfájl kiválasztása gombra.** Tallózással keresse meg és válassza ki a tanúsítványfájlt, amelyet már letöltött az Azure Portalról.

    ![Egyszeri bejelentkezési mentés idáig konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_user2.png)

6. A tanúsítvány hozzáadódik a TrustStore-hoz. Jegyezze fel a tanúsítvány aliasát.

    ![Egyszeri bejelentkezés – mentés konfigurálása gomb](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin7.png)

7. A **Felhasználók** lapon válassza a **hitelesítési szolgáltatást.**

    ![Egyszeri bejelentkezés – mentés konfigurálása gomb](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin8.png)

8. Válassza **a Fiókbeállítások** > **Létrehozása/Kezelése a KeyStore lehetőséget.** Hozzon létre KeyStore jelszóval.

    ![Egyszeri bejelentkezés – mentés konfigurálása gomb](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin9.png)

9. Lépjen vissza az admin képernyőre. Ezután válassza a **Beállítások** > **műveletek** > **webkonzol lehetőséget**.

    ![Egyszeri bejelentkezés – mentés konfigurálása gomb](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin1.png)

    Ezzel megnyitja a konfigurációs lapot.

    ![Az egyszeri bejelentkezési mentés gomb konfigurálása](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin2.png)

10. Keresse meg **az Adobe Gránit SAML 2.0 hitelesítéskezelőt.** Ezután válassza a **Hozzáadás** ikont.

    ![Egyszeri bejelentkezés – mentés konfigurálása gomb](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin3.png)

11. Ezen az oldalon tegye a következő műveleteket.

    ![Egyszeri bejelentkezés – mentés konfigurálása gomb](./media/adobeexperiencemanager-tutorial/tutorial_adobeexperiencemanager_admin4.png)

    a. A **Görbe** mezőbe **/** írja be a be írt értéket.

    b. Az **IDP URL-címe** mezőbe írja be az Azure Portalról másolt **bejelentkezési URL-címet.**

    c. Az **IDP-tanúsítvány aliasa** mezőbe írja be a TrustStore-ban hozzáadott **Tanúsítványalias** értéket.

    d. A **Biztonság biztosított entitásazonosító** mezőben adja meg az Azure Portalon konfigurált egyedi **Azure-hirdetési azonosító** értékét.

    e. A **helyességi feltétel fogyasztói szolgáltatás URL-címét** mezőbe írja be az Azure Portalon konfigurált **Válasz URL-értéke.**

    f. A **Kulcstároló jelszava** mezőbe írja be a KeyStore alkalmazásban beállított **jelszót.**

    g. A **Felhasználói attribútum azonosítója** mezőbe írja be a **névazonosítót** vagy egy másik, az ön esetében releváns felhasználói azonosítót.

    h. Válassza **a CRX-felhasználók automatikus létrehozása**lehetőséget.

    i. A **Kijelentkezés URL-címe** mezőbe írja be az Azure Portalon kapott egyedi **kijelentkezési URL-címet.**

    j. Kattintson a **Mentés** gombra.

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

Ebben a szakaszban engedélyezheti Britta Simon számára, hogy egyetlen bejelentkezéssel használja az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít az Adobe Experience Manager szolgáltatáshoz.

1. Az Azure portalon válassza az **Enterprise Applications**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd az Adobe **Experience Manager**lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza az **Adobe Experience Manager**lehetőséget.

    ![Az Adobe Experience Manager hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-adobe-experience-manager-test-user"></a>Adobe Experience Manager tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre az Adobe Experience Manager programban. Ha a **CRX-felhasználók automatikus létrehozása** lehetőséget választotta, a felhasználók a sikeres hitelesítés után automatikusan létrejönnek.

Ha manuálisan szeretne felhasználókat létrehozni, az [Adobe Experience Manager támogatási csapatával](https://helpx.adobe.com/support/experience-manager.html) együttműködve vegye fel a felhasználókat az Adobe Experience Manager platformra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen az Adobe Experience Manager csempére kattint, automatikusan be kell jelentkeznie az Adobe Experience Manager betöltésébe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
