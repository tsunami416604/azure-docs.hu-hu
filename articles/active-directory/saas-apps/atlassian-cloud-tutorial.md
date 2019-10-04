---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Atlassian felhővel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az Atlassian felhő között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: celested
ms.assetid: 729b8eb6-efc4-47fb-9f34-8998ca2c9545
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 06/07/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: cdbfb07b73d591bbab64f38e8c986fb1b7e072a7
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67106589"
---
# <a name="tutorial-integrate-atlassian-cloud-with-azure-active-directory"></a>Oktatóanyag: Atlassian Felhőbeli integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a Atlassian Felhőbeli integrálása az Azure Active Directory (Azure AD) lesz. Atlassian Felhőbeli integrálása az Azure ad-vel, akkor a következőket teheti:

* Az Azure ad-ben Atlassian felhőbe való hozzáféréssel rendelkező szabályozza.
* Engedélyezze a felhasználóknak kell automatikus bejelentkezésre Atlassian felhőbe az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, beszerezheti a egyhónapos ingyenes próbaidőszakot [Itt](https://azure.microsoft.com/pricing/free-trial/).
* Atlassian felhőalapú egyszeri bejelentkezés (SSO) engedélyezve van az előfizetés.
* Engedélyezi a Security Assertion Markup Language (SAML) egyszeri bejelentkezést az Atlassian felhőalapú termékek, szüksége Atlassian hozzáférés beállítása. Tudjon meg többet [Atlassian hozzáférés]( https://www.atlassian.com/enterprise/cloud/identity-manager).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja az Atlassian felhőalapú **SP és IDP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-atlassian-cloud-from-the-gallery"></a>Atlassian felhő hozzáadása a katalógusból

Atlassian Felhőbeli integrálása az Azure AD beállítása, hozzá kell Atlassian felhőalapú a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Atlassian felhőalapú** kifejezést a keresőmezőbe.
1. Válassza ki **Atlassian felhőalapú** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó Atlassian felhőalapú **B.Simon**. SSO működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolatot hozhat létre az Atlassian felhőben kell.

Az Azure AD SSO Atlassian felhőalapú tesztelése és konfigurálása, hajtsa végre a következő építőelemeit:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Atlassian felhőalapú egyszeri bejelentkezést](#configure-atlassian-cloud-sso)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az B.Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – B.Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Atlassian felhőalapú tesztfelhasználót](#create-atlassian-cloud-test-user)**  - a-megfelelője a B.Simon rendelkezik, amely kapcsolódik az Azure AD felhasználói ábrázolása Atlassian felhőben.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Atlassian felhőalapú** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezés**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, adja meg az értékeket a következő mezőket:

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://auth.atlassian.com/saml/<unique ID>`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://auth.atlassian.com/login/callback?connection=saml-<unique ID>`

    c. Kattintson a **további URL-címet beállítani**.

    d. Az a **továbbítási állapot** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<instancename>.atlassian.net`

    > [!NOTE]
    > Az előző értékek nem valódi. Az értékeket módosítsa a tényleges azonosítóval, és a válasz URL-címe. Valódi értéket kap a **Atlassian felhőalapú SAML-konfigurációja** képernyő, amelynek az ismertetése a később a **konfigurálása Atlassian felhőalapú egyszeri bejelentkezés** oktatóanyag.

1. Kattintson a **további URL-címet beállítani** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím:  `https://<instancename>.atlassian.net`

    > [!NOTE]
    > A bejelentkezési URL-érték nem valódi. Illessze be az értéket a példányon, amely bejelentkezik a Atlassian felhőalapú felügyeleti portál használatával.

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-10.png)

1. Az Atlassian felhőalapú alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amely megköveteli, hogy egyéni attribútum-leképezéshez az SAML-jogkivonat attribútumai konfigurációja. Az alábbi képernyőképen az alapértelmezett attribútumok listáját jeleníti meg, hol **nameidentifier** le van képezve a **user.userprincipalname**. Atlassian felhőalkalmazás vár **nameidentifier** a leképezendő **user.mail**, így kell szerkesztenie a attribútumleképezés kattintva **szerkesztése** ikonra, és módosítsa a Attribútumleképzés.

    ![image](common/edit-attribute.png)

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **tanúsítvány (Base64)** válassza **letöltése** töltse le a tanúsítványt, és menti azt a számítógépet.

    ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. Az a **Atlassian felhő beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure AD-azonosító

    c. Kijelentkezési URL

### <a name="configure-atlassian-cloud-sso"></a>Egyszeri bejelentkezés Atlassian felhő konfigurálása

1. Automatizálhatja a konfiguráció Atlassian-felhőben, telepítenie kell **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** kattintva **a bővítmény telepítése**.

    ![Saját alkalmazások kiterjesztése](common/install-myappssecure-extension.png)

2. A felvett bővítmény a böngészőre, kattintson a **telepítő Atlassian felhőalapú** átirányítja azt az Atlassian felhőalapú alkalmazás. Itt adja meg a rendszergazdai hitelesítő adataival bejelentkezni Atlassian felhő. A webböngésző-bővítmény automatikusan konfigurálja az alkalmazást, és 3-7 lépések automatizálásához.

    ![Konfiguráció beállítása](common/setup-sso.png)

3. Ha azt szeretné, manuálisan állíthatja be az Atlassian felhő, nyisson meg egy új böngészőablakban, és jelentkezzen be rendszergazdaként vállalati Atlassian felhőalapú webhelyét, és hajtsa végre az alábbi lépéseket:

4. Ellenőrizze a tartományt egyszeri bejelentkezés konfigurálása előtt kell. További információkért lásd: [Atlassian tartomány-ellenőrzés](https://confluence.atlassian.com/cloud/domain-verification-873871234.html) dokumentumot.

5. A bal oldali panelen válassza ki a **biztonsági** > **SAML egyszeri bejelentkezés**. Ha ezt még nem tette meg, az előfizetés Atlassian Identity Manager.

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-11.png)

6. Az a **hozzáadása SAML-konfigurációja** ablakban tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-12.png)

    a. Az a **identitásszolgáltató Entitásazonosító** mezőbe illessze be a **az Azure AD-azonosító** , az Azure Portalról másolt.

    b. Az a **identitásszolgáltató egyszeri bejelentkezési URL-cím** mezőbe illessze be a **bejelentkezési URL-cím** , az Azure Portalról másolt.

    c. Nyissa meg a letöltött tanúsítvány egy .txt fájlban, az Azure portálról másolja az értéket (nélkül a *megkezdéséhez tanúsítvány* és *End Certificate* sorok), majd illessze be a a **nyilvános X509 tanúsítvány** mezőbe.

    d. Kattintson a **konfiguráció mentése**.

