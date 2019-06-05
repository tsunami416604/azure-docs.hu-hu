---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az segítő segítő |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés segítő segítő és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 5f42e4d7-4d92-4096-a0d5-02fa438a5dfd
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/31/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19969588001bd48bd1c998fe49061db80d94c636
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2019
ms.locfileid: "66518521"
---
# <a name="tutorial-integrate-helper-helper-with-azure-active-directory"></a>Oktatóanyag: Az Azure Active Directory segítő segítő integrálása

Ebben az oktatóanyagban elsajátíthatja a segítő segítő integrálása az Azure Active Directory (Azure AD) lesz. Segítő segítő integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá segítő segítő Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezve segítő segítő az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Segítő segítő egyszeri bejelentkezés (SSO) engedélyezve van az előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Segítő segítő támogatja **SP és IDP** által kezdeményezett egyszeri Bejelentkezést, és támogatja a **igény szerinti** felhasználóátadást készíthet elő.

## <a name="adding-helper-helper-from-the-gallery"></a>Segítő segítő hozzáadása a katalógusból

Segítő segítő integrálása az Azure AD beállítása, hozzá kell segítő segítő a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **segítő segítő** kifejezést a keresőmezőbe.
1. Válassza ki **segítő segítő** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használó segítő segítő **b Simon**. Az SSO működjön kell segítő segítő az Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre.

Az Azure AD SSO segítő segítő tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Segítő segítő konfigurálása](#configure-helper-helper)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés a b Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  b Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre segítő segítő tesztfelhasználót](#create-helper-helper-test-user)**  van egy megfelelője a b Simon segítő segítő, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **segítő segítő** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** szakaszt, ha rendelkezik **szolgáltató metaadatait tartalmazó fájl** és való konfigurálásához a **Identitásszolgáltató** kezdeményezett módban a következő lépésekkel:

    >[!NOTE]
    >Nyissa meg az URL-cím `https://sso.helperhelper.com/saml/<customer_id>` beolvasni a szolgáltató metaadatait tartalmazó fájl. Kapcsolattartó [segítő segítő ügyfél-támogatási csapatának](mailto:info@helperhelper.com) a `<customer_id>`.

    a. Kattintson a **metaadatfájl feltöltése**.

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    c. A metaadatfájl sikeres feltöltése után a **azonosító** és **válasz URL-cím** értékeket automatikusan alapszintű SAML-konfigurációja szakasz első.

    > [!Note]
    > Ha a **azonosító** és **válasz URL-cím** értékek nem automatikus polulated lekérése, majd adja meg az értékeket manuálisan a követelmény alapján.

1. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://sso.helperhelper.com/saml/<customer_id>/login`

    > [!NOTE]
    > A bejelentkezési URL-érték nem valódi. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [segítő segítő ügyfél-támogatási csapatának](mailto:info@helperhelper.com) lekérni ezt az értéket. Emellett olvassa el a bemutatott minták a **alapszintű SAML-konfigurációja** az Azure portal.l szakaszát.

1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** és mentse a Jegyzettömbben .

   ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

1. Az a **segítő Segéd** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-helper-helper"></a>Segítő segítő konfigurálása

Az egyszeri bejelentkezés konfigurálása **segítő segítő** oldalon kell küldenie a **alkalmazás összevonási metaadatainak URL-címe** való [segítő segítő támogatási csapatának](mailto:info@helperhelper.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon b Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B. Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `BrittaSimon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban b Simon által biztosított hozzáférés segítő segítő Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **segítő segítő**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **b Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-helper-helper-test-user"></a>Segítő segítő tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó segítő segítő jön létre. Segítő segítő támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó már nem létezik az segítő segítő, a hitelesítés után egy új jön létre.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A segítő segítő csempe kiválasztásakor a hozzáférési panelen, érdemes lehet automatikusan bejelentkezett, amelynek beállítása egyszeri bejelentkezési segéd segítő. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)