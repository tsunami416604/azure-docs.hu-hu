---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az OpsGenie-vel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az OpsGenie között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 41b59b22-a61d-4fe6-ab0d-6c3991d1375f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 03/19/2020
ms.author: jeedes
ms.openlocfilehash: 8cf82827258b3888f4c30ad39a395d697a518a32
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81261098"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-opsgenie"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az OpsGenie-vel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az OpsGenie-t az Azure Active Directoryval (Azure AD). Ha integrálja az OpsGenie-t az Azure AD-vel, a következőket teheti:

* Az Azure AD-ben, aki hozzáfér opsgenie.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezve legyenek az OpsGenie-be az Azure AD-fiókjukkal.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* OpsGenie egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az OpsGenie támogatja az **IDP** által kezdeményezett SSO-t
* Az OpsGenie konfigurálása után kényszerítheti a munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlő a feltételes hozzáféréstől terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással.](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app)

## <a name="adding-opsgenie-from-the-gallery"></a>OpsGenie hozzáadása a galériából

Az OpsGenie azure AD-be való integrációjának konfigurálásához hozzá kell adnia az OpsGenie-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be az **OpsGenie** kifejezést a keresőmezőbe.
1. Válassza az **OpsGenie** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-opsgenie"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése az OpsGenie számára

Konfigurálja és tesztelje az Azure AD SSO-t az OpsGenie-vel egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó opsgenie között.

Az Azure AD SSO konfigurálásához és teszteléséhez az OpsGenie segítségével hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja OpsGenie SSO](#configure-opsgenie-sso)** -konfigurálása az egyszeri bejelentkezési beállításokat az alkalmazás oldalán.
    * **[Hozzon létre OpsGenie teszt felhasználó](#create-opsgenie-test-user)** - egy megfelelője B.Simon opsgenie, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **OpsGenie** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza ki **az egyszeri bejelentkezést.**
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban hajtsa végre az alábbi lépéseket:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://app.opsginie.com/auth/saml/<UNIQUEID>`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://app.opsginie.com/auth/saml?id=<UNIQUEID>`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel, amelyet az oktatóanyag későbbi részében ismertetünk.

5. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány szakaszában** kattintson a Másolás gombra az **Alkalmazásösszevonás metaadat-címének** másolásához és mentéséhez a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

1. Az **OpsGenie beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés használatával hozzáférést opsgenie.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **OpsGenie**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-opsgenie-sso"></a>OpsGenie SSO konfigurálása

1. Nyisson meg egy másik böngészőpéldányt, majd jelentkezzen be rendszergazdaként az OpsGenie-be.

2. Kattintson a **Beállítások**gombra, majd az **Egyszeri bejelentkezés lapra.**
   
    ![OpsGenie egyszeri bejelentkezés](./media/opsgenie-tutorial/tutorial-opsgenie-06.png)

3. Az SSO engedélyezéséhez válassza az **Engedélyezve**lehetőséget.
   
    ![OpsGenie beállítások](./media/opsgenie-tutorial/tutorial-opsgenie-07.png) 

4. A **Szolgáltató** csoportban kattintson az **Azure Active Directory** fülre.
   
    ![OpsGenie beállítások](./media/opsgenie-tutorial/tutorial-opsgenie-08.png) 

5. Az Azure Active Directory párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![OpsGenie beállítások](./media/opsgenie-tutorial/tutorial-opsgenie-09.png)
    
    a. Másolja az **Alkalmazásazonosító URI-értékét,** és illessze be **az Azonosító (entitásazonosító)** szövegmezőbe az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában.

    a. Másolja a **válasz URL-értékét,** és illessze be a **Válasz URL-cím** szövegmezőjébe az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában.

    a. Az **SAML 2.0 Végpont** szövegdobozába illessze be a **bejelentkezési URL-cím**értéket, amelyet az Azure Portalról másolt.
    
    b. A **Metaadat-url-cím:** szövegmezőbe illessze be **az Alkalmazásösszevonás metaadat-URL-címét,** amelyet az Azure Portalról másolt.
    
    c. Az Egyszeri bejelentkezés engedélyezéséhez kapcsolja be az **Egyszeri bejelentkezés engedélyezése** kapcsolót.

    d. Kattintson **az SSO-beállítások alkalmazása gombra.**

### <a name="create-opsgenie-test-user"></a>OpsGenie tesztfelhasználó létrehozása

A szakasz célja, hogy hozzon létre egy b.Simon nevű felhasználó opsgenie. 

1. Egy webböngésző ablakban jelentkezzen be opsgenie-bérlő, mint rendszergazda.

2. Keresse meg a Felhasználók listát a Bal oldali panel **Felhasználók** elemére kattintva.
   
    ![OpsGenie beállítások](./media/opsgenie-tutorial/tutorial-opsgenie-10.png) 

3. Kattintson az **Add User** (Felhasználó hozzáadása) elemre.

4. A **Felhasználó hozzáadása** párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![OpsGenie beállítások](./media/opsgenie-tutorial/tutorial-opsgenie-11.png)
   
    a. Az **E-mail** szövegmezőbe írja be az Azure Active Directoryban címzett B.Simon e-mail címét.
   
    b. A **Teljes név** mezőbe írja be a **B.Simon**nevet.
   
    c. Kattintson a **Mentés** gombra. 

> [!NOTE]
> B.Simon kap egy e-mailt a profilbeállításra vonatkozó utasításokkal.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor az OpsGenie csempére kattint a hozzáférési panelen, automatikusan be kell jelentkeznie az OpsGenie-be, amelyhez beállította az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az OpsGenie-t az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)