7. Győződjön meg arról, hogy meg van adva a megfelelő URL-címeket, frissítse az Azure AD-beállításokat az alábbiak szerint:

    ![Egyszeri bejelentkezés konfigurálása](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-13.png)

    a. A SAML ablakban másolja a **SP Szolgáltatásidentitás azonosítója** , majd az Azure portal alatt Atlassian felhőalapú **alapszintű SAML-konfigurációja**, illessze be a **azonosító** mezőbe.

    b. Az SAML ablakban másolja a **SP helyességi feltétel fogyasztói szolgáltatás URL-címe** , majd az Azure portal alatt Atlassian felhőalapú **alapszintű SAML-konfigurációja**, illessze be a **válasz URL-cím**mezőbe. A bejelentkezési URL-bérlői URL-címét az Atlassian Felhőbeli.

    > [!NOTE]
    > Hogy egy meglévő ügyfél frissítése után a **SP Szolgáltatásidentitás azonosítója** és **SP helyességi feltétel fogyasztói szolgáltatás URL-címe** értékeket az Azure Portalon válassza ki **Igen, frissítse a konfigurációt**. Ha Ön új ügyfél, kihagyhatja ezt a lépést.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon B.Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `BrittaSimon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a hozzáférés biztosításával Atlassian felhőbe Azure egyszeri bejelentkezés használatára B.Simon engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Atlassian felhőalapú**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **B.Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-atlassian-cloud-test-user"></a>Tesztfelhasználó Atlassian felhő létrehozása

Ahhoz, hogy jelentkezzen be a Atlassian felhőalapú Azure AD-felhasználók, a felhasználói fiókok manuális Atlassian felhőben kiosztani az alábbiak szerint:

1. Az a **felügyeleti** ablaktáblán válassza előbb **felhasználók**.

    ![A Felhőbeli felhasználók Atlassian-hivatkozás](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-14.png)

2. Egy felhasználó Atlassian felhőben létrehozásához válassza **meghívó felhasználói**.

    ![Hozzon létre egy Atlassian Felhőbeli felhasználó](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-15.png)

3. Az a **E-mail-cím** mezőbe írja be a felhasználó e-mail címét, és rendelje hozzá az alkalmazás-hozzáférést.

    ![Hozzon létre egy Atlassian Felhőbeli felhasználó](./media/atlassian-cloud-tutorial/tutorial-atlassiancloud-16.png)

4. Szeretne küldeni a felhasználónak e-mailes meghívót, válassza ki a **felhasználók meghívása**. E-mailt küld a felhasználónak, és elfogadja a meghívást, miután a felhasználó nem aktív, a rendszer.

> [!NOTE]
> Tömegesen is-felhasználók létrehozása kiválasztásával a **tömeges létrehozásához** gombra a **felhasználók** szakaszban.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A felhő Atlassian csempe kiválasztásakor a hozzáférési panelen azt kell automatikusan megtörténik a, amelynek beállítása egyszeri bejelentkezés Atlassian felhőbe. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)