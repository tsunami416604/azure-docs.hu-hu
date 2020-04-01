---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az Absztrakttal | Microsoft dokumentumok'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az Abstract között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb167ab-d769-4661-a8cb-ae371cb63d2a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 81886fa9165269d89bde8306c5829be41952c190
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68302590"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Oktatóanyag: Az Absztrakt integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az Abstractt az Azure Active Directoryval (Azure AD). Ha integrálja az Abstractt az Azure AD-vel, a következőket teheti:

* Az Azure AD- ban, aki hozzáfér az Absztrakt.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve absztrakt az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Absztrakt egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az absztrakt támogatja az **SP-t és az IDP** által kezdeményezett SSO-t

## <a name="adding-abstract-from-the-gallery"></a>Absztrakt hozzáadása a galériából

Az Absztrakt Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az Abstract a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **gyűjtemény Hozzáadás szakaszába** írja be az **Absztrakt** kifejezést a keresőmezőbe.
1. Válassza az **Absztrakt** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t az Absztrakt tal egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az Absztrakt ban.

Az Azure AD SSO konfigurálásához és teszteléséhez az Absztrakt szolgáltatással hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az absztrakt egyszeri bejelentkezést](#configure-abstract-sso)** - az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-tesztfelhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezésének teszteléséhez Britta Simonnal.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi Britta Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre absztrakt tesztfelhasználót](#create-abstract-test-user)** – ha a felhasználó Azure AD-megjelenítéséhez kapcsolódó, Absztrakt ban britsimon megfelelője van.
6. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **Absztrakt** alkalmazásintegráció lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszerű SAML konfigurációs** szakaszban az alkalmazás előre konfigurált **IDP** kezdeményezett módban, és a szükséges URL-címek már előre kitöltött az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Mentés** gombra kattintva.

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet:`https://app.abstract.com/signin`

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Absztrakt sso konfigurálása

Győződjön meg `App Federation Metadata Url` arról, hogy lekéri a és az `Azure AD Identifier` az Azure Portalon, mivel szüksége lesz az Okra az Absztrakt sso konfigurálásához.

Ezeket az információkat az **Egységes bejelentkezés beállítása SAML-lel** lapon találja:

* Az `App Federation Metadata Url` az **SAML aláíró tanúsítvány** szakaszban található.
* Az `Azure AD Identifier` az **Absztrakt beállítása** szakaszban található.


Most már készen áll az SSO konfigurálására az Absztrakton:

>[!Note]
>Az Absztrakt SSO-beállításainak eléréséhez hitelesítést kell biztosítania egy szervezeti rendszergazdai fiókkal.

1. Nyissa meg az [Absztrakt webalkalmazást](https://app.abstract.com/).
2. Nyissa meg az **Engedélyek** lapot a bal oldali sávban.
3. Az **SSO konfigurálása** csoportban adja meg a **metaadat URL-címét** és **az entitásazonosítót.**
4. Adja meg a manuális kivételeket. A manuális kivételek részben felsorolt e-mailek megkerülik az SSO-t, és képesek lesznek e-mailben és jelszóval bejelentkezni. 
5. Kattintson a **Save Changes** (Módosítások mentése) gombra.

>[!Note] 
>Az elsődleges e-mail címeket a manuális kivételek listájában kell használnia. Az SSO aktiválása sikertelen lesz, ha a lista a felhasználó másodlagos e-mail címe. Ebben az esetben megjelenik egy hibaüzenet a hibás fiók elsődleges e-mailcímével. Adja hozzá az elsődleges e-mailt a manuális kivételek listájához, miután ellenőrizte, hogy ismeri a felhasználót.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával hozzáférést biztosít az Absztrakt.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **Absztrakt**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-abstract-test-user"></a>Absztrakt tesztfelhasználó létrehozása

Az SSO tesztelése absztrakton:

1. Nyissa meg az [Absztrakt webalkalmazást](https://app.abstract.com/).
2. Nyissa meg az **Engedélyek** lapot a bal oldali sávban.
3. Kattintson **a Teszt a fiókommal lehetőségre.** Ha a teszt nem sikerül, kérjük, [forduljon ügyfélszolgálatunkhoz](https://www.abstract.com/help/contact/).

>[!Note]
>Az Absztrakt SSO-beállításainak eléréséhez hitelesítést kell biztosítania egy szervezeti rendszergazdai fiókkal.
Ezt a szervezeti rendszergazdai fiókot az Azure Portalon az Absztrakt hoz kell hozzárendelni.

### <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelEn az Absztrakt csempére kattint, automatikusan be kell jelentkeznie az SSO beállításához tartozó Absztrakt ba. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

