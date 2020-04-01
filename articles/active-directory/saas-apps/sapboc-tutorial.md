---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az SAP Analytics-felhővel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az SAP Analytics Cloud között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 6c5e44f0-4e52-463f-b879-834d80a55cdf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/17/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1fd01c86086c7491d22f655fcba5da237286412f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "68347798"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Oktatóanyag: Az SAP Analytics-felhő integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az SAP Analytics-felhőt az Azure Active Directoryval (Azure AD). Ha integrálja az SAP Analytics-felhőt az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az SAP Analytics Cloud szolgáltatáshoz.
* Lehetővé teszi, hogy a felhasználók automatikusan bejelentkezve az SAP Analytics Cloud az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* AZ SAP Analytics Cloud egyszeri bejelentkezéssel (SSO) engedélyezett előfizetéssel rendelkezik.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az SAP Analytics Cloud támogatja az **SP** által kezdeményezett SSO-t

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>AZ SAP Analytics-felhő hozzáadása a galériából

Az SAP Analytics-felhő Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SAP Analytics-felhőt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a katalógusból** szakaszban írja be az **SAP Analytics-felhőt** a keresőmezőbe.
1. Válassza az **SAP Analytics Cloud** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t az SAP Analytics Cloud segítségével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az SAP Analytics-felhőben.

