---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Microsoft összefolyása SAML Egyszeri SSO-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Microsoft összefolyása SAML Egyszeri bejelentkezés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1ad1cf90-52bc-4b71-ab2b-9a5a1280fb2d
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/05/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e6ea52474f3b352fabf19050f20012438e9d0c19
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76120589"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-confluence-saml-sso-by-microsoft"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Microsoft összefolyása SAML Egyszeri SSO-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Microsoft összefolyása SAML SSO-t az Azure Active Directoryval (Azure AD). Ha integrálja a Microsoft saml-sso-ját az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Microsoft saml-sso-hoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve a confluence SAML SSO a Microsoft az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="description"></a>Leírás:

Az Atlassian Confluence kiszolgálóval rendelkező Microsoft Azure Active Directory-fiókjával engedélyezheti az egyszeri bejelentkezést. Így a szervezet összes felhasználója használhatja az Azure AD hitelesítő adatokat a Confluence alkalmazásba való bejelentkezéshez. Ez a plugin saml 2.0-t használ az összevonáshoz.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálásához a Microsoft SAML SSO-val a következő elemekre van szüksége:

- Azure AD-előfizetés
- A Windows 64 bites kiszolgálóra telepített összefolyási kiszolgálóalkalmazás (a helyszínen vagy a felhőbeli IaaS-infrastruktúrán)
- A összefolyási kiszolgáló HTTPS-kompatibilis
- Vegye figyelembe, hogy a Confluence Plugin támogatott verzióit az alábbi szakasz ban említi.
- A összefolyási kiszolgáló elérhető az interneten, különösen az Azure AD bejelentkezési lapjára a hitelesítéshez, és képesnek kell lennie a token fogadására az Azure AD-től
- A rendszergazdai hitelesítő adatok összefolyása alatt vannak beállítva
- A WebSudo le van tiltva az összefolyáskorban
- A Confluence kiszolgálóalkalmazásban létrehozott tesztfelhasználó

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem javasoljuk az összefolyás éles környezetének használatát. Tesztelje az integrációt először az alkalmazás fejlesztési vagy átmeneti környezetében, majd használja az éles környezetet.

A kezdéshez a következő elemekre van szükség:

* Ne használja az éles környezetet, kivéve, ha ez szükséges.
* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* SamL SSO összefolyása a Microsoft egyszeri bejelentkezéssel (SSO) engedélyezett előfizetéssel.

## <a name="supported-versions-of-confluence"></a>Az összefolyás támogatott verziói

Jelenleg a confluence következő verziói támogatottak:

- Összefolyás: 5,0-5,10
- Összefolyás: 6.0.1-6.15.9
- Összefolyás: 7.0.1-7.1.0

> [!NOTE]
> Kérjük, vegye figyelembe, hogy a mi összefolyásplugin is működik ubuntu verzió 16.04

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Összefolyássamlúzó SAML SSO a Microsoft támogatja **SP** kezdeményezett SSO

## <a name="adding-confluence-saml-sso-by-microsoft-from-the-gallery"></a>Összefolyássamlés SAML SSO hozzáadása a Microsofttól a galériából

A Microsoft összefolyása SAML SSO-nak az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Microsoft összefolyása SAML SSO-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a katalógusból szakaszban** írja be a Microsoft **összefolyása SAML SSO-t** a keresőmezőbe.
1. Válassza **a Microsoft Összefolyása SAML SSO lehetőséget** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-confluence-saml-sso-by-microsoft"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Microsoft összefolyása SAML Egyszeri sSO-hoz

Konfigurálja és tesztelje az Azure AD SSO-t az SAML SSO összefolyással a Microsoft által egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Microsoft saml-sso összefolyása során.

