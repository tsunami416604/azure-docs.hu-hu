---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Freshservice szolgáltatással | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Freshservice között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0e08ef72dca09f873ad1cfcc91e132063b88406b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74227534"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-freshservice"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Freshservice szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Freshservice szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja a Freshservice szolgáltatást az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Freshservice.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve freshservice az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Freshservice egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Freshservice támogatja **SP** kezdeményezett SSO

## <a name="adding-freshservice-from-the-gallery"></a>Freshservice hozzáadása a galériából

A Freshservice azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Freshservice-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a katalógusból szakaszban** írja be a **Freshservice** kifejezést a keresőmezőbe.
1. Válassza **a Freshservice** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-freshservice"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a Freshservice szolgáltatáshoz

Konfigurálja és tesztelje az Azure AD SSO-t freshservice szolgáltatással egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó freshservice.

Az Azure AD SSO freshservice szolgáltatással való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[A Freshservice Egyszeri bejelentkezés konfigurálása](#configure-freshservice-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Freshservice teszt felhasználó](#create-freshservice-test-user)** létrehozása – b.Simon a Freshservice, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Freshservice** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza ki **az egyszeri bejelentkezést.**
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://<democompany>.freshservice.com`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<democompany>.freshservice.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Lépjen kapcsolatba [a Freshservice ügyfél támogatási csapatával,](https://support.freshservice.com/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A Freshservice használatához SHA-256 ujjlenyomat szükséges az Egyszeri szolgáltatás működéséhez. Az SHA-256 ujjlenyomat levételéhez hajtsa végre az alábbi lépéseket:

    ![Ujjlenyomat](./media/freshservice-tutorial/ic790821.png "Ujjlenyomat")

    1. Nyissa meg a [hivatkozást](https://www.samltool.com/fingerprint.php) különböző böngészőben.

    1. Nyissa meg a letöltött tanúsítványfájlt (Base64) a Jegyzettömbben, és illessze be a tartalmat az **X.509 tanúsítvány** szövegmezőbe.

    1. Az algoritmushoz válassza az **sha256** elemet a legördülő menüből.

    1. Kattintson **az UJJLENYOMAT KISZÁMÍTÁSA gombra.**

    1. Kattintson a másolás ikonra a generált **Ujjlenyomat** másolásához és a számítógépre mentéséhez.

1. A **Freshservice beállítása** szakaszban az **Azure Portalon**másolja a megfelelő URL-cím(ek) a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés használatával a Freshservice hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Freshservice lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-freshservice-sso"></a>Freshservice sso konfigurálása

1. A Freshservice konfigurációjának automatizálásához telepítenie kell a **My Apps Secure Sign-in böngészőbővítményt** **a Bővítmény telepítése**gombra kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzábővítményt a böngészőhöz, kattintson a **Setup Freshservice** irányítja, hogy a Freshservice alkalmazás. Innen adja meg a rendszergazdai hitelesítő adatokat a Freshservice-be való bejelentkezéshez. A böngésző bővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-6.

    ![Beállítási konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Freshservice szolgáltatást, nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Freshservice vállalati webhelyére, és hajtsa végre a következő lépéseket:

4. A felső menüben kattintson a **Rendszergazda gombra.**

    ![Felügyelet](./media/freshservice-tutorial/ic790814.png "Rendszergazda")

5. Az **ügyfélportálon**kattintson a **Biztonság**gombra.

    ![Biztonság](./media/freshservice-tutorial/ic790815.png "Biztonság")

6. A **Biztonság** szakaszban hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés](./media/freshservice-tutorial/ic790816.png "Egyszeri bejelentkezés")

    a. Kapcsolja be **az egyszeri bejelentkezést**.

    b. Válassza **az SAML SSO**lehetőséget.

    c. Az **SAML bejelentkezési URL-cím** szövegmezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.

    d. A **Kijelentkezés URL-cím** mezőjébe illessze be a **Kijelentkezés URL-címének**értékét, amelyet az Azure Portalról másolt.

    e. A **Biztonsági tanúsítvány ujjlenyomat** szövegdobozában illessze be a korábban létrehozott **Ujjlenyomat** értéket.

    f. Kattintson a **Mentés gombra**

### <a name="create-freshservice-test-user"></a>Freshservice tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a FreshService szolgáltatásba, ki kell építeni őket a FreshService szolgáltatásba. FreshService esetén kiépítése manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **FreshService** vállalati webhelyére rendszergazdaként.

2. A felső menüben kattintson a **Rendszergazda gombra.**

    ![Felügyelet](./media/freshservice-tutorial/ic790814.png "Rendszergazda")

3. A **Felhasználókezelés** csoportban kattintson a **Kérelmezők gombra.**

    ![Kérelmezők](./media/freshservice-tutorial/ic790818.png "Kérelmezők")

4. Kattintson **az Új kérelmező gombra.**

    ![Új kérelmezők](./media/freshservice-tutorial/ic790819.png "Új kérelmezők")

5. Az **Új kérelmező** csoportban hajtsa végre az alábbi lépéseket:

    ![Új kérelmező](./media/freshservice-tutorial/ic790820.png "Új kérelmező")  

    a. Adja meg egy érvényes Azure Active Directory-fiók **első név** és **e-mail** attribútumait, amelyet a kapcsolódó szövegdobozokba szeretne kiépíteni.

    b. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > Az Azure Active Directory-fiók tulajdonosa e-mailt kap, amely a fiók aktívvá váválasztása előtt egy hivatkozást is megad.
    >  

> [!NOTE]
> Az Azure AD felhasználói fiókok kiépítéséhez használhatja bármely más FreshService felhasználói fiók létrehozó eszközeit vagy API-kat a FreshService által biztosított.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panel Freshservice csempéjére kattint, automatikusan be kell jelentkeznie arra a Freshservice szolgáltatásba, amelyhez beállítja az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Freshservice szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)