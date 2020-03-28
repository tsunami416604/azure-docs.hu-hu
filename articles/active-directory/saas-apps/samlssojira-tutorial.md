---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az SAML SSO for Jira by Resolution GmbH-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az SAML SSO a Jira számára a resolution GmbH segítségével.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 20e18819-e330-4e40-bd8d-2ff3b98e035f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/03/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 957fee48397bc0b23737157dec0e74cf6505fab5
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160126"
---
# <a name="tutorial-azure-active-directory-integration-with-saml-sso-for-jira-by-resolution-gmbh"></a>Oktatóanyag: Az Azure Active Directory integrációja az SAML SSO for Jira by resolution GmbH-val

Ebben az oktatóanyagban megtudhatja, hogyan állíthatja be az SAML SSO-t a Jira by resolution GmbH az Azure Active Directoryval (Azure AD).
A SAML SSO integrálása a Jira számára a Megoldásalapú GmbH és az Azure AD szolgáltatással a következő előnyöket nyújtja:

* Az Azure AD-ben szabályozhatja, hogy ki jelentkezhet be a Jira-ba az SAML SSO plugin felbontással GmbH.You can control in Azure AD who can sign in to Jira with the SAML SSO plugin by resolution GmbH.
* Engedélyezheti, hogy a felhasználók automatikusan bejelentkezve jira az Azure AD-fiókok segítségével SAML SSO jira megoldásként GmbH (Single Sign-On).
* Fiókjait egyetlen központi helyen kezelheti – az Azure Portalon.

