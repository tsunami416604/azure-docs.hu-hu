---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az iLMS-szel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az iLMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: d6e11639-6cea-48c9-b008-246cf686e726
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/14/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 50097aec1c4a003d3494029e8f25bb13b564f207
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68944023"
---
# <a name="tutorial-integrate-ilms-with-azure-active-directory"></a>Oktatóanyag: Az iLMS integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az iLMS-t az Azure Active Directoryval (Azure AD). Ha integrálja az iLMS-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az iLMS-hez.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve az iLMS-be az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [itt](https://azure.microsoft.com/pricing/free-trial/)egy hónapos ingyenes próbaverziót kaphat.
* iLMS egyszeri bejelentkezés (SSO) engedélyezve van az előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. Az iLMS támogatja az **SP és az IDP** által kezdeményezett SSO-t

## <a name="adding-ilms-from-the-gallery"></a>ILMS hozzáadása a galériából

Az iLMS-nek az Azure AD-be való integrálásához hozzá kell adnia az iLMS-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be az **iLMS-t** a keresőmezőbe.
1. Válassza az **iLMS** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t az iLMS-sel egy **Britta Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó iLMS-ben.

Az Azure AD SSO iLMS-sel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az iLMS egyszeri bejelentkezést](#configure-ilms-sso)** – az egyszeri bejelentkezési beállítások alkalmazásoldali konfigurálásához.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre iLMS-tesztfelhasználót](#create-ilms-test-user)** – ha az iLMS-ben britta Simon megfelelője van, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **iLMS-alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** lapon, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** szövegmezőbe illessze be az iLMS felügyeleti portál SAML-beállításainak **AMAl-beállítások** SamL-beállítások ból másolt **azonosító** értéket.

    b. A **Válasz URL-cím** mezőjébe illessze be a **Szolgáltatóból** másolt **Végpont (URL)** értéket az iLMS felügyeleti portál SAML-beállításainak a következő mintával`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe illessze be az iLMS felügyeleti portál SAML-beállításainak **ABA-beállítások Szolgáltatásszolgáltató** szakaszából másolt Végpont **(URL)-értéket**`https://www.inspiredlms.com/Login/<instanceName>/consumer.aspx`

1. A JIT-kiépítés engedélyezéséhez az iLMS-alkalmazás egy adott formátumban várja az SAML-állításokat, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumok konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a Felhasználói attribútumok párbeszédpanel megnyitásához.

    > [!NOTE]
    > Az attribútumok leképezéséhez engedélyeznie kell **az iLMS-ben az Ismeretlen felhasználói fiók létrehozását.** Az [alábbi](https://support.inspiredelearning.com/help/adding-updating-and-managing-users#just-in-time-provisioning-with-saml-single-signon) utasításokat követve képet kaphat az attribútumok konfigurációjának konfigurációjáig.

1. A fentieken kívül az iLMS-alkalmazás azt várja, hogy néhány további attribútum ot kell visszaadni az SAML-válaszban. A **Felhasználói attribútumok** párbeszédpanel **Felhasználói jogcímek** szakaszában hajtsa végre az alábbi lépéseket az SAML token attribútum hozzáadásához az alábbi táblázatban látható módon:

    | Név | Forrás attribútuma|
    | --------|------------- |
    | Osztály | user.department (felhasználó.részleg) |
    | régió | user.state |
    | Részleg | user.jobtitle |

    a. Kattintson **az Új jogcím hozzáadása** gombra a Felhasználói **jogcímek kezelése** párbeszédpanel megnyitásához.

    b. A **Név** mezőbe írja be a sor attribútumnevét.

    c. Hagyja üresen a **névteret.**

    d. Válassza a Forrás **attribútumként lehetőséget.**

    e. A **Forrás attribútumlistában** írja be a sor attribútumértékét.

    f. Kattintson **az Ok gombra**

    g. Kattintson a **Mentés** gombra.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **iLMS beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

### <a name="configure-ilms-sso"></a>ILMS-sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként az **iLMS felügyeleti portáljára.**

2. Kattintson az **SSO:SAML gombra** a **Beállítások** lapon az SAML-beállítások megnyitásához és a következő lépések végrehajtásához:

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/1.png)

3. Bontsa ki a **Szolgáltató** szakaszt, és másolja az **Azonosító** és **végpont (URL)** értékét.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/2.png) 