Az Azure AD SSO konfigurálásához és teszteléséhez a Microsoft SAML SSO-jával, hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Microsoft SSO összefolyása SAML Egyszeri bejelentkezés –](#configure-confluence-saml-sso-by-microsoft-sso)** az alkalmazás oldalon az egyszeri bejelentkezési beállítások konfigurálásához.
    1. **[Hozzon létre összefolyású SAML SSO a Microsoft teszt felhasználó](#create-confluence-saml-sso-by-microsoft-test-user)** - egy megfelelője B.Simon a Confluence SAML SSO a Microsoft, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Confluence SAML SSO by Microsoft** alkalmazásintegrációs lapon keresse meg a **Kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<domain:port>/plugins/servlet/saml/auth`

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<domain:port>/`

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<domain:port>/plugins/servlet/saml/auth`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Port nem kötelező abban az esetben, ha egy elnevezett URL-t. Ezek az értékek a Confluence plugin konfigurációja során érkeznek, amelyet az oktatóanyag későbbi részében ismertetünk.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t enged hozzá a Microsoft összefolyása SAML Egyszeri SSO használatával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza **a Microsoft Összefolyása SAML SSO**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-confluence-saml-sso-by-microsoft-sso"></a>Összefolyási SAML SSO konfigurálása a Microsoft SSO-val

1. Egy másik böngészőablakban jelentkezzen be az összefolyási példányba rendszergazdaként.

1. Mutasson a fogaskerékre, és kattintson **a bővítményekre.**

    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/addon1.png)

1. Töltse le a bővítményt a [Microsoft letöltőközpontjából.](https://www.microsoft.com/download/details.aspx?id=56503) Manuálisan töltse fel a Microsoft által biztosított bővítményt **az Upload add-on** menüben. A bővítmény letöltését a [Microsoft szolgáltatási szerződése](https://www.microsoft.com/servicesagreement/)fedezi.

    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/addon12.png)

1. A Összefolyás fordított proxy forgatókönyv vagy a terheléselosztó forgatókönyv futtatásához hajtsa végre a következő lépéseket:

    > [!NOTE]
    > Először a kiszolgálót kell konfigurálnia az alábbi utasításokkal, majd telepítenie kell a bővítményt.

    a. Adja hozzá az alábbi attribútumot a JIRA kiszolgálóalkalmazás **server.xml** fájljának **összekötő** portjában.

    `scheme="https" proxyName="<subdomain.domain.com>" proxyPort="<proxy_port>" secure="true"`

    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/reverseproxy1.png)

    b. Módosítsa az **alap URL-címet** a **Rendszerbeállítások** ban a proxy/terheléselosztó szerint.

    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/reverseproxy2.png)

1. Miután a plugin telepítve van, úgy tűnik, a **Felhasználó által telepített** bővítmények szakasz kezelése **Add-on** szakasz. Az új bővítmény konfigurálásához kattintson a **Konfigurálás** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/addon15.png)

