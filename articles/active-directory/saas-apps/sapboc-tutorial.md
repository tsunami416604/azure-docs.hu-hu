---
title: 'Oktatóanyag: Azure Active Directory integráció az SAP Analytics-felhővel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az SAP Analytics-felhő között.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "68347798"
---
# <a name="tutorial-integrate-sap-analytics-cloud-with-azure-active-directory"></a>Oktatóanyag: SAP Analytics-felhő integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAP Analytics-felhőt Azure Active Directory (Azure AD) használatával. Ha integrálja az SAP Analytics-felhőt az Azure AD-vel, a következőket teheti:

* Az SAP Analytics-felhőhöz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy az Azure AD-fiókjával automatikusan bejelentkezzenek az SAP Analytics-felhőbe.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SAP Analytics felhőalapú egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az SAP Analytics felhő támogatja az **SP** által KEZDEMÉNYEZett SSO-t

## <a name="adding-sap-analytics-cloud-from-the-gallery"></a>SAP Analytics-felhő hozzáadása a katalógusból

Az SAP Analytics Cloud Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SAP Analytics-felhőt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe a **SAP Analytics Cloud** kifejezést.
1. Válassza az **SAP Analytics Cloud** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t az SAP Analytics-felhővel egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az SAP Analytics-felhőben.