4. Az **Identitásszolgáltató** csoportban kattintson **a Metaadatok importálása gombra.**

5. Válassza ki az **Összevonási metaadat-fájlt,** amelyet az Azure Portalról letöltöttaz **SAML aláíró tanúsítvány** szakaszból.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_ssoconfig1.png)

6. Ha engedélyezni szeretné a JIT-kiépítést, hogy iLMS-fiókokat hozzon létre a felhasználók nem ismeri fel, kövesse az alábbi lépéseket:

    a. Jelölje **be a Fel nem ismert felhasználói fiók létrehozása jelölőnégyzetet.**

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/tutorial_ilms_ssoconfig2.png)

    b. Az Azure AD attribútumainak leképezése az iLMS attribútumaival. Az attribútumoszlopban adja meg az attribútumnevét vagy az alapértelmezett értéket.

    c. Nyissa meg az **Üzleti szabályok** lapot, és hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/5.png)

    d. Jelölje be **a Fel nem ismert régiók, osztályok és részlegek létrehozása** jelölőnégyzetet, ha olyan régiókat, részlegeket és részlegeket hoz létre, amelyek az egyszeri bejelentkezés kor még nem léteznek.

    e. Jelölje be **a Felhasználói profil frissítése a bejelentkezés során jelölőnégyzetet,** ha meg szeretné adni, hogy a felhasználó profilja frissül-e az egyes egyszeri bejelentkezésekkel.

    f. Ha a **Felhasználói profil nem kötelező mezőinek üres értékek frissítése** jelölőnégyzet be van jelölve, a bejelentkezéskor üresprofil-mezők a felhasználó iLMS-profilját is üres értékeket fognak tartalmazni ezekhez a mezőkhöz.

    g. Jelölje be **a Hibaértesítő küldése e-mailt,** és adja meg annak a felhasználónak az e-mail címét, ahová a hibaértesítő e-mailt szeretné kapni.

7. A beállítások mentéséhez kattintson a **Mentés** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/ilms-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon Britta Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi Britta Simon az Azure egyszeri bejelentkezést az iLMS-hez való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **iLMS**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználó lista **Britta Simon** elemet, majd kattintson a képernyő alján található **Kijelölés** gombra.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-ilms-test-user"></a>ILMS-tesztfelhasználó létrehozása

Alkalmazás támogatja a Just in time felhasználói kiépítés és hitelesítés után a felhasználók jönnek létre az alkalmazásban automatikusan. A JIT működni fog, ha az iLMS felügyeleti portál saml-konfigurációs beállítása során a **Nem felismert felhasználói fiók létrehozása** jelölőnégyzetre kattintott.

Ha manuálisan kell létrehoznia egy felhasználót, kövesse az alábbi lépéseket:

1. Jelentkezzen be az iLMS vállalati webhelyére rendszergazdaként.

2. Kattintson **a Felhasználó regisztrálása** lap **Felhasználók** csoportjában a **Felhasználó regisztrálása** lap megnyitásához.

   ![Alkalmazott hozzáadása](./media/ilms-tutorial/3.png)

3. A **Felhasználó regisztrálása** lapon hajtsa végre a következő lépéseket.

    ![Alkalmazott hozzáadása](./media/ilms-tutorial/create_testuser_add.png)

    a. Az **Utónév** mezőbe írja be a Britta keresztnevet.

    b. A **Vezetéknév** mezőbe írja be a vezetéknevet, például Simon.

    c. Az **E-mail azonosító** mezőbe írja be a BrittaSimon@contoso.comfelhasználó e-mail címét a programhoz hasonlóan.

    d. A **Régió** legördülő menüben válassza ki a régió értékét.

    e. A **Divízió** legördülő menüben válassza ki a felosztás értékét.

    f. A **Részleg** legördülő menüben válassza ki a részleg értékét.

    g. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > A regisztrációs e-mailt a **Regisztrációs levél küldése** jelölőnégyzet bejelölésével küldheti el a felhasználónak.

### <a name="test-sso"></a>SSO tesztelése

Amikor a hozzáférési panelen kiválasztja az iLMS csempét, a rendszer automatikusan bejelentkezik azilem-rendszerbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
