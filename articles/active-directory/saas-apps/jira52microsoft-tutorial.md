---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Microsoft JIRA SAML SSO-jával (V5.2) | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Microsoft JIRA SAML SSO között (V5.2).
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: d0c00408-f9b8-4a79-bccc-c346a7331845
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 04/22/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d3b304bb35ea69906fc9576f45733134387be1b9
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67099516"
---
# <a name="tutorial-azure-active-directory-integration-with-jira-saml-sso-by-microsoft-v52"></a>Oktatóanyag: Az Azure Active Directory integrációja a Microsoft JIRA SAML SSO-jával (V5.2)

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a JIRA SAML SSO-t a Microsoft (V5.2) az Azure Active Directoryval (Azure AD).
A JIRA SAML SSO Microsoft általi (V5.2) integrálása az Azure AD-vel a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Microsoft JIRA SAML SSO-jához (V5.2).
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jira SAML Egyszeri bejelentkezés a Microsoft (V5.2) (Single Sign-On) az Azure AD-fiókok.
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="description"></a>Leírás

Az Atlassian JIRA kiszolgálóval rendelkező Microsoft Azure Active Directory-fiókjával engedélyezheti az egyszeri bejelentkezést. Így a szervezet összes felhasználója használhatja az Azure AD hitelesítő adatait a JIRA alkalmazásba való bejelentkezéshez. Ez a plugin saml 2.0-t használ az összevonáshoz.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Microsoft JIRA SAML SSO-jával (V5.2) a következő elemekre van szükség:

- Azure AD-előfizetés
- A JIRA Core és a Software 5.2-nek windowsos 64 bites verzióra kell telepítve és konfigurálva
- A JIRA kiszolgáló HTTPS-kompatibilis
- Megjegyzés: a JIRA Plugin támogatott verzióit az alábbi szakasz ban említi.
- A JIRA-kiszolgáló elérhető az interneten, különösen az Azure AD bejelentkezési lapjára a hitelesítéshez, és képesnek kell lennie a jogkivonat fogadására az Azure AD-től
- A rendszergazdai hitelesítő adatok a JIRA-ban vannak beállítva
- A WebSudo le van tiltva a JIRA-ban
- A JIRA kiszolgálóalkalmazásban létrehozott tesztfelhasználó

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem javasoljuk a JIRA éles környezetének használatát. Tesztelje az integrációt először az alkalmazás fejlesztési vagy átmeneti környezetében, majd használja az éles környezetet.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi ajánlásokat:

- Ne használja az éles környezetet, kivéve, ha ez szükséges.
- Ha nem rendelkezik Azure AD próbakörnyezettel, itt egy hónapos próbaverziót kaphat: [Próbaajánlat.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="supported-versions-of-jira"></a>A JIRA támogatott verziói

* JIRA Core és szoftver: 5.2
* A JIRA támogatja a 6,0–7,12-es További részletekért kattintson [a Microsoft JIRA SAML SSO](jiramicrosoft-tutorial.md)

> [!NOTE]
> Kérjük, vegye figyelembe, hogy a JIRA Plugin is működik az Ubuntu Version 16.04

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Jira SAML SSO a Microsoft (V5.2) támogatja **SP** kezdeményezett SSO

## <a name="adding-jira-saml-sso-by-microsoft-v52-from-the-gallery"></a>Jira SAML SSO hozzáadása a Microsofttól (V5.2) a galériából

A JIRA SAML SSO Microsoft általi (V5.2) Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a JIRA SAML SSO-t a Microsoft (V5.2) rendszerből a felügyelt SaaS-alkalmazások listájához.

**Ha a Microsoft JIRA SAML SSO-ját (V5.2) szeretné hozzáadni a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza a **Minden alkalmazás** lehetőséget.

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablak tetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Microsoft JIRA SAML SSO (V5.2)** kifejezést, válassza a **JIRA SAML SSO by Microsoft (V5.2)** elemet az eredménypanelen, majd kattintson a **Hozzáadás** gombra az alkalmazás hozzáadásához.

    ![JIRA SAML SSO a Microsoft (V5.2) az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Ebben a szakaszban konfigurálja és teszteli az Azure AD egyszeri bejelentkezését a Microsoft JIRA SAML Egyszeri Bejelentkezésével (V5.2) egy **Britta Simon**nevű tesztfelhasználó alapján.
Egyszeri bejelentkezés működéséhez létre kell hozni egy kapcsolat közötti kapcsolat egy Azure AD-felhasználó és a kapcsolódó felhasználó JIRA SAML SSO a Microsoft (V5.2) létre kell hozni.

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Microsoft JIRA SAML SSO-jával (V5.2) a következő építőelemeket kell végrehajtania:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezést](#configure-azure-ad-single-sign-on)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja a JIRA SAML SSO-t a Microsoft által (V5.2) Egyszeri bejelentkezés](#configure-jira-saml-sso-by-microsoft-v52-single-sign-on)** – az egyszeri bejelentkezési beállítások alkalmazásoldali konfigurálásához.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre JIRA SAML SSO a Microsoft (V5.2) teszt felhasználó](#create-jira-saml-sso-by-microsoft-v52-test-user)** – hogy egy megfelelője Britta Simon jira SAML SSO a Microsoft (V5.2), amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Ebben a szakaszban engedélyezi az Azure AD egyszeri bejelentkezést az Azure Portalon.

Az Azure AD egyszeri bejelentkezésének konfigurálásához a Microsoft JIRA SAML SSO (V5.2) használatával hajtsa végre az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a **Microsoft (V5.2)** alkalmazásintegrációs lapján válassza az **Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** párbeszédpanelen válassza **az SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon kattintson a **Szerkesztés** ikonra az **Egyszerű SAML-konfiguráció** párbeszédpanel megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    ![JIRA SAML SSO a Microsoft (V5.2) tartomány és URL-címek egyszeri bejelentkezési információk](common/sp-identifier-reply.png)

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<domain:port>/plugins/servlet/saml/auth`

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<domain:port>/`

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Port nem kötelező abban az esetben, ha egy elnevezett URL-t. Ezek az értékek a Jira plugin konfigurációja során érkeznek, amelyet később a bemutató ban ismertetünk.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="configure-jira-saml-sso-by-microsoft-v52-single-sign-on"></a>A JIRA SAML SSO microsoftos (V5.2) egyszeri bejelentkezéskonfigurálása

1. Egy másik böngészőablakban jelentkezzen be a JIRA-példányba rendszergazdaként.

2. Mutasson a fogaskerékre, és kattintson **a bővítményekre.**

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon1.png)

3. A Bővítmények lap csoportban kattintson **a Bővítmények kezelése gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon7.png)

4. Töltse le a bővítményt a [Microsoft letöltőközpontjából.](https://www.microsoft.com/download/details.aspx?id=56521) Manuálisan töltse fel a Microsoft által biztosított bővítményt **az Upload add-on** menüben. A bővítmény letöltését a [Microsoft szolgáltatási szerződése](https://www.microsoft.com/servicesagreement/)fedezi.

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon12.png)

5. Miután a plugin telepítve van, úgy tűnik, a **felhasználó által telepített** bővítmények részben. Az új bővítmény konfigurálásához kattintson a **Konfigurálás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon13.png)