Az Azure AD SSO SAP Analytics-felhővel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
2. **[SAP Analytics FELHŐALAPÚ egyszeri bejelentkezés konfigurálása](#configure-sap-analytics-cloud-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
3. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
5. **[Hozzon létre SAP Analytics Cloud test User](#create-sap-analytics-cloud-test-user)** -t, hogy a B. Simon egy, a felhasználó Azure ad-képviseletéhez kapcsolódó, az SAP Analytics-felhőben található.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **SAP Analytics Cloud** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:

    | |
    |-|-|
    | `https://<sub-domain>.sapanalytics.cloud/` |
    | `https://<sub-domain>.sapbusinessobjects.cloud/` |

    b. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet a következő minta használatával:
    
    | |
    |-|-|
    | `<sub-domain>.sapbusinessobjects.cloud` |
    | `<sub-domain>.sapanalytics.cloud` |

    > [!NOTE] 
    > Ezekben az URL-címekben szereplő értékek csak a bemutatóra vonatkoznak. Frissítse az értékeket a tényleges bejelentkezési URL-címmel és az azonosító URL-címével. A bejelentkezési URL-cím beszerzéséhez lépjen kapcsolatba az [SAP Analytics Cloud Client támogatási csapatával](https://help.sap.com/viewer/product/SAP_BusinessObjects_Cloud/release/). Az azonosító URL-cím lekéréséhez töltse le az SAP Analytics felhőalapú metaadatait a felügyeleti konzolról. Ezt az oktatóanyag későbbi részében ismertetjük.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

6. Az **SAP Analytics-felhő beállítása** szakaszban a követelmények alapján másolja ki a megfelelő URL-címeket.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-sap-analytics-cloud-sso"></a>SAP Analytics felhőalapú egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként az SAP Analytics felhőalapú vállalati webhelyére.

2. Válassza a menü**System** > **rendszerfelügyelet** **menüpontot** > .
    
    ![Válassza a menü, majd a rendszer, majd az adminisztráció lehetőséget.](./media/sapboc-tutorial/config1.png)

3. A **Biztonság** lapon válassza a **Szerkesztés** (toll) ikont.
    
    ![A biztonság lapon kattintson a Szerkesztés ikonra.](./media/sapboc-tutorial/config2.png)  

4. A **hitelesítési módszer**beállításnál válassza az **SAML egyszeri bejelentkezés (SSO)** lehetőséget.

    ![A hitelesítési módszerhez válassza az SAML egyszeri bejelentkezés lehetőséget.](./media/sapboc-tutorial/config3.png)  

5. A szolgáltatói metaadatok letöltéséhez (1. lépés) válassza a **Letöltés**lehetőséget. A metaadat-fájlban keresse meg és másolja a **entityID** értéket. A Azure Portal az **alapszintű SAML-konfiguráció** párbeszédpanelen illessze be az értéket az **azonosító** mezőbe.

    ![Másolja és illessze be a entityID értéket](./media/sapboc-tutorial/config4.png)  

6. Ha fel szeretné tölteni a szolgáltatói metaadatokat (2. lépés) a Azure Portal letöltött fájlban, az **Identity Provider metaadatainak feltöltése**területen válassza a **feltöltés**lehetőséget.  

    ![Az identitás-szolgáltató metaadatainak feltöltése területen válassza a feltöltés lehetőséget.](./media/sapboc-tutorial/config5.png)

7. A **felhasználói attribútum** listáról válassza ki a megvalósításhoz használni kívánt felhasználói attribútumot (3. lépés). Ez a felhasználói attribútum az identitás-szolgáltatóhoz van hozzárendelve. Ha egyéni attribútumot szeretne megadni a felhasználó oldalán, használja az **Egyéni SAML-leképezési** lehetőséget. Vagy az **e-mail-címet** vagy a **felhasználói azonosítót** is választhatja felhasználói attribútumként. A példánkban kiválasztottuk az **e-mailt** , mert a felhasználói azonosító jogcímet a **userPrincipalName** attribútummal együtt a Azure Portal a **felhasználói attribútumok & jogcímek** című szakaszban leképezték. Ez egy egyedi felhasználói e-mailt biztosít, amely minden sikeres SAML-válasz esetében az SAP Analytics Cloud alkalmazásnak lesz elküldve.

    ![Felhasználói attribútum kiválasztása](./media/sapboc-tutorial/config6.png)

8. A fiók identitás-szolgáltatóval való ellenőrzéséhez (4. lépés) a **bejelentkezési hitelesítő adatok (e-mail)** mezőben adja meg a felhasználó e-mail-címét. Ezután válassza a **Fiók ellenőrzése**lehetőséget. A rendszer hozzáadja a bejelentkezési hitelesítő adatokat a felhasználói fiókhoz.

    ![Adja meg az e-mailt, és válassza a fiók ellenőrzése lehetőséget](./media/sapboc-tutorial/config7.png)

9. Válassza a **Mentés** ikont.

    ![Mentés ikon](./media/sapboc-tutorial/save.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az SAP Analytics-felhőhöz való hozzáférés biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **SAP Analytics-felhő**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-sap-analytics-cloud-test-user"></a>SAP Analytics Cloud test-felhasználó létrehozása

Az Azure AD-felhasználókat az SAP Analytics felhőbe való bejelentkezés előtt kell kiépíteni az SAP Analytics-felhőbe. Az SAP Analytics felhőben a kiépítés manuális feladat.

Felhasználói fiók kiépítése:

1. Jelentkezzen be rendszergazdaként az SAP Analytics felhőalapú vállalati webhelyére.

2. Válassza a **menü** > **biztonsági** > **felhasználók**lehetőséget.

    ![Alkalmazott hozzáadása](./media/sapboc-tutorial/user1.png)

3. A **felhasználók** lapon új felhasználói adatok hozzáadásához válassza a elemet **+**. 

    ![Felhasználók hozzáadása lap](./media/sapboc-tutorial/user4.png)

    Ezután hajtsa végre a következő lépéseket:

    a. A **felhasználói azonosító** mezőben adja meg a felhasználó felhasználói azonosítóját, például: **B**.

    b. A **keresztnév** mezőbe írja be a felhasználó utónevét, például: **B**.

    c. A **vezetéknév** mezőbe írja be a felhasználó vezetéknevét, például **Simon**.

    d. A **megjelenítendő név** mezőbe írja be a felhasználó teljes nevét, például: **B. Simon**.

    e. Az **e-mail** mezőbe írja be a felhasználó e-mail-címét, például `b.simon@contoso.com`:.

    f. A **szerepkörök kiválasztása** lapon válassza ki a megfelelő szerepkört a felhasználó számára, majd kattintson az **OK gombra**.

      ![Szerepkör kiválasztása](./media/sapboc-tutorial/user3.png)

    g. Válassza a **Mentés** ikont.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a SAP Analytics felhő csempére kattint, automatikusan be kell jelentkeznie arra az SAP Analytics-felhőbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

