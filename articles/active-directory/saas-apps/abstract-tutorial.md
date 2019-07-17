---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az absztrakt |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az absztrakt között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 2eb167ab-d769-4661-a8cb-ae371cb63d2a
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8038784c9da30a42541688536169576cd9a92e9
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68235010"
---
# <a name="tutorial-integrate-abstract-with-azure-active-directory"></a>Oktatóanyag: Absztrakt integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a absztrakt integrálása az Azure Active Directory (Azure AD) lesz. Absztrakt integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá absztrakt Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezve absztrakt az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, hozzájuthat egy [ingyenes fiókot](https://azure.microsoft.com/free/).
* Absztrakt egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben.

* Absztrakt támogatja **SP és IDP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-abstract-from-the-gallery"></a>Absztrakt hozzáadása a katalógusból

Absztrakt integrálása az Azure AD beállítása, hozzá kell absztrakt a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **absztrakt** kifejezést a keresőmezőbe.
1. Válassza ki **absztrakt** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.


## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata absztrakt **B.Simon**. Működik az egyszeri bejelentkezés Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolat létrehozására az absztrakt szüksége.

Az Azure AD SSO absztrakt tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Absztrakt egyszeri bejelentkezést](#configure-abstract-sso)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre absztrakt tesztfelhasználót](#create-abstract-test-user)**  - a-megfelelője a Britta Simon szerepel, amely kapcsolódik az Azure AD felhasználói ábrázolása absztrakt.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **absztrakt** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** szakaszban az alkalmazás előre konfigurálva **Identitásszolgáltató** kezdeményezett mód és a szükséges URL-címek vannak már előre fel van töltve az Azure-ral. A felhasználónak szüksége van a konfiguráció mentéséhez kattintson a **mentése** gombra.

1. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címe:  `https://app.abstract.com/signin`

4. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson a Másolás gombra, hogy **alkalmazás összevonási metaadatainak URL-címe** és mentse a számítógép.

    ![A tanúsítvány letöltési hivatkozás](common/copy-metadataurl.png)

### <a name="configure-abstract-sso"></a>Absztrakt egyszeri bejelentkezés konfigurálása

Győződjön meg arról, ne felejtse el lekérni a `App Federation Metadata Url` és a `Azure AD Identifier` az Azure Portalról, meg kell ezeket az egyszeri bejelentkezés konfigurálása az absztrakt.

Találja meg azokat az információkat a **állítsa be egyszeri bejelentkezést az SAML** oldalon:

* A `App Federation Metadata Url` található a **SAML-aláíró tanúsítvány** szakaszban.
* A `Azure AD Identifier` található a **absztrakt beállítása** szakaszban.


Most már készen áll a absztrakt egyszeri bejelentkezés konfigurálására:

>[!Note]
>Szüksége lesz egy szervezeti rendszergazdai fiókot az egyszeri bejelentkezési beállításainak absztrakt a hitelesítéshez.

1. Nyissa meg a [absztrakt webalkalmazás](https://app.abstract.com/).
2. Nyissa meg a **engedélyek** lapra a bal oldali sávon.
3. Az a **egyszeri bejelentkezés konfigurálása** területén adja meg a **metaadatok URL-címe** és **Entitásazonosító**.
4. Adja meg, lehetséges, hogy manuális kivételeket. A manuális kivételek szakaszban felsorolt e-mailek fog Egyszeri mellőzés, és tudni jelentkezzen be az e-mailek és a jelszót. 
5. Kattintson a **módosítások mentése**.

>[!Note] 
>Elsődleges e-mail-címeket használja a manuális kivételek lista kell. Egyszeri bejelentkezés az aktiválás sikertelen lesz, ha az e-mailben listázza a felhasználó másodlagos e-mail. Ha ez történik, látni fogja a sikertelen fiók elsődleges e-mail-címével hibaüzenetet. A felhasználó ismeri ellenőrzése után adja hozzá, hogy az elsődleges e-mail a manuális kivételek listájáról.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon B.Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban B.Simon által biztosított hozzáférés absztrakt Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **absztrakt**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **B.Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-abstract-test-user"></a>Absztrakt tesztfelhasználó létrehozása

Absztrakt az egyszeri bejelentkezés teszteléséhez:

1. Nyissa meg a [absztrakt webalkalmazás](https://app.abstract.com/).
2. Nyissa meg a **engedélyek** lapra a bal oldali sávon.
3. Kattintson a **fiókot tesztelési**. Ha a teszt meghiúsul, kérjük [forduljon az ügyfélszolgálati csapatunkhoz](https://www.abstract.com/help/contact/).

>[!Note]
>Szüksége lesz egy szervezeti rendszergazdai fiókot az egyszeri bejelentkezési beállításainak absztrakt a hitelesítéshez.
A szervezeti rendszergazdai fiókot kell az Azure Portalon absztrakt rendelhető.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Az absztrakt csempére kattintva a hozzáférési panelen kell lennie automatikusan jelentkezett be, amelynek beállítása egyszeri bejelentkezés az absztrakt. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