6. Hajtsa végre a következő lépéseket a konfigurációs lapon:

    ![Egyszeri bejelentkezés konfigurálása](./media/jira52microsoft-tutorial/addon52.png)

    > [!TIP]
    > Győződjön meg arról, hogy csak egy tanúsítvány van leképezve az alkalmazáshoz, hogy ne legyen hiba a metaadatok feloldása. Ha több tanúsítvány van, a metaadatok feloldásakor a rendszergazda hibaüzenetet kap.

    a. A **Metaadatok URL-címmezőjében** illessze be **az Alkalmazásösszevonás metaadat-URL-címét,** amelyet az Azure Portalról másolt, és kattintson a **Feloldás gombra.** Beolvassa az IdP metaadat-URL-címét, és feltölti az összes mezőinformációt.

    b. Másolja az **azonosítót, a válasz URL-címét és a Bejelentkezés az URL-értékeket,** és illessze be őket **az Azonosító, a Válasz URL-cím és a Bejelentkezés url-címbe** az Azure Portal **alapszintű SAML-konfigurációs** szakaszában.

    c. A **Bejelentkezési gomb neve** mezőbe írja be annak a gombnak a nevét, amelyet a szervezet a felhasználóknak látnia kell a bejelentkezési képernyőn.

    d. Az **SAML felhasználói azonosító helyek között** válassza a Felhasználói azonosító t a Tárgy utasítás **NameIdentifier elemében,** vagy a **felhasználói azonosító attribútumelemben.**  Ennek az azonosítónak a JIRA felhasználói azonosítónak kell lennie. Ha a felhasználói azonosító nem egyezik, akkor a rendszer nem engedélyezi a felhasználók számára a bejelentkezést.

    > [!Note]
    > Az alapértelmezett SAML-felhasználóazonosító helye a névazonosító. Ezt attribútumbeállításra módosíthatja, és megadhatja a megfelelő attribútumnevet.

    e. Ha a **Felhasználói azonosító attribútumelem-beállításban lehetőséget választja,** akkor az **Attribútumnév** szövegmezőbe írja be annak az attribútumnak a nevét, ahol a felhasználói azonosító várható. 

    f. Ha az összevont tartományt (például Az ADFS stb.) használja az Azure AD-vel, kattintson a **Home Realm Discovery engedélyezése** lehetőségre, és konfigurálja a **tartománynevet**.

    g. A **Tartománynév** mezőbe írja be a tartománynevet ide az ADFS-alapú bejelentkezés esetén.

    h. Jelölje be **az Egyszeri kijelentkezés engedélyezése** jelölőnégyzetet, ha ki szeretne jelentkezni az Azure AD-ből, amikor egy felhasználó kijelentkezik a JIRA-ból. 

    i. A beállítások mentéséhez kattintson a **Mentés** gombra.

    > [!NOTE]
    > A telepítéssel és a hibaelhárítással kapcsolatos további információkért látogasson el [az MS JIRA SSO Connector Admin Guide oldalra,](../ms-confluence-jira-plugin-adminguide.md) és a gyakran ismételt kérdések is [rendelkezésre](../ms-confluence-jira-plugin-faq.md) állnak a segítségért.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A Felhasználó tulajdonságokban hajtsa végre a következő lépéseket.

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **a BrittaSimon**értéket.
  
    b. A **Felhasználónév** mező `brittasimon\@yourcompanydomain.extension`típusa mezőben. Például: BrittaSimon@contoso.com.

    c. Jelölje be **a Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon azure egyszeri bejelentkezés t azáltal, hogy hozzáférést biztosít a JIRA SAML SSO a Microsoft (V5.2).

1. Az Azure portalon válassza a **Vállalati alkalmazások**lehetőséget, válassza az **Összes alkalmazás**lehetőséget, majd válassza a Microsoft **JIRA SAML SSO (V5.2)** lehetőséget.

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában válassza a **Microsoft JIRA SAML SSO (V5.2) lehetőséget.**

    ![A JIRA SAML SSO by Microsoft (V5.2) hivatkozása az Alkalmazások listában](common/all-applications.png)

3. A bal oldali menüben válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Kattintson a **Felhasználó hozzáadása** gombra, majd a **Hozzárendelés hozzáadása** **párbeszédpanelen** válassza a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza **a Britta Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**

6. Ha az SAML-helyességben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson **a** kijelölés gombra a képernyő alján.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-jira-saml-sso-by-microsoft-v52-test-user"></a>JIRA SAML SSO létrehozása a Microsoft (V5.2) tesztfelhasználója által

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a JIRA helyszíni kiszolgálóra, ki kell építeni őket a JIRA helyszíni kiszolgálóra.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a JIRA helyszíni kiszolgálójára rendszergazdaként.

2. Mutasson a fogaskerékre, és kattintson a **Felhasználókezelés parancsra.**

    ![Alkalmazott hozzáadása](./media/jira52microsoft-tutorial/user1.png)

3. A rendszer átirányítja a Rendszergazdai hozzáférés lapra, hogy beírja a **Jelszót,** és kattintson a **Megerősítés** gombra.

    ![Alkalmazott hozzáadása](./media/jira52microsoft-tutorial/user2.png)

4. A **Felhasználókezelés** lap csoportban kattintson **a Felhasználó létrehozása gombra.**

    ![Alkalmazott hozzáadása](./media/jira52microsoft-tutorial/user3.png) 

5. Az **"Új felhasználó létrehozása"** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![Alkalmazott hozzáadása](./media/jira52microsoft-tutorial/user4.png)

    a. Az **E-mail cím** mezőbe írja be Brittasimon@contoso.coma felhasználó e-mail címét, például .

    b. A **Teljes név** mezőbe írja be a felhasználó teljes nevét, például Britta Simon.

    c. A **Felhasználónév** mezőbe írja be a Brittasimon@contoso.comfelhasználó e-mail címét, például .

    d. A **Jelszó** mezőbe írja be a felhasználó jelszavát.

    e. Kattintson **a Felhasználó létrehozása gombra.**

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Microsoft JIRA SAML SSO (V5.2) csempéjére kattint, akkor automatikusan be kell jelentkeznie a Microsoft által a JIRA SAML SSO-ba (V5.2), amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
