---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Mail Luck! | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Mail Luck!között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 783ea511-0b02-4455-ac8c-7aefde4ae4df
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: daf86270224d2a76eb124e879594ac2588b46776
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72969818"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-mail-luck"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Mail Luck!

Ebben a tutorial, megtudhatja, hogyan kell integrálni Mail Luck! az Azure Active Directory (Azure AD) használatával. Ha integrálja Mail Luck! az Azure AD-vel a következőket teheti:

* Szabályozhatja az Azure AD-t, aki hozzáfér a Mail Luck!-hoz.
* Tegye lehetővé, hogy a felhasználók automatikusan bejelentkezhessenek a Mail Luckba! az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Mail Luck! egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Mail Luck! támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-mail-luck-from-the-gallery"></a>Hozzáadása Mail Luck! a galériából

Konfigurálása integrációja Mail Luck! az Azure AD-be, hozzá kell adnia a Mail Luck-ot! a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a galéria szakaszban** írja be a **Mail Luck!** a keresőmezőbe.
1. Válassza mail **szerencse!** az eredménypanelről, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-mail-luck"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Mail Luck számára!

Konfigurálja és tesztelje az Azure AD SSO-t a Mail Luck segítségével! **b.simon**nevű tesztfelhasználót használ. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolatkapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Mail Luck!.For SSO to work, you need to establish a link relationship between a Azure AD user and the related user in Mail Luck!.

Az Azure AD SSO konfigurálásához és teszteléséhez a Mail Luck!-val hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja Mail Luck! Egyszeri bejelentkezés](#configure-mail-luck-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Create Mail Luck! teszt felhasználó](#create-mail-luck-test-user)** -, hogy egy megfelelője B.Simon a Mail Luck! amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure portalon](https://portal.azure.com/), a **Mail Luck!** alkalmazásintegrációs lapon keresse meg a **Manage szakaszt,** és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://manage<UNITID>.ml-sgw.jp/<TENANT_NAME>/saml/sign_in`

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`https://manage<UNITID>.ml-sgw.jp/<TENANT_NAME>/saml/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és azonosítóval. Kapcsolat [Mail Luck! Az ügyféltámogatási csapat](https://customer.nttpc.co.jp/cgi-bin/form/inquiry_index.cgi) ezeket az értékeket kapja. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Mail Luck!.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Mail Luck!**.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-mail-luck-sso"></a>Konfigurálja Mail Luck! SSO

Az egyszeri bejelentkezés beállítása a **Mail Luck!-ra!** oldalon, meg kell küldeni az **App Federation metaadatok URL-t** [Mail Luck! támogató csapat](https://customer.nttpc.co.jp/cgi-bin/form/inquiry_index.cgi). Úgy állították be ezt a beállítást, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

### <a name="create-mail-luck-test-user"></a>Create Mail Luck! teszt felhasználó

Ebben a szakaszban egy B.Simon nevű felhasználót hoz létre a Mail Luck!-ban. Működjön együtt [mail luck! támogatási csapat,](https://customer.nttpc.co.jp/cgi-bin/form/inquiry_index.cgi) hogy adjunk a felhasználók a Mail Luck! Platform. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>SSO tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha rákattint a Mail Luck! csempét a Hozzáférési panelen, akkor automatikusan be kell jelentkeznie a Mail Luck! amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja Mail Luck! az Azure AD-vel](https://aad.portal.azure.com/)