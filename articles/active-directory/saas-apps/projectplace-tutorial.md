---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Projectplace-vel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Projectplace között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 298059ca-b652-4577-916a-c31393d53d7a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/29/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 179721075484c35c5ebbb3d936b83bc407b75a8d
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "67093523"
---
# <a name="tutorial-integrate-projectplace-with-azure-active-directory"></a>Oktatóanyag: A Projectplace integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a ProjectPlace-t az Azure Active Directoryval (Azure AD). Ha integrálja a Projectplace-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, hogy ki férhet hozzá a Projectplace-hez.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve projectplace az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.
* A felhasználók automatikusan kiépíthetők a Projectplace-ben.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Projectplace egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. A Projectplace támogatja **az SP és az IDP** által kezdeményezett egyszeri bejelentkezést, és támogatja a Just In **Time** felhasználói kiépítést.

## <a name="adding-projectplace-from-the-gallery"></a>Projekthely hozzáadása a gyűjteményből

A Projectplace Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a ProjectPlace-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Gyűjtemény Hozzáadás szakaszába** írja be a **Projekthely kifejezést** a keresőmezőbe.
1. Válassza a **Projectplace** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a ProjectPlace-vel egy **B. Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a projectplace kapcsolódó felhasználója között.

Az Azure AD SSO projectplace-vel történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD SSO-t,](#configure-azure-ad-sso)** hogy a felhasználók használhassák ezt a funkciót.
2. **[Konfigurálja a Projectplace-t](#configure-projectplace)** az sso-beállítások alkalmazásoldali konfigurálásához.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user)** az Azure AD egyszeri bejelentkezésének teszteléséhez B. Simonnal.
4. **[Rendelje hozzá az Azure AD tesztfelhasználót,](#assign-the-azure-ad-test-user)** hogy b. Simon az Azure AD egyszeri bejelentkezést.
5. **[Projectplace tesztfelhasználó](#create-projectplace-test-user)** létrehozása, hogy egy megfelelője B. Simon a Projectplace, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Tesztelje az SSO-t,](#test-sso)** hogy ellenőrizze, működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Projectplace** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszerű SAML-konfiguráció** szakaszban, ha szeretné konfigurálni az alkalmazást **IDP** kezdeményezett módban, az alkalmazás előre konfigurált, és a szükséges URL-címek már előre kitöltött az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Mentés** gombra kattintva.

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet:`https://service.projectplace.com`

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportban** kattintson a másolás **ikonra** az **Alkalmazásösszevonás metaadat-url-címének**másolásához , a követelménynek megfelelően, és mentse azt a Jegyzettömbbe.

   ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

1. A **Projectplace beállítása szakaszban** másolja a megfelelő URL-cím(eke)t a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-projectplace"></a>Projekthely konfigurálása

Az egyszeri bejelentkezés konfigurálásához a **Projectplace** oldalán el kell küldenie a másolt **App Federation metaadat-URL-címet** az Azure Portalról a [Projectplace támogatási csapatának.](https://success.planview.com/Projectplace/Support) Ez a csapat biztosítja, hogy az SAML SSO-kapcsolat mindkét oldalon megfelelően legyen beállítva.

>[!NOTE]
>Az egyszeri bejelentkezési konfigurációt a [Projectplace támogatási csapatának kell elvégeznie.](https://success.planview.com/Projectplace/Support) Értesítést kap, amint a konfiguráció befejeződött. 

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B. Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B. Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi B. Simon azure egyszeri bejelentkezés t a Projectplace hozzáférést biztosítva.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Projectplace lehetőséget.**
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B. Simon** elemet a Felhasználók listában, majd kattintson a kijelölés gombra a képernyő alján. **Select**
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-projectplace-test-user"></a>Projectplace tesztfelhasználó létrehozása

>[!NOTE]
>Ezt a lépést kihagyhatja, ha engedélyezve van a kiépítés a Projectplace-ben. Megkérheti a [Projectplace támogatási csapatát,](https://success.planview.com/Projectplace/Support) hogy engedélyezze a kijelentkezést, miután elkészült a felhasználók a Projectplace-ben az első bejelentkezés során.

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a Projectplace-be, hozzá kell adnia őket a Projectplace-hez. Manuálisan kell hozzáadnia őket.

**Felhasználói fiók létrehozásához tegye a következőket:**

1. Jelentkezzen be a **Projectplace** vállalati webhelyére rendszergazdaként.

2. Nyissa meg a **Személyek**lehetőséget, és válassza a **Tagok**lehetőséget:
   
    ![Nyissa meg a Kapcsolatok lehetőséget, és válassza a Tagok lehetőséget](./media/projectplace-tutorial/ic790228.png "People")

3. Válassza **a Tag hozzáadása**lehetőséget:
   
    ![Tag hozzáadása kiválasztása](./media/projectplace-tutorial/ic790232.png "Tagok hozzáadása")

4. A **Tag hozzáadása** szakaszban tegye a következő lépéseket.
   
    ![Tag hozzáadása szakasz](./media/projectplace-tutorial/ic790233.png "Új tagok")
   
    1. Az **Új tagok** mezőbe írja be a hozzáadni kívánt érvényes Azure AD-fiók e-mail címét.
   
    1. Válassza a **Küldés**lehetőséget.

   Egy e-mailt, amely egy linket, hogy erősítse meg a fiókot, mielőtt aktívvá válik az Azure AD-fiók tulajdonosa.

>[!NOTE]
>A Projectplace által biztosított bármely más felhasználói fiók-létrehozási eszközt vagy API-t is használhat az Azure AD felhasználói fiókok hozzáadásához.


### <a name="test-sso"></a>SSO tesztelése

Amikor a Projectplace csempét választja a Hozzáférési panelen, a rendszer automatikusan bejelentkezik arra a projekthelyre, amelyre az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)