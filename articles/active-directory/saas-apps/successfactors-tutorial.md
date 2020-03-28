---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a SuccessFactors programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a SuccessFactors között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 32bd8898-c2d2-4aa7-8c46-f1f5c2aa05f1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d49915271580b5665981bf7e212f3d5712c86456
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76292979"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a SuccessFactors-szal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a SuccessFactors-ot az Azure Active Directoryval (Azure AD). Ha integrálja a SuccessFactors-ot az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a SuccessFactors.Control in Azure AD who has access to SuccessFactors.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve SuccessFactors az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* SuccessFactors egyszeri bejelentkezés (SSO) engedélyezve van előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A SuccessFactors támogatja az **SP** által kezdeményezett sso-t.
* A SuccessFactors konfigurálása után kényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlők a feltételes hozzáféréstől származnak. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="adding-successfactors-from-the-gallery"></a>SuccessFactors hozzáadása a galériából

A SuccessFactors Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a SuccessFactors-ot a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **SuccessFactors kifejezést** a keresőmezőbe.
1. Válassza a **SuccessFactors** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Az Azure AD-sSO konfigurálása és tesztelése a SuccessFactors számára

Konfigurálja és tesztelje az Azure AD SSO-t a SuccessFactors segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a SuccessFactors.For SSO to work, you need to establish a link relationship between a Azure AD user and the related user in SuccessFactors.

Az Azure AD SSO konfigurálásához és teszteléséhez a SuccessFactors segítségével hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
2. **[Konfigurálja a SuccessFactors Egyszeri bejelentkezést](#configure-successfactors-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre SuccessFactors teszt felhasználó](#create-successfactors-test-user)** - a B.Simon megfelelője a SuccessFactors, amely kapcsolódik az Azure AD felhasználói ábrázolása.
3. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **SuccessFactors** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `https://www.successfactors.com/<companyname>`|
    | `https://www.successfactors.com`|
    | `https://<companyname>.successfactors.eu`|
    | `https://www.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://hcm4preview.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.eu`|
    | `https://www.successfactors.cn`|
    | `https://www.successfactors.cn/<companyname>`|

    c. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.successfactors.com`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.sapsf.com`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.successfactors.eu`|
    | `https://<companyname>.sapsf.eu`|
    | `https://<companyname>.sapsf.eu/<companyname>`|
    | `https://<companyname>.sapsf.cn`|
    | `https://<companyname>.sapsf.cn/<companyname>`|

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Lépjen kapcsolatba [a SuccessFactors ügyféltámogatási csapatával,](https://www.successfactors.com/content/ssf-site/en/support.html) hogy megkapja ezeket az értékeket.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon keresse meg az **SAML aláíró tanúsítvány szakaszát,** keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **SuccessFactors beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a SuccessFactors hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **SuccessFactors lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-successfactors-sso"></a>A SuccessFactors sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a **SuccessFactors felügyeleti portálra** rendszergazdaként.

2. Látogasson el **az Alkalmazásbiztonság** és a natív **Egyszeri bejelentkezés funkció .**

3. Helyezzen el bármilyen értéket az **alaphelyzetbe állítási jogkivonatban,** és kattintson a **Jogkivonat mentése** gombra az SAML egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon][11]

    > [!NOTE]
    > Ez az érték lesz a be-/kikapcsoló gomb. Ha bármilyen értéket ment, az SAML SSO be van kapcsolva. Ha üres értéket ment, az SAML egyszeri hibajogosult nincs kikapcsolva.

4. Natív a képernyőkép alatt, és hajtsa végre a következő műveleteket:

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon][12]
  
    a. Válassza ki az **SAML v2 SSO** rádiógombot
  
    b. Állítsa be az **SAML asserting félnevét**(például SAML kibocsátó + vállalatnév).

    c. A **Kiállító URL-címmezőjébe** illessze be az **Azure AD-azonosító** értékét, amelyet az Azure Portalról másolt.

    d. Válassza a **Helyesség feltétel** **kötelező aláírást megkövetelőként**lehetőséget.

    e. Válassza **az Engedélyezve** **engedélyezése SAML jelzőként**lehetőséget.

    f. Válassza a **Nem** **lehetőséget bejelentkezési kérelem aláírásaként (SF generált/SP/RP)** lehetőséget.

    g. Válassza **a Böngésző/közzétételi profil** **saml-profilként lehetőséget.**

    h. Válassza a **Nem** lehetőséget **a tanúsítvány érvényességi időszakának érvényesítése ként.**

    i. Másolja a letöltött tanúsítványfájl tartalmát az Azure Portalról, majd illessze be az **SAML tanúsítvány ellenőrzése** szövegmezőbe.

    > [!NOTE] 
    > A tanúsítvány tartalmának meg kell kezdenie a tanúsítvány- és zárótanúsítvány-címkéket.

5. Nyissa meg az SAML V2-t, majd hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalon][13]

    a. Válassza az **Igen** **támogatási SP által kezdeményezett globális kijelentkezés lehetőséget.**

    b. A **Globális kijelentkezési szolgáltatás URL-cím (LogoutRequest cél)** szövegmezőbe illessze be a **kijelentkezési URL-értéket,** amelyet az Azure Portal on-t másolt.

    c. Válassza a **Nem** **kötelező sp kell titkosítani az összes NameID elem**.

    d. Válassza **a Meghatározatlan** **nevetadja meg a formátumot.**

    e. Válassza az **Igen** **lehetőséget sp kezdeményezett bejelentkezés engedélyezése (AuthnRequest)** lehetőséget.

    f. A **Küldési kérelem vállalati szintű kiállítóként** szövegmezőbe illessze be a **bejelentkezési URL-értéket,** amelyet az Azure Portalról másolt.

6. Hajtsa végre ezeket a lépéseket, ha azt szeretné, hogy a bejelentkezési felhasználónevek ne legyenek kinevezve.

    ![Egyszeri bejelentkezés konfigurálása][29]

    a. Látogassa meg **a Vállalati beállításokat**(alul).

    b. Jelölje be a Nem **kis- és nagybetűket megkülönböztető felhasználónév engedélyezése jelölőnégyzetet.**

    c. Kattintson a **Mentés** gombra.

    > [!NOTE]
    > Ha ezt megpróbálja engedélyezni, a rendszer ellenőrzi, hogy létrehoz-e egy duplikált SAML bejelentkezési nevet. Ha például az ügyfél felhasználóneve Felhasználó1 és felhasználó1. A kis- és nagybetűk megkülönböztetésének elvétele miatt ezek az ismétlődések ismétlődnek. A rendszer hibaüzenetet ad, és nem engedélyezi a szolgáltatást. Az ügyfélnek meg kell változtatnia az egyik felhasználónevet, hogy másként legyen beírva.

### <a name="create-successfactors-test-user"></a>SuccessFactors tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a SuccessFactors-ba, ki kell építeni őket a SuccessFactors-ba. SuccessFactors esetén kiépítése egy manuális feladat.

Ahhoz, hogy a felhasználók létre SuccessFactors, kapcsolatba kell lépnie a [SuccessFactors támogatási csapat](https://www.successfactors.com/content/ssf-site/en/support.html).

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a SuccessFactors csempére kattint, automatikusan be kell jelentkeznie azon SuccessFactors programba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a SuccessFactors szolgáltatást az Azure AD-vel](https://aad.portal.azure.com)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hogyan védhetjük meg a SuccessFactors-ot a fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
