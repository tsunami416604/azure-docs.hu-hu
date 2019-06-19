---
title: 'Oktatóanyag: Az Azure Active Directory-integráció vezérlővel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a folyamatos ellenőrzés között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1cb7f505-0d06-44b0-95b1-65b470e97092
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: d5a3383c6c79f3cde901c244eafb39bb0f479176
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164912"
---
# <a name="tutorial-integrate-continuity-control-with-azure-active-directory"></a>Oktatóanyag: Folyamatos ellenőrzés integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a folytonossági vezérlő (vezérlő) integrálása az Azure Active Directory (Azure AD) lesz. Vezérlő és az Azure AD integrálása, akkor a következőket teheti:

* Kezelheti az Azure AD ki férhet hozzá a vezérlő.
* Engedélyezze a felhasználóknak kell automatikus bejelentkezésre szabályozhatja az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, beszerezheti a egyhónapos ingyenes próbaidőszakot [Itt](https://azure.microsoft.com/pricing/free-trial/).
* A vezérlő egyszeri bejelentkezés (SSO) engedélyezve van az előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja a vezérlőelemet **SP** által kezdeményezett egyszeri bejelentkezés.

## <a name="adding-control-from-the-gallery"></a>Vezérlőelem hozzáadása a katalógusból

Az Azure AD integrálása a vezérlő konfigurálásához kell a katalógus vezérlőelem felvétele a felügyelt SaaS-alkalmazások listájában.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **vezérlő** kifejezést a keresőmezőbe.
1. Válassza ki **vezérlő** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO-vezérlés használatával nevű tesztfelhasználó **Britta Simon**. SSO működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolatot hozhat létre az vezérlőelem kell.

Az Azure AD SSO vezérlő tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Vezérlő egyszeri bejelentkezést](#configure-control-sso)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre vezérlő tesztfelhasználót](#create-control-test-user)**  – a vezérlőelem, amely kapcsolódik az Azure AD felhasználói ábrázolása egy megfelelője a Britta Simon rendelkezik.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **vezérlő** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** lap, adja meg az értékeket a következő mező:

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<SUBDOMAIN>.continuity.net/auth/saml`

    > [!Note]
    > Az érték nem valódi. Frissítse az értéket a megfelelő együtt. Az egyszeri bejelentkezés altartomány konfigurálhatók [vezérlő hitelesítési stratégiák](https://control.continuity.net/settings/account_profile#tab/security). Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **szerkesztése** gombra kattintva nyissa meg a **SAML-aláíró tanúsítvány** párbeszédpanel.

    ![SAML-aláíró tanúsítvány szerkesztése](common/edit-certificate.png)

1. Az a **SAML-aláíró tanúsítvány** területén másolja a **ujjlenyomat** , és mentse a számítógépre.

    ![Másolja ki az ujjlenyomat értéket](common/copy-thumbprint.png)

1. Az a **felügyeletét** szakaszt, másolja a bejelentkezési URL-címet, és menti azt a számítógépet.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-control-sso"></a>Vezérlő egyszeri bejelentkezés konfigurálása

Az egyszeri bejelentkezés konfigurálásához a **vezérlő** oldalán, akkor frissítenie kell az egyszeri bejelentkezéses hitelesítési beállítások [vezérlő hitelesítési stratégiák](https://control.continuity.net/settings/account_profile#tab/security). Frissítés **SAML egyszeri bejelentkezési URL-cím** az a **bejelentkezési URL-cím** és **tanúsítvány-ujjlenyomat** az a **az ujjlenyomat értéket** az Azure Portalról.

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

Ebben a szakaszban Britta Simon szabályozható a hozzáférés biztosításával az Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **vezérlő**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-control-test-user"></a>Vezérlő tesztfelhasználó létrehozása

Ebben a szakaszban egy vezérlő Britta Simon nevű felhasználó létrehozásához. Együttműködve [vezérlő támogatási csapatának](mailto:help@continuity.net) felhasználót is hozzáadhat a a felügyeleti platformmal. Britta Simon használata az Azure AD **felhasználónév** feltöltéséhez őt **Identity Provider felhasználói azonosító** vezérlőelemben. Felhasználók kell létrehozni, és azok **Identity Provider felhasználói azonosító** vezérlőelem egyszeri bejelentkezés használatához állítsa be.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A vezérlő csempe kiválasztásakor a hozzáférési panelen azt kell automatikusan megtörténik a a vezérlőelemet, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