Az Azure AD SSO SAP Analytics-felhővel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
2. **[Konfigurálja az SAP Analytics Cloud SSO-t](#configure-sap-analytics-cloud-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
3. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
5. **[Hozzon létre SAP Analytics Cloud-tesztfelhasználót](#create-sap-analytics-cloud-test-user)** – hogy b.Simon megfelelője legyen az SAP Analytics-felhőben, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
6. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **SAP Analytics Cloud-alkalmazások** integrációs lapján keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A Bejelentkezés az **URL-cím** mezőbe írja be az URL-címet a következő minta használatával:

    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. Az **Azonosító (entitásazonosító)** mezőbe írjon be egy URL-címet a következő minta használatával:
    
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Az url-címekben lévő értékek csak szemléltetésre szolgálnak. Frissítse az értékeket a tényleges bejelentkezési URL-címmel és azonosító URL-címével. A bejelentkezési URL-cím lefelvételéhez lépjen kapcsolatba az [SAP Analytics felhőbeli ügyféltámogatási csapatával.](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/) Az azonosító URL-címét az SAP Analytics felhőmetaadatainak letöltésével szerezheti be a felügyeleti konzolról. Ez később a bemutató.

4. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadatok XML-jét,** és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre való mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. Az **SAP Analytics-felhő beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>SAP Analytics felhőalapú sso konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az SAP Analytics Cloud vállalati webhelyére rendszergazdaként.

2. Válassza **a Menürendszer** > **System** > **felügyelete**lehetőséget.
    
    ![Válassza a Menü, majd a Rendszer, majd a Felügyelet lehetőséget](./media/sapboc-tutorial/config1.png)

3. A **Biztonság** lapon kattintson a **Szerkesztés** (toll) ikonra.
    
    ![A Biztonság lapon kattintson a Szerkesztés ikonra](./media/sapboc-tutorial/config2.png)  

4. **A hitelesítési módszerhez**válassza az **SAML Single Sign-On (SSO)** lehetőséget.

    ![Válassza az SAML Single Sign-On lehetőséget a hitelesítési módszerhez](./media/sapboc-tutorial/config3.png)  

5. A szolgáltató metaadatainak letöltéséhez (1. lépés) válassza a **Letöltés**lehetőséget. A metaadatfájlban keresse meg és másolja az **entitásazonosító** értékét. Az Azure Portalon az **Egyszerű SAML konfiguráció párbeszédpanelen** illessze be az értéket az **azonosító** mezőbe.

    ![Az entitásazonosító érték másolása és beillesztése](./media/sapboc-tutorial/config4.png)  

6. Ha fel szeretné tölteni a szolgáltató metaadatait (2. lépés) az Azure Portalról letöltött fájlba, az **Identitásszolgáltató metaadatainak feltöltése**csoportban válassza a **Feltöltés lehetőséget.**  

    ![Az Identitásszolgáltató feltöltési metaadatai csoportban válassza a Feltöltés lehetőséget.](./media/sapboc-tutorial/config5.png)

7. A **Felhasználói attribútum** listában jelölje ki a megvalósításhoz használni kívánt felhasználói attribútumot (3. lépés). Ez a felhasználói attribútum leképezi az identitásszolgáltatónak. Ha egyéni attribútumot szeretne megadni a felhasználó lapján, használja az **Egyéni SAML-hozzárendelés beállítást.** Vagy kiválaszthatja **az E-mail** vagy **a FELHASZNÁLÓI azonosítót** felhasználói attribútumként. A példában az **E-mail** lehetőséget választottuk, mert a felhasználói azonosító jogcímet az Azure Portal **Felhasználói attribútumok & jogcímek** szakaszában a **user attributes** & jogcímek attribútummal képeztük le. Ez egy egyedi felhasználói e-mailt biztosít, amelyet minden sikeres SAML-válaszban elküld a rendszer az SAP Analytics Cloud alkalmazásnak.

    ![Felhasználói attribútum kiválasztása](./media/sapboc-tutorial/config6.png)

8. Ha ellenőrizni szeretné a fiókot az identitásszolgáltatónál (4. lépés), a **Bejelentkezési hitelesítő adatok (e-mail)** mezőbe írja be a felhasználó e-mail címét. Ezután válassza **a Fiók ellenőrzése**lehetőséget. A rendszer bejelentkezési hitelesítő adatokat ad hozzá a felhasználói fiókhoz.

    ![Adja meg a levelezést, és válassza a Fiók ellenőrzése lehetőséget.](./media/sapboc-tutorial/config7.png)

9. Kattintson a **Mentés** ikonra.

    ![Mentés ikon](./media/sapboc-tutorial/save.png)

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

Ebben a szakaszban lehetővé teszi b.Simon azure egyszeri bejelentkezés az SAP Analytics-felhőhöz való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **SAP Analytics Cloud**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

### <a name="create-sap-analytics-cloud-test-user"></a>SAP Analytics Cloud-tesztfelhasználó létrehozása

Az Azure AD-felhasználókat ki kell építeni az SAP Analytics-felhőben, mielőtt bejelentkezhetnek az SAP Analytics-felhőbe. Az SAP Analytics-felhőben a kiépítés manuális feladat.

Felhasználói fiók kiépítése:

1. Jelentkezzen be az SAP Analytics Cloud vállalati webhelyére rendszergazdaként.

2. Válassza **a Menü** > **biztonsági** > **felhasználói lehetőséget.**

    ![Alkalmazott hozzáadása](./media/sapboc-tutorial/user1.png)

3. A **Felhasználók** lapon új felhasználói adatok **+** at szeretne megadni, válassza a lehetőséget. 

    ![Felhasználók hozzáadása lap](./media/sapboc-tutorial/user4.png)

    Ezután hajtsa végre a következő lépéseket:

    a. A **USER ID** mezőbe írja be a felhasználó felhasználói azonosítóját, például **B**.

    b. A **FIRST NAME** mezőbe írja be a felhasználó keresztnevét, például **B**.

    c. A **VEZETÉKNÉV** mezőbe írja be a felhasználó vezetéknevét, például **Simon**.

    d. A **MEGJELENÍTENDŐ NÉV** mezőbe írja be a felhasználó teljes nevét, például **B.Simon**.

    e. Az **E-MAIL** mezőbe írja be a felhasználó `b.simon@contoso.com`e-mail címét, például .

    f. A **Szerepkörök kiválasztása** lapon válassza ki a felhasználónak megfelelő szerepkört, majd kattintson az **OK gombra.**

      ![Szerepkör kiválasztása](./media/sapboc-tutorial/user3.png)

    g. Kattintson a **Mentés** ikonra.

### <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor az SAP Analytics Felhő csempére kattint a Hozzáférési panelen, automatikusan be kell jelentkeznie az SAP Analytics-felhőbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

