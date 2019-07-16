---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az AirWatch |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az AirWatch között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 96a3bb1c-96c6-40dc-8ea0-060b0c2a62e5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 07/11/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9f0a684c6a38ba3a95438941f668b36b23d278df
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68227592"
---
# <a name="tutorial-integrate-airwatch-with-azure-active-directory"></a>Oktatóanyag: AirWatch integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a AirWatch integrálása az Azure Active Directory (Azure AD) lesz. AirWatch integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá az AirWatch Azure AD-ben.
* Engedélyezze a felhasználóknak, hogy lehet automatikusan bejelentkezve AirWatch az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, beszerezheti a egyhónapos ingyenes próbaidőszakot [Itt](https://azure.microsoft.com/pricing/free-trial/).
* AirWatch egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Támogatja az AirWatch **SP** által kezdeményezett egyszeri bejelentkezés.

## <a name="adding-airwatch-from-the-gallery"></a>AirWatch hozzáadása a katalógusból

Az Azure AD integrálása a AirWatch konfigurálásához hozzá kell AirWatch a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **AirWatch** kifejezést a keresőmezőbe.
1. Válassza ki **AirWatch** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO nevű tesztfelhasználó használata AirWatch **B.Simon**. SSO működjön, az Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat kapcsolatot hozhat létre az AirWatch a kell.

Az Azure AD SSO AirWatch tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[AirWatch egyszeri bejelentkezést](#configure-airwatch-sso)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Hozzon létre AirWatch tesztfelhasználót](#create-airwatch-test-user)**  – egy megfelelője a Britta Simon AirWatch, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
5. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **AirWatch** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezési**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** lap, adja meg az értékeket a következő mezőket:

    1. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<subdomain>.awmdm.com/AirWatch/Login?gid=companycode`

    1. Az a **azonosító (entityid)** szöveg írja be az értéket: `AirWatch`

    > [!NOTE]
    > Ez az érték nem a valós. Ez az érték frissítse a tényleges bejelentkezési URL-CÍMÉT. Kapcsolattartó [AirWatch ügyfél-támogatási csapatának](https://www.air-watch.com/company/contact-us/) lekérni ezt az értéket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

1. AirWatch alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **felhasználói attribútumok** párbeszédpanel.

    ![image](common/edit-attribute.png)

1. Az a **felhasználói jogcímek** szakaszában a **felhasználói attribútumok** párbeszédpanelen, a jogcímek szerkesztése használatával **Szerkesztés ikon** , vagy adja hozzá a jogcímek használatával **hozzáadása új jogcímet**SAML-jogkivonat attribútum beállítása, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Name (Név) |  Adatforrás-attribútum|
    |---------------|----------------|
    | EGYEDI AZONOSÍTÓJA | user.userprincipalname |
    | | |

    a. Kattintson a **hozzáadása új jogcímet** megnyitásához a **kezelheti a felhasználói jogcímek** párbeszédpanel.

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Hagyja a **Namespace** üres.

    d. Válassza ki a forrás, **attribútum**.

    e. Az a **forrásattribútum** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    f. Kattintson a **Ok**

    g. Kattintson a **Save** (Mentés) gombra.

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **összevonási metaadatainak XML** válassza **letöltése** az metaadatainak XML-fájl letöltéséhez, és menti azt a számítógépet.

   ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. Az a **AirWatch beállítása** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-airwatch-sso"></a>AirWatch egyszeri bejelentkezés konfigurálása

1. Egy másik böngészőablakban jelentkezzen be az AirWatch vállalati hely rendszergazdaként.

1. A beállítások lapon. Válassza ki **beállítások > Vállalati integráció > címtárszolgáltatások**.

   ![Beállítások](./media/airwatch-tutorial/ic791921.png "beállításai")

1. Kattintson a **felhasználói** lap a **Alap DN** szövegmezőbe írja be a tartomány nevét, és kattintson **mentése**.

   ![Felhasználói](./media/airwatch-tutorial/ic791922.png "felhasználó")

1. Kattintson a **kiszolgáló** fülre.

   ![Kiszolgáló](./media/airwatch-tutorial/ic791923.png "kiszolgáló")

1. Hajtsa végre a következő lépéseket a **LDAP** szakaszban:

    ![Töltse fel](./media/airwatch-tutorial/ic791924.png "LDAP")   

    a. Mint **címtár típusa**válassza **None**.

    b. Válassza ki **SAML hitelesítéshez használandó**.

1. Az a **SAML 2.0** szakaszban, a letöltött tanúsítvány feltöltéséhez kattintson **feltöltése**.

    ![Töltse fel](./media/airwatch-tutorial/ic791932.png "feltöltése")

1. Az a **kérelem** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Kérelem](./media/airwatch-tutorial/ic791925.png "kérése")  

    a. Mint **kötési típus kérése**válassza **POST**.

    b. Az Azure Portalon az a **AirWatch, egyszeri bejelentkezés konfigurálása** párbeszédpanel oldalon, a másolási a **bejelentkezési URL-cím** értéket, és illessze be azt a **Identity Provider egyszeri bejelentkezési URL-Címként** szövegmező.

    c. Mint **nameid-formátumához**válassza **E-mail cím**.

    d. Mint **hitelesítési kérelem biztonsági**válassza **None**.

    e. Kattintson a **Save** (Mentés) gombra.

1. Kattintson a **felhasználói** lapon újra.

    ![Felhasználói](./media/airwatch-tutorial/ic791926.png "felhasználó")

1. Az a **attribútum** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Attribútum](./media/airwatch-tutorial/ic791927.png "attribútum")

    a. Az a **objektumazonosító** szövegmezőbe írja be `http://schemas.microsoft.com/identity/claims/objectidentifier`.

    b. Az a **felhasználónév** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    c. Az a **megjelenítendő név** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    d. Az a **Utónév** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname`.

    e. Az a **Vezetéknév** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname`.

    f. Az a **E-mail** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    g. Kattintson a **Save** (Mentés) gombra.

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

Ebben a szakaszban a hozzáférés biztosításával az AirWatch Azure egyszeri bejelentkezés használatára B.Simon engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **AirWatch**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **B.Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-airwatch-test-user"></a>AirWatch tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók AirWatch bejelentkezni, akkor ki kell építenie az AirWatch. AirWatch, esetén kiépítése a manuális feladat.

**Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **AirWatch** rendszergazdaként a vállalati webhely.

2. A bal oldali navigációs panelén kattintson **fiókok**, és kattintson a **felhasználók**.
  
   ![Felhasználók](./media/airwatch-tutorial/ic791929.png "felhasználók")

3. Az a **felhasználók** menüben kattintson a **listanézet**, és kattintson a **Hozzáadás > felhasználó hozzáadása**.
  
   ![Felhasználó hozzáadása](./media/airwatch-tutorial/ic791930.png "felhasználó hozzáadása")

4. Az a **hozzáadása / szerkesztése felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

   ![Felhasználó hozzáadása](./media/airwatch-tutorial/ic791931.png "felhasználó hozzáadása")

   a. Írja be a **felhasználónév**, **jelszó**, **jelszó megerősítése**, **Utónév**, **Vezetéknév**,  **E-mail-cím** egy érvényes Azure Active Directory-fióknevet, amelyet a kapcsolódó szövegmezőkben létrehozásához.

   b. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> Bármely más AirWatch felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését AirWatch által biztosított API-k.

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Az AirWatch csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett az AirWatch, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
