---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a NetDocuments programmal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a NetDocuments között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1a47dc42-1a17-48a2-965e-eca4cfb2f197
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71e5e13485c4a10664d98363e8e99bfd3b4f4bcf
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "72035704"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-netdocuments"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a NetDocuments programmal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a NetDocuments dokumentumokat az Azure Active Directoryval (Azure AD). Ha integrálja a NetDocuments-t az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, hogy ki férhet hozzá a NetDocuments-hez.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve a NetDocuments az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* NetDocuments egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* A NetDocuments támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-netdocuments-from-the-gallery"></a>NetDocuments hozzáadása a gyűjteményből

A NetDocuments Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a netdocuments-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a **NetDocuments** kifejezést a keresőmezőbe.
1. Válassza a **NetDocuments** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-netdocuments"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése a NetDocuments szolgáltatáshoz

Konfigurálja és tesztelje az Azure AD SSO-t a NetDocuments segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a netdocuments kapcsolódó felhasználója között.

Az Azure AD SSO netdocuments-sel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja a NetDocuments egyszeri bejelentkezést](#configure-netdocuments-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[NetDocuments tesztfelhasználó létrehozása](#create-netdocuments-test-user)** – b.Simon megfelelője a NetDocuments-ben, amely a felhasználó Azure AD-ábrázolásához kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **NetDocuments** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://vault.netvoyage.com/neWeb2/docCent.aspx?whr=<Repository ID>`
    
    c. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:`http://netdocuments.com/VAULT`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges Bejelentkezési URL-címmel és a Válasz URL-címmel. A tárházazonosító egy **hitelesítés-hitelesítéssel** kezdődő érték, amelyet a NetDocuments adattárhoz társított 8 karakterből álló kód követ. További információkért tekintse meg a [NetDocuments összevont identitástámogatási dokumentumát.](https://support.netdocuments.com/hc/en-us/articles/205220410-Federated-Identity-Login) Másik lehetőségként lépjen kapcsolatba a [NetDocuments ügyféltámogatási csapatával,](https://support.netdocuments.com/hc/) hogy megkapja ezeket az értékeket, ha nehezen tudja konfigurálni a fenti információkat. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. A NetDocuments alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML token attribútumkonfigurációjához. A következő képernyőképen az alapértelmezett attribútumok listája látható, ahol a **nameidentifier** a **user.userprincipalname**. A NetDocuments alkalmazás azt várja, hogy a **névazonosító** le legyen képezve **az employeeid-val** vagy bármely más, a szervezetre **névazonosítóként**vonatkozó jogcímekkel, ezért az attribútumleképezést a **Szerkesztés** ikonra kattintva kell módosítania, és módosítania kell az attribútumleképezést.

    ![image](common/edit-attribute.png)

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **NetDocuments beállítása** csoportban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t a NetDocuments hozzáférést biztosítva a NetDocuments.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **NetDocuments**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-netdocuments-sso"></a>NetDocuments sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be a NetDocuments vállalati webhelyére rendszergazdaként.

2. Nyissa meg a **Rendszergazda**lehetőséget.

3. Kattintson **a Felhasználók és csoportok hozzáadása és eltávolítása gombra.**
   
    ![Repository](./media/netdocuments-tutorial/ic795047.png "Adattár") (Adattár)

4. Kattintson **a Speciális hitelesítési beállítások megadása gombra.**
    
    ![Speciális hitelesítési beállítások konfigurálása](./media/netdocuments-tutorial/ic795048.png "Speciális hitelesítési beállítások konfigurálása")

5. Az **Összevont identitás** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Federated Identity](./media/netdocuments-tutorial/ic795049.png "Federated Identity")
   
    a. Összevont **identitáskiszolgáló-típusként**válassza az **Active Directory összevonási szolgáltatások**lehetőséget.
   
    b. Kattintson **a Fájl kiválasztása**gombra a letöltött metaadatfájl Azure Portalról letöltött fájl feltöltéséhez.
   
    c. Kattintson az **OK** gombra.

### <a name="create-netdocuments-test-user"></a>NetDocuments tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezhessenek a NetDocuments szolgáltatásba, ki kell építeni őket a NetDocuments dokumentumaiba.  
A NetDocuments esetében a kiépítés manuális feladat.

**Felhasználói fiók kiépítéséhez hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **NetDocuments** vállalati webhelyére rendszergazdaként.

2. A felső menüben kattintson a **Rendszergazda gombra.**
   
    ![Felügyelet](./media/netdocuments-tutorial/ic795051.png "Rendszergazda")

3. Kattintson **a Felhasználók és csoportok hozzáadása és eltávolítása gombra.**
   
    ![Repository](./media/netdocuments-tutorial/ic795047.png "Adattár") (Adattár)

4. Az **E-mail cím** mezőbe írja be a kiépíteni kívánt érvényes Azure Active Directory-fiók e-mail címét, majd kattintson a **Felhasználó hozzáadása gombra.**
   
    ![E-mail cím](./media/netdocuments-tutorial/ic795053.png "E-mail-cím")
   
    >[!NOTE]
    >Az Azure Active Directory-fiók tulajdonosa kap egy e-mailt, amely tartalmaz egy hivatkozást, hogy erősítse meg a fiókot, mielőtt aktívvá válik. Az Azure Active Directory felhasználói fiókok kiépítéséhez a NetDocuments által biztosított bármely más NetDocuments felhasználói fiók-létrehozási eszközzel vagy API-val is használható.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a Hozzáférési panelEn a NetDocuments csempére kattint, automatikusan be kell jelentkeznie arra a NetDocuments dokumentumba, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki a NetDocuments szolgáltatást az Azure AD-vel](https://aad.portal.azure.com/)

