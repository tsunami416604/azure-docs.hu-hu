---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Zscaler privát hozzáférést (ZPA) |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Zscaler privát hozzáférést (ZPA) között.
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
ms.openlocfilehash: 984498a2b9d4d72ee6bb6b9f0a9e62636bf870bf
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66226439"
---
# <a name="tutorial-integrate-zscaler-private-access-zpa-with-azure-active-directory"></a>Oktatóanyag: Zscaler privát hozzáférést (ZPA) integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a Zscaler privát hozzáférést (ZPA) integrálása az Azure Active Directory (Azure AD) lesz. Zscaler privát hozzáférést (ZPA) integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozhatja az Azure ad-ben, aki hozzáfér a Zscaler privát hozzáférést (ZPA).
* Engedélyezze a felhasználókat, hogy a rendszer automatikusan bejelentkezett a Zscaler privát hozzáférést (ZPA) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Zscaler privát hozzáférést (ZPA) egyszeri bejelentkezéses (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Zscaler privát hozzáférést (ZPA) támogatja a **SP** által kezdeményezett egyszeri bejelentkezés.

## <a name="adding-zscaler-private-access-zpa-from-the-gallery"></a>Zscaler privát hozzáférést (ZPA) hozzáadása a katalógusból

Konfigurálja az integrációt a Zscaler privát hozzáférést (ZPA) az Azure AD-be, szüksége Zscaler privát hozzáférést (ZPA) hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Zscaler privát hozzáférést (ZPA)** kifejezést a keresőmezőbe.
1. Válassza ki **Zscaler privát hozzáférést (ZPA)** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO a Zscaler privát hozzáférést (ZPA) nevű tesztfelhasználó használata **Britta Simon**. SSO működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolatot hozhat létre a Zscaler privát hozzáférést (ZPA) kell.

Az Azure AD SSO a Zscaler privát hozzáférést (ZPA) tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Zscaler privát hozzáférést (ZPA) konfigurálása](#configure-zscaler-private-access-zpa)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Zscaler privát hozzáférést (ZPA) tesztfelhasználót](#create-zscaler-private-access-zpa-test-user)**  szeretné, hogy egy megfelelője a Britta Simon a Zscaler privát hozzáférést (ZPA), amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Zscaler privát hozzáférést (ZPA)** alkalmazás integráció lapon keresse meg a **kezelés** szakaszt, és válassza **egyszeri bejelentkezés**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** lap, adja meg az értékeket a következő mezőket:

    1. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://samlsp.private.zscaler.com/auth/login?domain=<your-domain-name>`

    1. Az a **azonosító (entityid)** szövegmezőbe írja be egy URL-címe: `https://samlsp.private.zscaler.com/auth/metadata`

    > [!NOTE]
    > A **bejelentkezési URL-cím** értéke nem valódi. Frissítse az értéket a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [Zscaler privát hozzáférést (ZPA) ügyfél-támogatási csapatának](https://help.zscaler.com/zpa-submit-ticket) a gépkulcsengedélyek értékének. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **összevonási metaadatainak XML** válassza **letöltése** töltse le a tanúsítványt, és menti azt a számítógépet.

   ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. Az a **állítsa be Zscaler privát hozzáférést (ZPA)** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-zscaler-private-access-zpa"></a>Konfigurálja a Zscaler privát hozzáférést (ZPA)

1. Automatizálhatja a konfiguráció belül Zscaler privát hozzáférést (ZPA), telepítenie kell **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** kattintva **a bővítmény telepítése**.

    ![Saját alkalmazások kiterjesztése](common/install-myappssecure-extension.png)

2. A felvett bővítmény a böngészőre, kattintson a **telepítő Zscaler privát hozzáférést (ZPA)** fog irányítja át a Zscaler privát hozzáférést (ZPA) alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatait, jelentkezzen be Zscaler privát hozzáférést (ZPA). A webböngésző-bővítmény automatikusan konfigurálja az alkalmazást, és 3 – 6. lépések automatizálásához.

    ![Konfiguráció beállítása](common/setup-sso.png)

3. Ha szeretné manuálisan beállítani a Zscaler privát hozzáférést (ZPA), nyissa meg egy új böngészőablakban, és jelentkezzen be rendszergazdaként vállalati Zscaler privát hozzáférést (ZPA) webhelyét, és hajtsa végre az alábbi lépéseket:

4. A menüt a bal oldali menüjében kattintson az **felügyeleti** , és keresse meg **hitelesítési** szakaszban kattintson **identitásszolgáltató konfigurációja**.

    ![Zscaler Private Access Administrator administration](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-administration.png)

5. Kattintson a jobb felső sarokban **adja hozzá az identitásszolgáltató konfigurációja**. 

    ![Zscaler privát hozzáférés rendszergazdája identitásszolgáltató](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-idp.png)

6. Az a **adja hozzá az identitásszolgáltató konfigurációja** oldalon tegye a következőket:
 
    ![Zscaler privát hozzáférést rendszergazda kiválasztása](./media/zscalerprivateaccess-tutorial/tutorial-zscaler-private-access-select.png)

    a. Kattintson a **fájl kiválasztása** feltölteni a letöltött metaadat-fájlt az Azure AD-ből a **IdP-metaadatok fájlfeltöltés** mező.

    b. Olvassa be, hogy a **identitásszolgáltató metaadatok** Azure AD-ből és tölti fel a mezők adatai az alább látható módon.

    ![Zscaler Private Access Administrator config](./media/zscalerprivateaccess-tutorial/config.png)

    c. Válassza ki a tartományt a **tartományok** mező.
    
    d. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon Britta Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `BrittaSimon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a hozzáférés biztosításával a Zscaler privát hozzáférést (ZPA) Azure egyszeri bejelentkezés használatára Britta Simon engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Zscaler privát hozzáférést (ZPA)** .
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-zscaler-private-access-zpa-test-user"></a>Zscaler privát hozzáférést (ZPA) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon a Zscaler privát hozzáférést (ZPA) nevű felhasználó létrehozásához. Együttműködve [Zscaler privát hozzáférést (ZPA) támogatási csapatának](https://help.zscaler.com/zpa-submit-ticket) a felhasználók hozzáadása a Zscaler privát hozzáférést (ZPA) platform.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A Zscaler privát hozzáférést (ZPA) csempe kiválasztásakor a hozzáférési panelen azt kell automatikusan megtörténik a, a Zscaler privát hozzáférést (ZPA), amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)