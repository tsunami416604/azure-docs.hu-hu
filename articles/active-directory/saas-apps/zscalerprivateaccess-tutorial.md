---
title: 'Oktatóanyag: Azure Active Directory integráció a Zscaler privát hozzáférésével (ZPA) | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Zscaler privát hozzáférése (ZPA) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 83711115-1c4f-4dd7-907b-3da24b37c89e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: e0a1538f640bb4722eca1d4f3a80125837593bab
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "67085735"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Oktatóanyag: a Zscaler privát hozzáférésének (ZPA) integrálása Azure Active Directory

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Zscaler privát hozzáférését (ZPA) Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Zscaler privát hozzáférését (ZPA), a következőket teheti:

* Vezérlés az Azure AD-ben, aki hozzáfér a Zscaler privát eléréséhez (ZPA).
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Zscaler privát elérésére (ZPA) az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Zscaler privát hozzáférés (ZPA) egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A Zscaler Private Access (ZPA) támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Zscaler privát hozzáférésének (ZPA) hozzáadása a katalógusból

A Zscaler Private Access (ZPA) Azure AD-ba való integrálásának konfigurálásához hozzá kell adnia a Zscaler privát hozzáférését (ZPA) a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Zscaler privát elérés (ZPA)** kifejezést a keresőmezőbe.
1. Válassza az **Zscaler privát elérés (ZPA)** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Konfigurálja és tesztelje az Azure AD SSO-t a Zscaler Private Access (ZPA) segítségével egy **Britta Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Zscaler Private Accessben (ZPA).

Az Azure AD SSO és a Zscaler magánhálózati hozzáférés (ZPA) konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
2. **[Konfigurálja a Zscaler privát hozzáférését (ZPA)](#configure-zscaler-private-access-zpa)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
3. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez a Britta Simon használatával.
4. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy a Britta Simon engedélyezze az Azure ad egyszeri bejelentkezés használatát.
5. **[Hozzon létre Zscaler Private Access (ZPA) tesztelési felhasználót](#create-zscaler-private-access-zpa-test-user)** , hogy rendelkezzen egy, a felhasználó Azure ad-képviseletéhez kapcsolódó Zscaler Private Access (ZPA) Britta.
6. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Zscaler Private Access (ZPA)** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** lapon adja meg a következő mezők értékeit:

    1. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. Az **azonosító (Entity ID)** szövegmezőbe írja be az URL-címet:`https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > A **bejelentkezési URL-cím** értéke nem valós. Frissítse az értéket a tényleges bejelentkezési URL-címmel. Az érték beszerzéséhez forduljon a [Zscaler privát hozzáférési (ZPA) ügyfélszolgálatához](https://help.zscaler.com/zpa-submit-ticket) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Zscaler privát elérésének beállítása (ZPA)** szakaszban a követelmények alapján másolja ki a megfelelő URL-címeket.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Zscaler privát hozzáférésének konfigurálása (ZPA)

1. Ha a Zscaler privát hozzáférés (ZPA) szolgáltatáson belül szeretné automatizálni a konfigurációt, telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Setup Zscaler Private Access (ZPA)** elemre, majd a Zscaler Private Access (ZPA) alkalmazásra irányítja. Itt adja meg a rendszergazdai hitelesítő adatokat, hogy bejelentkezzen a Zscaler privát elérésére (ZPA). A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a Zscaler privát elérését (ZPA), nyisson meg egy új böngészőablakot, és jelentkezzen be a Zscaler Private Access (ZPA) vállalati webhelyére rendszergazdaként, és hajtsa végre a következő lépéseket:

4. A menü bal oldalán kattintson az **Adminisztráció** elemre, és navigáljon a **hitelesítés** szakaszra, majd kattintson a **identitásszolgáltató-konfiguráció**elemre.

    ![Zscaler-hozzáférés rendszergazdai felügyelete](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. A jobb felső sarokban kattintson a **identitásszolgáltató-konfiguráció hozzáadása**lehetőségre. 

    ![Zscaler privát hozzáférés-felügyeleti identitásszolgáltató](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. A **identitásszolgáltató-konfiguráció hozzáadása** oldalon hajtsa végre a következő lépéseket:
 
    ![Zscaler privát hozzáférés-rendszergazdájának kiválasztása](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Kattintson a **fájl kiválasztása** lehetőségre a letöltött metaadat-fájl feltöltéséhez az Azure ad-ből a **identitásszolgáltató metaadat-fájl feltöltése** mezőben.

    b. Beolvassa a **identitásszolgáltató metaadatait** az Azure ad-ből, és az alább látható módon feltölti az összes mezőt.

    ![Zscaler magánhálózati hozzáférésének rendszergazdai konfigurációja](./media/zscalerprivateaccess-tutorial/config.png)

    c. Válassza ki a tartományt a **tartományok** mezőben.
    
    d. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Britta Simon nevű Azure Portalban.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `BrittaSimon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a Britta Simon használatával engedélyezheti az Azure egyszeri bejelentkezést azáltal, hogy hozzáférést biztosít a Zscaler privát eléréséhez (ZPA).

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Zscaler privát hozzáférés (ZPA)** lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a **Britta Simon** elemet a felhasználók listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

### <a name="create-zscaler-private-access-zpa-test-user"></a>Zscaler (ZPA) tesztelési felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Zscaler Private Accessben (ZPA). A [Zscaler Private Access (ZPA) támogatási csapatával](https://help.zscaler.com/zpa-submit-ticket) vegye fel a felhasználókat a Zscaler Private Access (ZPA) platformon.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ha a hozzáférési panelen kiválasztja a Zscaler Private Access (ZPA) csempét, automatikusan be kell jelentkeznie a Zscaler privát elérésére (ZPA), amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)