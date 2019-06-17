---
title: 'Oktatóanyag: Az Azure AD SAML-eszközkészlet az Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az Azure AD SAML-eszközkészlet között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 3f4348e7-c34e-43c7-926e-f1b26ffacf6d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4baeda4fca69969fae3f866e642ad3dc8ad46509
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67091938"
---
# <a name="tutorial-integrate-azure-ad-saml-toolkit-with-azure-active-directory"></a>Oktatóanyag: Az Azure AD SAML-eszközkészlet integrálása az Azure Active Directoryval

Ebben az oktatóanyagban megismerheti, hogyan integrálható az Azure AD SAML-eszközkészlet az Azure Active Directoryval (Azure AD) lesz. Az Azure AD integrálása az Azure AD SAML-eszközkészlet, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá az Azure AD SAML-eszközkészlet az Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezett az Azure AD SAML-eszközkészlet az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, beszerezheti a egyhónapos ingyenes próbaidőszakot [Itt](https://azure.microsoft.com/pricing/free-trial/).
* Az Azure AD SAML eszközkészlet egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja az Azure AD SAML-eszközkészlet **SP** által kezdeményezett egyszeri bejelentkezés.

## <a name="adding-azure-ad-saml-toolkit-from-the-gallery"></a>Az Azure AD SAML-eszközkészlet hozzáadása a katalógusból

Az integráció az Azure AD-be az Azure AD SAML-eszközkészlet konfigurálásához hozzá kell az Azure AD SAML-eszközkészlet a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Azure AD SAML-eszközkészlet** kifejezést a keresőmezőbe.
1. Válassza ki **Azure AD SAML-eszközkészlet** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata az Azure AD SAML-eszközkészlet **B.Simon**. Működik az egyszeri bejelentkezés Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolat létrehozására az Azure AD SAML-eszközkészlet szüksége.

Az Azure AD SAML-eszközkészlet az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Egyszeri bejelentkezés az Azure AD SAML-eszközkészlet konfigurálása](#configure-azure-ad-saml-toolkit-sso)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés az B.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  B.Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre az Azure AD SAML-eszközkészlet tesztfelhasználót](#create-azure-ad-saml-toolkit-test-user)**  egy megfelelője a B.Simon rendelkeznie az Azure AD SAML-eszközkészlet, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Azure AD SAML-eszközkészlet** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezés**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** lap, adja meg az értékeket a következő mezőket:

    1. Az a **bejelentkezési URL-cím** szövegmezőbe írja be egy URL-címe: `https://samltoolkit.azurewebsites.net/`

    1. Az a **azonosító (entityid)** szövegmezőbe írja be egy URL-címe: `https://samltoolkit.azurewebsites.net`

    1. Az a **válasz URL-cím** szövegmezőbe írja be egy URL-címe: `https://samltoolkit.azurewebsites.net/SAML/Consume`

1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

   ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. Az a **beállítása az Azure AD SAML-eszközkészlet** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-azure-ad-saml-toolkit-sso"></a>Az Azure AD SAML SSO-t eszközkészlet konfigurálása

1. Nyissa meg egy új böngészőablakban, ha nem regisztrált az Azure AD SAML-eszközkészlet webhelyen, először regisztrálnia kattintva a **regisztrálása**. Ha már regisztrálta, jelentkezzen be az Azure AD SAML-eszközkészlet vállalati hely használatával a regisztrált bejelentkezési hitelesítő adatait.

    ![Azure AD SAML Toolkit Register](./media/saml-toolkit-tutorial/register.png)

1. Kattintson a **SAML-konfigurációja**.

    ![Az Azure AD SAML eszközkészlet SAML-konfigurációja](./media/saml-toolkit-tutorial/saml-configure.png)

1. Kattintson a **Create** (Létrehozás) gombra.

    ![Az Azure AD SAML-eszközkészlet egyszeri bejelentkezés létrehozása](./media/saml-toolkit-tutorial/createsso.png)

1. Az a **SAML SSO-konfigurációja** lapon, a következő lépésekkel:

    ![Az Azure AD SAML-eszközkészlet egyszeri bejelentkezés létrehozása](./media/saml-toolkit-tutorial/fill-details.png)

    1. Az a **bejelentkezési URL-cím** szövegmezőjébe illessze be a **bejelentkezési URL-cím** érték, amely az Azure Portalról másolta.

    1. Az a **az Azure AD-azonosító** szövegmezőjébe illessze be a **az Azure AD-azonosító** érték, amely az Azure Portalról másolta.

    1. Az a **kijelentkezési URL-címe** szövegmezőjébe illessze be a **kijelentkezési URL-címe** érték, amely az Azure Portalról másolta.

    1. Kattintson a **fájl kiválasztása** , és töltse fel a **összevonási metaadatainak XML** fájlt, amely az Azure Portalról letöltött.

    1. Kattintson a **Create** (Létrehozás) gombra.

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

Ebben a szakaszban Azure egyszeri bejelentkezést a hozzáférés biztosításával az Azure AD SAML-eszközkészlet használata B.Simon engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Azure AD SAML-eszközkészlet**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **B.Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-azure-ad-saml-toolkit-test-user"></a>Az Azure AD SAML-eszközkészlet tesztfelhasználó létrehozása

Ebben a szakaszban egy B.Simon nevű felhasználó az Azure AD SAML-eszközkészlet jön létre. Azure AD SAML-eszközkészlet támogatja a just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó már nem létezik az Azure AD SAML-eszközkészlet, egy új jön létre a hitelesítés után.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Az Azure AD SAML-eszközkészlet csempe kiválasztásakor a hozzáférési panelen, érdemes lehet automatikusan bejelentkezett, amelynek beállítása egyszeri bejelentkezés az Azure AD SAML-eszközkészlet. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