Ha további részleteket szeretne megtudni az SaaS-alkalmazások Azure AD-vel való integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció és a Saml SSO beállítása a Jira by resolution GmbH számára a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik Azure AD-környezettel, itt egy hónapos próbaverziót [kaphat.](https://azure.microsoft.com/pricing/free-trial/)
* SAML SSO a Jira számára a határozat által GmbH egyszeri bejelentkezésre engedélyezett előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* SAML SSO for Jira by resolution GmbH támogatja **az SP-t** és **az IDP** által kezdeményezett SSO-t

## <a name="adding-an-enterprise-application-for-single-sign-on"></a>Vállalati alkalmazás hozzáadása egyszeri bejelentkezéshez

Az egyszeri bejelentkezés beállításához az Azure AD-ben új vállalati alkalmazást kell hozzáadnia. A galériában van egy előre konfigurált alkalmazás előre beállított erre, **SAML SSO jira határozat gmbh**.

**Saml SSO a Jira by resolution GmbH-hoz a galériából, hajtsa végre a következő lépéseket:**

1. Az **[Azure Portalon](https://portal.azure.com)** a bal oldali navigációs panelen kattintson az **Azure Active Directory** ikonjára.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Nyissa meg a **Vállalati alkalmazások**( Vállalati alkalmazások ) menüt, majd kattintson a Minden alkalmazás **gombra.**

    ![Az Enterprise alkalmazások panel](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson az **Új alkalmazás** gombra a párbeszéd ablaktetején.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be az **SAML SSO for Jira by resolution GmbH parancsot,** válassza a **SAML SSO for Jira by resolution GmbH** lehetőséget az eredménypanelen, majd kattintson az **Add** gombra az alkalmazás hozzáadásához. Módosíthatja a vállalati alkalmazás nevét is.

     ![SAML SSO a Jira számára határozat szerint GmbH az eredménylistában](common/search-new-app.png)

## <a name="configure-and-test-single-sign-on-with-the-saml-sso-plugin-and-azure-ad"></a>Egyszeri bejelentkezés konfigurálása és tesztelése az SAML SSO beépülő modullal és az Azure AD-vel

Ebben a szakaszban tesztelni fogja, és konfigurálja az egyszeri bejelentkezés t Jira egy Azure AD-felhasználó számára. Ez történik egy teszt felhasználó hívott **Britta Simon**.
Az egyszeri bejelentkezés hez létre kell hozni egy kapcsolatkapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az SAML SSO-ban a Jira by resolution GmbH-ban.

Az egyszeri bejelentkezés konfigurálásához és teszteléséhez a következő lépéseket kell végrehajtania:

1. **[Konfigurálja az Azure AD vállalati alkalmazást egyszeri bejelentkezéshez](#configure-the-azure-ad-enterprise-application-for-single-sign-on)** – Konfigurálja az Azure AD vállalati alkalmazást az egyszeri bejelentkezéshez
2. **[Konfigurálja a Jira példány SAML SSO-bővítményét](#configure-the-saml-sso-plugin-of-your-jira-instance)** – Konfigurálja az egyszeri bejelentkezési beállításokat az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – Hozzon létre egy tesztfelhasználót az Azure AD-ben.
1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi a teszt felhasználó számára, hogy az egyetlen bejelentkezés az Azure oldalán.
1. **[Hozza létre a tesztfelhasználót a Jira-ban](#create-the-test-user-also-in-jira)** – Hozzon létre egy megfelelő tesztfelhasználót a Jira-ban az Azure AD-tesztfelhasználó számára.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)** – Ellenőrizze, hogy a konfiguráció működik-e.

### <a name="configure-the-azure-ad-enterprise-application-for-single-sign-on"></a>Konfigurálja az Azure AD vállalati alkalmazást egyszeri bejelentkezéshez

Ebben a szakaszban beállíthatja az egyszeri bejelentkezést az Azure Portalon.

A Corr a Resolution GmbH által imaszóval egyszeri bejelentkezés konfigurálásához hajtsa végre a következő lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)a közvetlenül létrehozott **SAML SSO for Jira by resolution GmbH** vállalati alkalmazás, válassza **az Egyszeri bejelentkezés** a bal oldali panelen.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása**csoportban válassza ki az **SAML** módot az egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Ezután kattintson a **Szerkesztés** ikonra az **Egyszerű SAML konfiguráció párbeszédpanel** megnyitásához.

    ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépéseket:

    ![SAML SSO a Jira számára határozat szerint GmbH Domain és URL-ek egyszeri bejelentkezési információk](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

    c. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást sp által kezdeményezett módban kívánja konfigurálni: **SP**

    ![SAML SSO a Jira számára határozat szerint GmbH Domain és URL-ek egyszeri bejelentkezési információk](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<server-base-url>/plugins/servlet/samlsso`

    > [!NOTE]
    > Az azonosító, a válasz URL-címe és a bejelentkezési URL-cím esetében a helyettesítő ** \<kiszolgáló-bázis url-t>** a Jira-példány alap URL-címével. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat. Ha problémája van, lépjen kapcsolatba velünk a [SAML SSO for Jira by resolution GmbH Ügyfél támogatási csapatnál.](https://www.resolution.de/go/support)

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszban** töltse le az **összevonási metaadat-XML-t,** és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-the-saml-sso-plugin-of-your-jira-instance"></a>A Jira példány SAML SSO-bővítményének konfigurálása 

1. Egy másik böngészőablakban jelentkezzen be a Jira-példányba rendszergazdaként.

2. Mutasson a fogaskerék reaktikra a jobb oldalon, és kattintson az **Alkalmazások kezelése**parancsra.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon1.png)

3. Ha a rendszergazdai hozzáférés lapra irányítja át a rendszer, írja be a **Jelszót,** és kattintson a **Megerősítés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon2.png)

4. Jira általában átirányítja Önt az Atlassian piactérre. Ha nem, kattintson az **Új alkalmazások keresése** a bal oldali panelen elemre. Keresse meg **az SAML Single Sign On (SSO) kifejezést a JIRA számára,** és kattintson a **Telepítés** gombra az SAML beépülő modul telepítéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/store.png)

5. A plugin telepítés indul. Ha elkészült, kattintson a **Bezárás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/store-2.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/store-3.png)

6. Ezután kattintson a **Kezelés gombra.**

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/store-4.png)
    
8. Ezután kattintson a **Konfigurálás gombra** az éppen telepített bővítmény konfigurálásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/store-5.png)

9. Az **SAML SingleSignOn plugin konfigurálása** varázslóban kattintson **az Új IdP hozzáadása** elemre az Azure AD új identitásszolgáltatóként való konfigurálásához.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon4.png) 

10. Az **SAML-identitásszolgáltató kiválasztása lapon hajtsa** végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon5a.png)
 
    a. Állítsa be az **Azure AD-t** IdP-típusként.
    
    b. Adja hozzá az identitásszolgáltató **nevét** (például Az Azure AD).
    
    c. Adjon hozzá egy (nem kötelező) az identitásszolgáltató (pl. Azure AD) **ismertetése.**
    
    d. Kattintson a **Tovább** gombra.
    
11. Az **Identitásszolgáltató konfigurációja** lapon kattintson a **Tovább**gombra.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon5b.png)

12. Az **SAML IdP metaadatok importálása lapon hajtsa** végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon5c.png)

    a. Kattintson a **Metaadat-XML-fájl kijelölése gombra,** és válassza ki a korábban letöltött **összevonási metaadat-XML-fájlt.**

    b. Kattintson az **Importálás** gombra.
     
    c. Várjon röviden, amíg az importálás sikeres lesz.  
     
    d. Kattintson a **Tovább** gombra.
    
13. A **User ID attribútum és az átalakítási** lapon kattintson a **Tovább** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon5d.png)
    
14. A **Beállítások mentéséhez** kattintson a Felhasználó létrehozása és frissítése lapon **a & Mentés gombra.**
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon6a.png)
    
15. A **Beállítások tesztelése** lapon kattintson a **Teszt kihagyása & manuáliskonfigurálása** gombra a felhasználói teszt egyelőre való kihagyásához. Ez a következő szakaszban kerül végrehajtásra, és az Azure Portalon bizonyos beállításokat igényel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon6b.png)
    
16. A figyelmeztetés kihagyásához kattintson az **OK** gombra.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/samlssojira-tutorial/addon6c.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ez a szakasz célja, hogy hozzon létre egy tesztfelhasználót az Azure Portalon Britta Simon. A felhasználóval tesztelni fogja az egyszeri bejelentkezést.

1. Az Azure Portalon a bal oldali ablaktáblában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A **Felhasználó tulajdonságai párbeszédpanelen**hajtsa végre az alábbi lépéseket:

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    a. A **Név** mezőbe írja be **Britta Simon ( Britta Simon**) értéket.
  
    b. A **Felhasználónév** mezőbe <b>BrittaSimon@contoso.com</b>írja be a mezőt.

    c. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a Jelszó mezőben megjelenő értéket.

    d. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban hozzáadja Britta Simon a vállalati alkalmazás, amely lehetővé teszi számára, hogy használja az egyszeri bejelentkezés.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget. 

    ![A vállalati alkalmazások panelje](common/enterprise-applications.png)

2. Az alkalmazások listájában keresse meg az oktatóanyag elején létrehozott vállalati alkalmazást. Ha követi a lépéseket a bemutató, ez az úgynevezett **SAML SSO jira határozat gmbh**. Ha adott neki egy másik nevet, keresse meg ezt a nevet.

    ![A SAML SSO a Jira by resolution GmbH link az Alkalmazások listában](common/all-applications.png)

3. A bal oldali panelen kattintson a **Felhasználók és csoportok**elemre.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** **párbeszédpanelen** a Felhasználók és csoportok lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználó lista **Britta Simon** elemet, majd kattintson a képernyő alján található **Kijelölés** gombra.

6. Ha az SAML-feltételben szerepkörértéket vár, akkor a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó megfelelő szerepkörét a listából, majd kattintson a képernyő alján található **Kijelölés** gombra.

7. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-the-test-user-also-in-jira"></a>Hozza létre a teszt felhasználó is Jira

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a SamL SSO-ba a Jira by resolution GmbH-hoz, a Saml SSO-ba kell beépíteni őket a Resolution GmbH által. Ebben az esetben az oktatóanyag, meg kell csinálni a kiépítés kézzel. Azonban vannak más kiépítési modellek is elérhetők az SAML SSO plugin megoldásonként, például **just in time** kiépítés. Lásd a [SAML SSO által a resolution GmbH-nál eltaláló](https://wiki.resolution.de/doc/saml-sso/latest/all)dokumentációt. Ha kérdése van, forduljon a [resolution support ügyfélszolgálatához.](https://www.resolution.de/go/support)

**Felhasználói fiók manuális kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Jira példányba rendszergazdaként.

2. Mutasson a fogaskerék fölé, és válassza a **Felhasználókezelés**lehetőséget.

   ![Alkalmazott hozzáadása](./media/samlssojira-tutorial/user1.png)

3. Ha a rendszergazdai hozzáférés lapra irányítja át a rendszer, írja be a **Jelszót,** és kattintson a **Megerősítés** gombra.

    ![Alkalmazott hozzáadása](./media/samlssojira-tutorial/user2.png) 

4. A **Felhasználókezelés** lapon kattintson a **Felhasználó létrehozása gombra.**

    ![Alkalmazott hozzáadása](./media/samlssojira-tutorial/user3-new.png) 

5. Az **"Új felhasználó létrehozása"** párbeszédpanelen hajtsa végre a következő lépéseket. A felhasználót pontosan ugyanúgy kell létrehoznia, mint az Azure AD-ben:

    ![Alkalmazott hozzáadása](./media/samlssojira-tutorial/user4-new.png) 

    a. Az **E-mail cím** mezőbe írja be a <b>BrittaSimon@contoso.com</b>felhasználó e-mail címét: .

    b. A **Teljes név** mezőbe írja be a felhasználó teljes nevét: **Britta Simon**.

    c. A **Felhasználónév** mezőbe írja be a felhasználó <b>BrittaSimon@contoso.com</b>e-mail címét: . 

    d. A **Jelszó** mezőbe írja be a felhasználó jelszavát.

    e. A felhasználó létrehozásának befejezéséhez kattintson a **Felhasználó létrehozása** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Jira by resolution GmbH határozattal készült SAML SSO-ra kattint, automatikusan be kell jelentkeznie az SAML SSO for Jira-ba a GmbH által, amelyre beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Azt is tesztelni egyszeri bejelentkezés, ha navigálni [https://\<szerver-base-url>/plugins/servlet/samlsso](https://\<server-base-url>/plugins/servlet/samlsso). Helyettesítő ** \<szerver-base-url>** a Jira példány alap URL-címével.


## <a name="enable-single-sign-on-redirection-for-jira"></a>Egyszeri bejelentkezési átirányítás engedélyezése Jira számára

Amint azt a szakasz ban korábban, jelenleg két módon lehet elindítani az egyszeri bejelentkezés. Az Azure **Portal** használatával vagy **a Jira-példányra mutató speciális hivatkozás**használatával. A SAML SSO plugin felbontásban GmbH is lehetővé teszi, hogy kiváltó egyszeri bejelentkezés egyszerűen **elérése url mutat a Jira példány**.

Lényegében, minden felhasználó hozzáférés Jira lesz irányítva az egyetlen bejelentkezés után aktiváló lehetőség a plugin.

Az SSO-átirányítás aktiválásához tegye a következőket **a Jira példányban:**

1. Hozzáférés a konfigurációs oldalon a SAML SSO plugin Jira.
1. Kattintson az **Átirányítás gombra** a bal oldali panelen.
![](./media/samlssojira-tutorial/ssore1.png)

1. Jelölje be **az SSO-átirányítás engedélyezése**.
![](./media/samlssojira-tutorial/ssore2.png) 

1. Nyomja meg a **Beállítások mentése** gombot a jobb felső sarokban.

Aktiválása után a lehetőséget, akkor is elérheti a felhasználónevet / jelszót kérdés, ha a **noszso engedélyezése** opció be van jelölve a navigációt [https://\<szerver-base-url>/login.jsp?nosso](https://\<server-base-url>/login.jsp?nosso). Mint mindig, helyettesítő ** \<szerver-base-url>** az alap URL-t.


## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

