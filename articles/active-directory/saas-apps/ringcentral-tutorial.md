---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező RingCentral |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és RingCentral között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5848c875-5185-4f91-8279-1a030e67c510
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/09/2019
ms.author: jeedes
ms.openlocfilehash: 2e4f45deb8c63640a328e38cebc2ecbe67233c3b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66143138"
---
# <a name="tutorial-integrate-ringcentral-with-azure-active-directory"></a>Oktatóanyag: RingCentral integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a RingCentral integrálása az Azure Active Directory (Azure AD) lesz. RingCentral integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá RingCentral Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezve RingCentral az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, beszerezheti a egyhónapos ingyenes próbaidőszakot [Itt](https://azure.microsoft.com/pricing/free-trial/).
* RingCentral egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja a RingCentral **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-ringcentral-from-the-gallery"></a>RingCentral hozzáadása a katalógusból

Az Azure AD-be RingCentral integráció konfigurálásához, hozzá kell RingCentral a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **RingCentral** kifejezést a keresőmezőbe.
1. Válassza ki **RingCentral** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata RingCentral **Britta Simon**. Az SSO működjön kell RingCentral az Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre.

Az Azure AD SSO RingCentral tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[RingCentral egyszeri bejelentkezést](#configure-ringcentral-sso)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre RingCentral tesztfelhasználót](#create-ringcentral-test-user)**  – egy megfelelője a Britta Simon RingCentral, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **RingCentral** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** szakaszt, ha rendelkezik **szolgáltató metaadatait tartalmazó fájl**, hajtsa végre az alábbi lépéseket:

    1. Kattintson a **metaadatfájl feltöltése**.
    1. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.
    1. Miután sikeresen feltöltötte a metaadat-fájlt, a **azonosítója** és **válasz URL-cím** értékeket automatikusan az első **alapszintű SAML-konfigurációja** szakasz.

    > [!Note]
    > Megjelenik a **szolgáltató metaadatait tartalmazó fájl** a az oktatóanyag későbbi részében ismertetett RingCentral egyszeri bejelentkezési konfiguráció lapon.

1. Ha nem rendelkezik **szolgáltató metaadatait tartalmazó fájl**, adja meg az értékeket a következő mezőket:

    a. Az a **azonosító** szövegmezőbe írja be egy URL-címe:

    | |
    |--|
    |  `https://sso.ringcentral.com` |
    | `https://ssoeuro.ringcentral.com` |

    b. Az a **válasz URL-cím** szövegmezőbe írja be egy URL-címe:

    | |
    |--|
    | `https://sso.ringcentral.com/sp/ACS.saml2` |
    | `https://ssoeuro.ringcentral.com/sp/ACS.saml2` |

1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** és mentse a számítógép.

    ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

### <a name="configure-ringcentral-sso"></a>RingCentral egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be RingCentral egy biztonsági-rendszergazdaként.

1. Az a képernyő felső részén kattintson a **eszközök**.

    ![image](./media/ringcentral-tutorial/ringcentral1.png)

1. Navigáljon a **egyszeri bejelentkezési**.

    ![image](./media/ringcentral-tutorial/ringcentral2.png)

1. Az a **egyszeri bejelentkezés** lap **egyszeri bejelentkezési konfiguráció** szakaszban a **1. lépés** kattintson **szerkesztése** , és hajtsa végre az alábbi lépéseket:

    ![image](./media/ringcentral-tutorial/ringcentral3.png)

1. Az a **egyszeri bejelentkezés beállítása** lapon, a következő lépésekkel:

    ![image](./media/ringcentral-tutorial/ringcentral4.png)

    a. Kattintson a **Tallózás** a metaadatait tartalmazó fájl, amely az Azure Portalról letöltött feltölteni.

    b. Metaadatok feltöltése után az értékek első automatikusan kitölti a **SSO általános információkat** szakaszban.

    c. A **attribútumleképzés** szakaszban jelölje be **térkép E-mail attribútumot** , `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`

    d. Kattintson a **Save** (Mentés) gombra.

    e. A **2. lépés** kattintson **letöltése** letöltéséhez a **szolgáltató metaadatait tartalmazó fájl** , és töltse fel a **alapszintű SAML-konfigurációja** szakasz automatikus feltöltéséhez az **azonosító** és **válasz URL-cím** értékeket az Azure Portalon.

    ![image](./media/ringcentral-tutorial/ringcentral6.png) 

    f. Ugyanazon az oldalon keresse meg **SSO engedélyezése** szakaszt, és hajtsa végre az alábbi lépéseket:

    ![image](./media/ringcentral-tutorial/ringcentral5.png)

    * Válassza ki **egyszeri bejelentkezési szolgáltatás engedélyezése**.

    * Válassza ki **engedélyezése a felhasználók számára, hogy jelentkezzenek be egyszeri bejelentkezés vagy RingCentral hitelesítő**.

    * Kattintson a **Save** (Mentés) gombra.

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

Ebben a szakaszban Britta Simon által biztosított hozzáférés RingCentral Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **RingCentral**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-ringcentral-test-user"></a>RingCentral tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű RingCentral hoz létre. Együttműködve [RingCentral ügyfél-támogatási csapatának](https://success.ringcentral.com/RCContactSupp) a felhasználók hozzáadása az RingCentral platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A RingCentral csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a RingCentral, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
