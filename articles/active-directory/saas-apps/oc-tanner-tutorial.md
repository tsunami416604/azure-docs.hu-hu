---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az O.C.-vel. Tanner - AppreciateHub | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az O.C között. Tanner - AppreciateHub.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: dee8fbca-0b60-4a21-8917-1fb6919de5a0
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 01/28/2020
ms.author: jeedes
ms.openlocfilehash: 768756e013a4301ea11f5f9478178bdad46f9d67
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76983996"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-oc-tanner---appreciatehub"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az O.C.Tutorial: Azure Active Directory single sign-on (SSO) integráció o.c. Tanner – AppreciateHub

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az O.C.-t. Tanner – AppreciateHub az Azure Active Directory (Azure AD). Amikor integrálja o.c. Tanner - AppreciateHub az Azure AD, akkor:

* Az Azure AD-ben, aki hozzáfér o.c. Tanner - AppreciateHub.
* A felhasználók automatikus bejelentkezése a o.c.-be. Tanner – AppreciateHub az Azure AD-fiókokkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* O.C. Tanner – AppreciateHub egyszeri bejelentkezés (SSO) engedélyezve van az előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* O.C. Tanner - A AppreciateHub támogatja az **IDP** által kezdeményezett Egyszeri erőforrást

* Miután beállította az O.C.-t. Tanner – A AppreciateHub valós időben kényszerítheti a munkamenet-vezérlőket, amelyek védik a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlők a feltételes hozzáféréstől származnak. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-oc-tanner---appreciatehub-from-the-gallery"></a>O.C. hozzáadása Tanner - AppreciateHub a galériából

Az O.C. integrációjának konfigurálása. Tanner - AppreciateHub az Azure AD, hozzá kell adnia O.C. Tanner - AppreciateHub a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a katalógusból szakaszban** írja be **o.c. Tanner - AppreciateHub** a keresőmezőbe.
1. Válassza **az O.C. Tanner - AppreciateHub** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-oc-tanner---appreciatehub"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezéso.Configure and test Azure AD single sign-on for O.C. Tanner – AppreciateHub

Konfigurálja és tesztelje az Azure AD SSO-t az O.C.-vel. Tanner - AppreciateHub egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó o.c. Tanner - AppreciateHub.

Az Azure AD SSO konfigurálása és tesztelése az O.C.To configure and test Azure AD SSO with O.C. Tanner - AppreciateHub, töltse ki a következő építőelemek:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja az O.C. Tanner - AppreciateHub SSO](#configure-oc-tanner---appreciatehub-sso)** - konfigurálása az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
    * **[Hozzon létre O.C. Tanner - AppreciateHub teszt felhasználó](#create-oc-tanner---appreciatehub-test-user)** -, hogy egy megfelelője B.Simon O.C. Tanner – AppreciateHub, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **O.C. Tanner – AppreciateHub** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban a felhasználónak nem kell végrehajtania semmilyen lépést, mivel az alkalmazás már előre integrálva van az Azure-ral.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra, ha letöltheti az **összevonási metaadat-XML-t** a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **O.C. Tanner – AppreciateHub beállítása** szakaszon másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Hirdetés-azonosító

    c. Kijelentkezés URL-címe

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t, hozzáférést biztosítva o.c. Tanner - AppreciateHub.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza **az O.C. Tanner - AppreciateHub**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-oc-tanner---appreciatehub-sso"></a>O.C. konfigurálása Tanner - AppreciateHub SSO

Az **O.C. Tanner – AppreciateHub** oldalon történő egyszeri bejelentkezés konfigurálásához el kell küldenie a letöltött **összevonási metaadat-XML-t** és a megfelelő másolt URL-címeket az Azure Portalról [az O.C. Tanner - AppreciateHub támogatási csapatnak.](mailto:sso@octanner.com) Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-oc-tanner---appreciatehub-test-user"></a>O.C. létrehozása Tanner - AppreciateHub teszt felhasználó

Ennek a szakasznak az a célja, hogy létrehozzon egy Britta Simon nevű felhasználót o.c.-ben. Tanner - AppreciateHub.

**Britta Simon nevű felhasználó létrehozásához az O.C. Tanner - AppreciateHub alkalmazásban hajtsa végre a következő lépéseket:**

Kérje meg az [O.C. Tanner – AppreciateHub támogatási csapatát,](mailto:sso@octanner.com) hogy hozzon létre egy olyan felhasználót, amely nek neveID-attribútuma megegyezik a Britta Simon felhasználónevével az Azure AD-ben.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor rákattint a O.C. Tanner – AppreciateHub csempe a hozzáférési panelen, akkor automatikusan be kell jelentkeznie az O.C. Tanner - AppreciateHub, amelyre beállította sso. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az O.C. Tannert – a AppreciateHub az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Hogyan védhetjük o.c. Tanner - AppreciateHub fejlett láthatóság és ellenőrzések](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)