1. Hajtsa végre a következő lépéseket a konfigurációs lapon:

    ![Egyszeri bejelentkezés konfigurálása](./media/confluencemicrosoft-tutorial/addon54.png)

    > [!TIP]
    > Győződjön meg arról, hogy csak egy tanúsítvány van leképezve az alkalmazáshoz, hogy ne legyen hiba a metaadatok feloldása. Ha több tanúsítvány van, a rendszergazda hibaüzenetet kap a metaadatok feloldásakor.

    1. A **Metaadatok URL-címmezőjében** illessze be **az Alkalmazásösszevonás metaadat-URL-címét,** amelyet az Azure Portalról másolt, és kattintson a **Feloldás gombra.** Beolvassa az IdP metaadat-URL-címét, és feltölti az összes mezőinformációt.

    1. Másolja az **azonosítót, a válasz URL-címét és a Bejelentkezés az URL-értékeket,** és illessze be őket **az Azonosító, a Válasz URL-cím és a Bejelentkezés url-címbe** az Azure Portal **alapszintű SAML-konfigurációs** szakaszában.

    1. A **Bejelentkezési gomb neve** mezőbe írja be annak a gombnak a nevét, amelyet a szervezet a felhasználóknak látnia kell a bejelentkezési képernyőn.
    
    1. A **Bejelentkezési gomb leírása mezőbe** írja be annak a gombnak a leírását, amelyet a szervezet a felhasználóknak látnia kell a bejelentkezési képernyőn.

    1. Az **SAML felhasználói azonosító helyek között**válassza ki azt a felhasználói **azonosítót, amely a Tárgy utasítás NameIdentifier elemében található,** vagy a **Felhasználói azonosító attribútumelemben található.**  Ennek az azonosítónak kell lennie a összefolyás felhasználói azonosítónak. Ha a felhasználói azonosító nem egyezik, akkor a rendszer nem engedélyezi a felhasználók számára a bejelentkezést. 

       > [!Note]
       > Az alapértelmezett SAML-felhasználóazonosító helye a névazonosító. Ezt attribútumbeállításra módosíthatja, és megadhatja a megfelelő attribútumnevet.

    1. Ha a **Felhasználói azonosító attribútumelem-beállításban lehetőséget választja,** akkor az **Attribútumnév** szövegmezőbe írja be annak az attribútumnak a nevét, ahol a felhasználói azonosító várható. 

    1. Ha az összevont tartományt (például Az ADFS stb.) használja az Azure AD-vel, kattintson a **Home Realm Discovery engedélyezése** lehetőségre, és konfigurálja a **tartománynevet**.

    1. A **Tartománynév** mezőbe írja be a tartománynevet ide az ADFS-alapú bejelentkezés esetén.

    1. Jelölje be **az Egyszeri kijelentkezés engedélyezése** jelölőnégyzetet, ha ki szeretne jelentkezni az Azure AD-ből, amikor egy felhasználó kijelentkezik a Folyásból. 

    1. Engedélyezze **az Azure-bejelentkezés kényszerítése** jelölőnégyzetet, ha csak az Azure AD hitelesítő adataival szeretne bejelentkezni.

       > [!Note]
       > Ha engedélyezni szeretné az alapértelmezett bejelentkezési űrlapot a bejelentkezési lapon a bejelentkezési lapon, amikor a force azure bejelentkezés engedélyezve van, adja hozzá a lekérdezési paramétert a böngésző URL-címéhez.
       > `https://<domain:port>/login.action?force_azure_login=false`

    1. A beállítások mentéséhez kattintson a **Mentés** gombra.

       > [!NOTE]
       > A telepítéssel és a hibaelhárítással kapcsolatos további információkért látogasson el [az MS Confluence SSO Connector Admin Guide oldalra.](../ms-confluence-jira-plugin-adminguide.md) Van is egy [GYIK](../ms-confluence-jira-plugin-faq.md) a segítséget.

### <a name="create-confluence-saml-sso-by-microsoft-test-user"></a>Összefolyású SAML-sSO létrehozása a Microsoft tesztfelhasználója által

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a helyszíni kiszolgálóösszefolyási kiszolgálóra, a Microsoftnak ki kell építenie őket a Confluence SAML SSO-ba. A Microsoft összefolyása SAML SSO, kiépítése egy manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a helyszíni kiszolgáló összefolyása rendszergazdaként.

1. Mutasson a fogaskerékre, és kattintson a **Felhasználókezelés parancsra.**

    ![Alkalmazott hozzáadása](./media/confluencemicrosoft-tutorial/user1.png)

1. A Felhasználók csoportban kattintson a **Felhasználók hozzáadása** fülre. A **Felhasználó hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Alkalmazott hozzáadása](./media/confluencemicrosoft-tutorial/user2.png)

    a. A **Felhasználónév** mezőbe írja be a felhasználó e-mail címét, például B.Simon.

    b. A **Teljes név** mezőbe írja be a felhasználó teljes nevét, például B.Simon.

    c. Az **E-mail** mezőbe írja be a B.Simon@contoso.comfelhasználó e-mail címét, például .

    d. A **Jelszó** mezőbe írja be B.Simon jelszavát.

    e. Kattintson **a Jelszó megerősítése** gombra, írja be újra a jelszót.

    f. Kattintson **a Hozzáadás** gombra.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a Hozzáférési panelen a Microsoft által létrehozott SAML-sso csempére kattint, automatikusan be kell jelentkeznie a Microsoft összefolyása SAML-sso-ba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Microsoft összefolyása SAML SSO-t az Azure AD-vel](https://aad.portal.azure.com/)
