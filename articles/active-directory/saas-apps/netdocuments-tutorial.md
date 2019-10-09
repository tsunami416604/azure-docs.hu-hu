---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a NetDocuments | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és NetDocuments között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71e5e13485c4a10664d98363e8e99bfd3b4f4bcf
ms.sourcegitcommit: f9e81b39693206b824e40d7657d0466246aadd6e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/08/2019
ms.locfileid: "72035704"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a NetDocuments

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a NetDocuments a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az NetDocuments-t az Azure AD-vel, a következőket teheti:

* A NetDocuments-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a NetDocuments az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* NetDocuments egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A NetDocuments támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-netdocuments-from-the-gallery"></a>NetDocuments hozzáadása a gyűjteményből

A NetDocuments Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a NetDocuments a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **NetDocuments** kifejezést a keresőmezőbe.
1. Válassza ki a **NetDocuments** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netdocuments"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a NetDocuments

Konfigurálja és tesztelje az Azure AD SSO-t a NetDocuments a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a NetDocuments-ben.

Az Azure AD SSO és a NetDocuments konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. **[NETDOCUMENTS SSO konfigurálása](#configure-netdocuments-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre NetDocuments-teszt felhasználót](#create-netdocuments-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-NetDocuments rendelkezik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **NetDocuments** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. A **Válasz URL-címe** szövegmezőbe írja be a következő mintát használó URL-címet: `https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`
    
    c. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával: `http://netdocuments.com/VAULT`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és a válasz URL-címével. A tárház-azonosító egy olyan érték, amely a NetDocuments-tárházhoz tartozó, a **CA-** tól kezdődően 8 karakterből áll. További információért tekintse meg a [NetDocuments összevont identitás támogatási dokumentumát](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) . Ha nehézségekbe ütközik a fenti információk használatával, vegye fel a kapcsolatot a NetDocuments ügyfélszolgálati [csapatával](https://support.netdocuments.com/hc/) is. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A NetDocuments alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőfelvételen az alapértelmezett attribútumok listája látható, ahol a **NameIdentifier** a **User. userPrincipalName**leképezéssel van leképezve. A NetDocuments alkalmazás az **AlkalmazottKód** -mel vagy bármely más, a szervezete számára a **NameIdentifier**-ként érvényes **NameIdentifier** rendeli hozzá az attribútum-hozzárendelést, ezért a **Szerkesztés** ikonra kattintva módosítania kell az attribútumok leképezését. és módosítsa az attribútumok leképezését.

    ![image](common/edit-attribute.png)

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. A **NetDocuments beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension értéket. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a NetDocuments.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **NetDocuments**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-netdocuments-sso"></a>NetDocuments SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a NetDocuments vállalati webhelyre rendszergazdaként.

2. Nyissa meg a **rendszergazdát**.

3. Kattintson **a felhasználók és csoportok hozzáadása és eltávolítása**elemre.
   
    ![Adattár]-(./media/netdocuments-tutorial/ic795047.png "adattár")

4. Kattintson a **speciális hitelesítési beállítások konfigurálása lehetőségre**.
    
    ![Speciális hitelesítési beállítások konfigurálása](./media/netdocuments-tutorial/ic795048.png "speciális hitelesítési beállítások konfigurálása")

5. Az **Összevont identitás** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    Összevont ![identitás](./media/netdocuments-tutorial/ic795049.png "összevont identitása")
   
    a. **Összevont identitás-kiszolgáló típusaként**válassza a **Active Directory összevonási szolgáltatások (AD FS)** lehetőséget.
   
    b. Kattintson a **Fájl választása**gombra, és töltse fel a letöltött metaadat-fájlt, amelyet a Azure Portal letöltött.
   
    c. Kattintson az **OK** gombra.

### <a name="create-netdocuments-test-user"></a>NetDocuments-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a NetDocuments, a NetDocuments kell kiépíteni őket.  
NetDocuments esetén a kiépítés manuális feladat.

**Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:**

1. Jelentkezzen be a **NetDocuments** vállalati webhelyre rendszergazdaként.

2. A felső menüben kattintson a **rendszergazda**elemre.
   
    ![Rendszergazdai](./media/netdocuments-tutorial/ic795051.png "rendszergazda")

3. Kattintson **a felhasználók és csoportok hozzáadása és eltávolítása**elemre.
   
    ![Adattár]-(./media/netdocuments-tutorial/ic795047.png "adattár")

4. Az **E-mail cím** szövegmezőbe írja be a kiépíteni kívánt érvényes Azure Active Directory fiók e-mail-címét, majd kattintson a **felhasználó hozzáadása**elemre.
   
    ![E-mail cím](./media/netdocuments-tutorial/ic795053.png "e-mail-címe")
   
    >[!NOTE]
    >A Azure Active Directory fiók tulajdonosa egy e-mailt fog kapni, amely tartalmaz egy hivatkozást, amely megerősíti a fiókot, mielőtt az aktívvá válna. A NetDocuments által biztosított egyéb NetDocuments-létrehozási eszközöket vagy API-kat használhatja Azure Active Directory felhasználói fiókok kiépítéséhez.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a NetDocuments csempére kattint, automatikusan be kell jelentkeznie arra a NetDocuments, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A NetDocuments kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

