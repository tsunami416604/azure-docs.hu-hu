---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SuccessFactors | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és SuccessFactors között.
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
ms.devlang: na
ms.topic: tutorial
ms.date: 08/13/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 38d40a2f72e73dde0f99ebbc9701e02c8d03738b
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68989494"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-successfactors"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a SuccessFactors

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a SuccessFactors a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az SuccessFactors-t az Azure AD-vel, a következőket teheti:

* A SuccessFactors-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a SuccessFactors az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SuccessFactors egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A SuccessFactors támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-successfactors-from-the-gallery"></a>SuccessFactors hozzáadása a gyűjteményből

A SuccessFactors Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a SuccessFactors a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **SuccessFactors** kifejezést a keresőmezőbe.
1. Válassza ki a **SuccessFactors** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-sso-for-successfactors"></a>Azure AD SSO konfigurálása és tesztelése a SuccessFactors-hez

Konfigurálja és tesztelje az Azure AD SSO-t a SuccessFactors a **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a SuccessFactors-ben.

Az Azure AD SSO és a SuccessFactors konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. **[SUCCESSFACTORS SSO konfigurálása](#configure-successfactors-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre SuccessFactors-teszt felhasználót](#create-successfactors-test-user)** – ha a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-SuccessFactors rendelkezik.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/) **SuccessFactors** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az alapszintű **SAML-konfiguráció** szakaszban hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával:

    | |
    |--|
    | `https://<companyname>.successfactors.com/<companyname>`|
    | `https://<companyname>.sapsf.com/<companyname>`|
    | `https://<companyname>.successfactors.eu/<companyname>`|
    | `https://<companyname>.sapsf.eu`|

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe:

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

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:

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
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel, azonosítóval és válasz URL-címmel. Az értékek lekéréséhez forduljon a [SuccessFactors](https://www.successfactors.com/content/ssf-site/en/support.html) ügyfélszolgálati csapatához.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

6. A **SuccessFactors beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
    1. A **Név** mezőbe írja a következőt: `B.Simon`.  
    1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
    1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
    1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a SuccessFactors.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **SuccessFactors**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-successfactors-sso"></a>SuccessFactors SSO konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a **SuccessFactors felügyeleti portálra** rendszergazdaként.

2. Látogasson el az **alkalmazás biztonságára** és a natív az **egyszeri bejelentkezés funkcióra**.

3. Helyezzen be bármilyen értéket az alaphelyzetbe állítási tokenbe, és kattintson a **jogkivonat mentése** lehetőségre az SAML SSO engedélyezéséhez.

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán][11]

    > [!NOTE]
    > Ezt az értéket használja be-és kikapcsoló kapcsolóként. Ha a rendszer bármilyen értéket ment, az SAML egyszeri bejelentkezés be van kapcsolva. Ha a rendszer üres értéket ment, az SAML SSO ki van kapcsolva.

4. A lenti képernyőre, és hajtsa végre a következő műveleteket:

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán][12]
  
    a. Válassza ki az **SAML v2 SSO** választógombot
  
    b. Állítsa be az **SAML-érvényesítő fél nevét**(például SAML-kiállító + vállalat neve).

    c. A **kiállító URL-címe** szövegmezőbe illessze be a Azure Portalból másolt **Azure ad-azonosító** értékét.

    d. Válassza az érvényesítés **kötelező aláírás**megkövetelése lehetőséget.

    e. Válassza az **engedélyezve** lehetőséget az **SAML-jelző engedélyezése**lehetőséggel.

    f. Válassza a nem **bejelentkezési kérelem aláírása lehetőséget (SF által generált/SP/RP)** .

    g. Válassza ki a **Browser/post profilt** **SAML**-profilként.

    h. Válassza a nem **kényszerített tanúsítvány érvényességi időtartamát**.

    i. Másolja a letöltött tanúsítványfájl tartalmát a Azure Portalból, majd illessze be az **SAML-ellenőrzési tanúsítvány** szövegmezőbe.

    > [!NOTE] 
    > A tanúsítvány tartalmának meg kell kezdődnie a tanúsítvány és a záró tanúsítvány címkéit.

5. Navigáljon az SAML v2-re, majd hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása az alkalmazás oldalán][13]

    a. Válassza az **Igen** lehetőséget az **SP által kezdeményezett globális kijelentkezés támogatásához**.

    b. A **globális kijelentkezési szolgáltatás URL-címe (LogoutRequest-cél)** szövegmezőbe illessze be azt a kijelentkezési **URL-címet** , amelyet a Azure Portal másolt.

    c. Ha a **nem** lehetőséget választja, az **SP-nek titkosítania kell az összes NameID elemet**.

    d. Válassza ki a **meghatározatlan értéket** **NameID formátumban**.

    e. Válassza az **Igen** lehetőséget az **SP által kezdeményezett bejelentkezés engedélyezése (AuthnRequest)** beállításnál.

    f. A **küldési kérelem vállalati szintű kiállítóként** szövegmezőbe illessze be a **bejelentkezési URL-cím** értékét, amelyet a Azure Portal másolt.

6. Hajtsa végre ezeket a lépéseket, ha a bejelentkezési felhasználónevek kis-és nagybetűk megkülönböztetését szeretné elvégezni.

    ![Egyszeri bejelentkezés konfigurálása][29]

    a. Látogasson el a **Vállalati beállítások**oldalra (az alsó sarokban).

    b. Válassza a jelölőnégyzetet a **nem kis-és nagybetűket megkülönböztető Felhasználónév engedélyezése**mellett.

    c. Kattintson a **Save** (Mentés) gombra.

    > [!NOTE]
    > Ha megpróbálja engedélyezni ezt, a rendszer ellenőrzi, hogy létrehoz-e ismétlődő SAML-bejelentkezési nevet. Például ha az ügyfél rendelkezik a Felhasználó1 és a Felhasználó1 felhasználónévvel. A kis-és nagybetűk megkülönböztetése az ismétlődéseket is lehetővé teszi. A rendszer hibaüzenetet küld, és nem engedélyezi a szolgáltatást. Az ügyfélnek meg kell változtatnia az egyik felhasználónevet, hogy az a különböző módon legyen elnevezve.

### <a name="create-successfactors-test-user"></a>SuccessFactors-tesztelési felhasználó létrehozása

Annak engedélyezéséhez, hogy az Azure AD-felhasználók bejelentkezzenek a SuccessFactors, a SuccessFactors kell kiépíteni őket. SuccessFactors esetén a kiépítés manuális feladat.

A SuccessFactors-ben létrehozott felhasználók beszerzéséhez kapcsolatba kell lépnie a [SuccessFactors-támogatási csapattal](https://www.successfactors.com/content/ssf-site/en/support.html).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SuccessFactors csempére kattint, automatikusan be kell jelentkeznie arra a SuccessFactors, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A SuccessFactors kipróbálása az Azure AD-vel](https://aad.portal.azure.com)

<!--Image references-->

[11]: ./media/successfactors-tutorial/tutorial_successfactors_07.png
[12]: ./media/successfactors-tutorial/tutorial_successfactors_08.png
[13]: ./media/successfactors-tutorial/tutorial_successfactors_09.png
[29]: ./media/successfactors-tutorial/tutorial_successfactors_10.png
