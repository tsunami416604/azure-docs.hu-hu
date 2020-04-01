---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Concur Travel and Expense | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Concur Travel and Expense között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6401ace4-71c0-4778-8b15-900f2f5f0c4c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 03/10/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9dddd9f6904aa5ef7840850792aeabf04666dddc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72373414"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-concur-travel-and-expense"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Concur Travel and Expense szolgáltatással

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Concur Travel and Expense szolgáltatást az Azure Active Directoryval (Azure AD). Ha integrálja a Concur Travel and Expense-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér a Concur Travel and Expense.Control in Azure AD who has access to Concur Travel and Expense.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve concur utazási és költség az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* Concur Utazási és költség-előfizetés.
* A "Vállalati rendszergazda" szerepkör a Concur felhasználói fiók alatt. Tesztelheti, hogy rendelkezik-e a megfelelő hozzáféréssel, ha a [Concur SSO Önkiszolgáló eszközhöz](https://www.concursolutions.com/nui/authadmin/ssoadmin)megy. Ha nem rendelkezik hozzáféréssel, kérjük, forduljon a Concur támogatási vagy megvalósítási projektmenedzseréhez. 

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t.

* A Concur Travel and Expense támogatja az **IDP-t** és **az SP** által kezdeményezett sso-t
* A Concur Travel and Expense támogatja az SSO tesztelését mind az éles, mind a megvalósítási környezetben 

> [!NOTE]
> Az alkalmazás azonosítója rögzített karakterlánc-érték mindhárom régióban: USA, EMEA és Kína. Így csak egy példány konfigurálható minden régióban egy bérlőben. 

## <a name="adding-concur-travel-and-expense-from-the-gallery"></a>A Concur Travel és a Expense hozzáadása a galériából

A Concur Travel and Expense azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Concur Travel and Expense-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **Concur Travel and Expense kifejezést** a keresőmezőbe.
1. Válassza **az Utazás és költség egyeztetése** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-concur-travel-and-expense"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését a Concur Travel and Expense számára

Konfigurálja és tesztelje az Azure AD SSO-t a Concur Travel and Expense segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Concur Travel and Expense.For SSO to work, you need to establish a link relationship between a Azure AD user and the related user in Concur Travel and Expense.

Az Azure AD SSO konfigurálásához és teszteléséhez a Concur Travel and Expense segítségével hajtsa végre az alábbi építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a Concur Travel and Expense Egyszeri bejelentkezést](#configure-concur-travel-and-expense-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre Concur Travel and Expense teszt felhasználó](#create-concur-travel-and-expense-test-user)** - egy megfelelője B.Simon a Concur Travel and Expense, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)keresse meg az **Utazás és költség** alkalmazás integrációja lap A **Kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszerű SAML konfigurációs** szakaszban az alkalmazás előre konfigurált **IDP** kezdeményezett módban, és a szükséges URL-címek már előre kitöltött az Azure-ban. A felhasználónak mentenie kell a konfigurációt a **Mentés** gombra kattintva.

    > [!NOTE]
    > Az azonosító (entitásazonosító) és a válasz URL-címe (helyességi feltétel fogyasztói szolgáltatás URL-címe) régióspecifikus. Válassza ki a Concur entitás adatközpontja alapján. Ha nem ismeri a Concur entitás adatközpontját, forduljon a Concur ügyfélszolgálatához. 

5. A **Beállítások beállítása SAML-lel** lapon kattintson a **Felhasználói attribútum** szerkesztése/tollikonjára a beállítások szerkesztéséhez. Az egyedi felhasználói azonosítónak meg kell egyeznie a Concur felhasználói login_id. Általában meg kell változtatni **user.userprincipalname** **a user.mail**.

    ![Felhasználói attribútum szerkesztése](common/edit-attribute.png)

6. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportban** keresse meg az **összevonási metaadat-XML-t,** és válassza a **Letöltés** lehetőséget a metaadatok letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés t engedve a Concur Utazási és költség.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **Utazás és költség egyeztetése**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-concur-travel-and-expense-sso"></a>Az utazási és költség-sso konfigurálása

1. Az egyszeri bejelentkezés konfigurálásához a **Concur Travel and Expense** oldalon fel kell töltenie a letöltött **összevonási metaadat-XML-t** a [Concur Egyszeri bejelentkezés önkiszolgáló eszközbe,](https://www.concursolutions.com/nui/authadmin/ssoadmin) és be kell jelentkeznie egy "Vállalati rendszergazda" szerepkörrel rendelkező fiókkal. 

1. Kattintson a **Hozzáadás** gombra.
1. Adjon meg egy egyéni nevet az IdP-nek, például "Azure AD (US)". 
1. Kattintson **az XML-fájl feltöltése gombra,** és csatolja a korábban letöltött **összevonási metaadat-XML-t.**
1. A módosítás mentéséhez kattintson a **Metaadatok hozzáadása gombra.**

    ![Concur SSO önkiszolgáló eszköz képernyőkép](./media/concur-travel-and-expense-tutorial/add-metadata-concur-self-service-tool.png)

### <a name="create-concur-travel-and-expense-test-user"></a>Concur Utazási és kiadási tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy B.Simon nevű felhasználót az Utazás és költség egybeesése során. Együttműködve Concur támogatási csapatával a felhasználók hozzáadása a Concur Utazási és költségplatformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. 

> [!NOTE]
> B.Simon Concur bejelentkezési azonosítójának meg kell egyeznie B.Simon egyedi azonosítójával az Azure AD-ben. Ha például B.Simon Azure AD-s egyedi `B.Simon@contoso.com`azonosítója a. B.Simon Concur bejelentkezési azonosítójának `B.Simon@contoso.com` is meg kell, hogy legyen. 

## <a name="configure-concur-mobile-sso"></a>A Concur Mobile SSO konfigurálása
A Concur mobil sso engedélyezéséhez meg kell adnia a Concur támogatási csoport **felhasználói hozzáférési URL-címét.** Az alábbi lépéseket követve kaphat **felhasználói hozzáférési URL-címet** az Azure AD-től:
1. Ugrás a **vállalati alkalmazásokra**
1. Kattintson **az Utazás és költség egyeztetése gombra**
1. Kattintson **a Tulajdonságok gombra**
1. Felhasználói **hozzáférés URL-címének másolása** és az URL-cím megírása a Concur támogatásnak

> [!NOTE]
> Az Egyszeri szolgáltató konfigurálásának önkiszolgáló lehetősége nem érhető el, ezért a Concur támogatási csapatával együttműködve engedélyezze a mobil egyszeri szolgáltatást. 

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelen az Utazás és költség egyeztetése csempére kattint, automatikusan be kell jelentkeznie az SSO beállításához tartozó Egybeesés i. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a Concur Travel and Expense szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)

