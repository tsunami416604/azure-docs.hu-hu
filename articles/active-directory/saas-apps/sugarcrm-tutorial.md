---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Sugar CRM rendszerrel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Sugar CRM között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 3331b9fc-ebc0-4a3a-9f7b-bf20ee35d180
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: fae7b80fd4d2fcec32bbef5e4cdf18e576412a86
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "74231980"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sugar-crm"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Sugar CRM-mel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Sugar CRM-et az Azure Active Directoryval (Azure AD). Ha integrálja a Sugar CRM-et az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Sugar CRM-hez.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve sugar CRM az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Sugar CRM egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Sugar CRM támogatja **SP** kezdeményezett SSO

> [!NOTE]
> Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egy bérlőben.

## <a name="adding-sugar-crm-from-the-gallery"></a>Sugar CRM hozzáadása a galériából

A Sugar CRM azure AD-be való integrálásának konfigurálásához hozzá kell adnia a sugar CRM-et a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **Hozzáadás a gyűjteményből szakaszban** írja be a **Sugar CRM kifejezést** a keresőmezőbe.
1. Válassza **a Sugar CRM** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-sugar-crm"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Sugar CRM-hez

Konfigurálja és tesztelje az Azure AD SSO-t a Sugar CRM-mel egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Sugar CRM-ben.

Az Azure AD SSO konfigurálásához és teszteléséhez a Sugar CRM-el hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Sugar CRM Egyszeri bejelentkezést](#configure-sugar-crm-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre sugar CRM teszt felhasználó](#create-sugar-crm-test-user)** - a B.Simon megfelelője a Sugar CRM, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Sugar CRM-alkalmazásintegrációs** lapon keresse meg a Kezelés **szakaszt,** és válassza az egyszeri **bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `https://<companyname>.sugarondemand.com`|
    | `https://<companyname>.trial.sugarcrm`|

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:

    | |
    |--|
    | `https://<companyname>.sugarondemand.com/<companyname>`|
    | `https://<companyname>.trial.sugarcrm.com/<companyname>`|
    | `https://<companyname>.trial.sugarcrm.eu/<companyname>`|

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és a válasz URL-címével. Lépjen kapcsolatba [a Sugar CRM ügyfél támogatási csapatával,](https://support.sugarcrm.com/) hogy megkapja ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány** szakaszában keresse meg a **Tanúsítvány (Base64)** lehetőséget, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **Sugar CRM beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a Sugar CRM-hez való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Sugar CRM**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-sugar-crm-sso"></a>Cukor CRM sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a Sugar CRM vállalati webhelyére rendszergazdaként.

1. Nyissa meg a **Rendszergazda**lehetőséget.

    ![Felügyelet](./media/sugarcrm-tutorial/ic795888.png "Rendszergazda")

1. A **Felügyelet** csoportban kattintson a **Jelszókezelés gombra.**

    ![Felügyelet](./media/sugarcrm-tutorial/ic795889.png "Adminisztráció")

1. Válassza **az SAML-hitelesítés engedélyezése**lehetőséget.

    ![Felügyelet](./media/sugarcrm-tutorial/ic795890.png "Adminisztráció")

1. Az **SAML-hitelesítés** szakaszban hajtsa végre az alábbi lépéseket:

    ![SAML-hitelesítés](./media/sugarcrm-tutorial/ic795891.png "SAML-hitelesítés")  

    a. A **Bejelentkezési URL-cím** mezőbe illessze be a **bejelentkezési URL-cím**értékét, amelyet az Azure Portalról másolt.
  
    b. Az **SLO URL-cím** szövegmezőjébe illessze be a **Kijelentkezés URL-címének**értékét, amelyet az Azure Portalról másolt.
  
    c. Nyissa meg az alap-64 kódolású tanúsítványt a jegyzettömbben, másolja annak tartalmát a vágólapra, majd illessze be a teljes tanúsítványt az **X.509 tanúsítvány** szövegmezőbe.
  
    d. Kattintson a **Mentés** gombra.

### <a name="create-sugar-crm-test-user"></a>Sugar CRM-tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek a Sugar CRM-be, ki kell őket építeni a Sugar CRM-be. A Sugar CRM esetében a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be **a Sugar CRM** vállalat webhelyére rendszergazdaként.

1. Nyissa meg a **Rendszergazda**lehetőséget.

    ![Felügyelet](./media/sugarcrm-tutorial/ic795888.png "Rendszergazda")

1. A **Felügyelet** csoportban kattintson a **Felhasználókezelés gombra.**

    ![Felügyelet](./media/sugarcrm-tutorial/ic795893.png "Adminisztráció")

1. Nyissa meg a **Felhasználók \> új felhasználó létrehozása**lehetőséget.

    ![Új felhasználó létrehozása](./media/sugarcrm-tutorial/ic795894.png "Új felhasználó létrehozása")

1. A **Felhasználói profil** lapon hajtsa végre az alábbi lépéseket:

    ![Új felhasználó](./media/sugarcrm-tutorial/ic795895.png "Új felhasználó")

    * Írja be egy érvényes Azure Active Directory-felhasználó **felhasználónevét**, **vezetéknevét**és **e-mail címét** a kapcsolódó szövegdobozokba.
  
1. **Állapot ként**válassza az **Aktív**lehetőséget.

1. A Jelszó lapon hajtsa végre az alábbi lépéseket:

    ![Új felhasználó](./media/sugarcrm-tutorial/ic795896.png "Új felhasználó")

    a. Írja be a jelszót a kapcsolódó szövegmezőbe.

    b. Kattintson a **Mentés** gombra.

> [!NOTE]
> A Sugar CRM felhasználói fiók létrehozásához használt bármely más, a Sugar CRM által biztosított API-k at azure AD felhasználói fiókok kiépítéséhez használhatja.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen a Sugar CRM csempére kattint, a rendszer automatikusan bejelentkezik arra a Sugar CRM rendszerbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Sugar CRM-et az Azure AD-vel](https://aad.portal.azure.com/)

