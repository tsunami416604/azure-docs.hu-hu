---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező TOPdesk – biztonságos |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és TOPdesk – biztonságos között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/27/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 2a559191bb4e84b3b60529bf20a25e381c72f42a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57840936"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező TOPdesk – biztonságos

Ebben az oktatóanyagban elsajátíthatja, hogyan TOPdesk - integrálása az Azure Active Directoryval (Azure AD) biztonságos.
TOPdesk – biztonságos integrálása az Azure ad-vel biztosít a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá TOPdesk – biztonságos Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezve TOPdesk – biztonságos (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása TOPdesk – biztonságos, kell a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* TOPdesk – biztonságos egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* TOPdesk – biztonságos támogatja **SP** által kezdeményezett egyszeri bejelentkezés

## <a name="adding-topdesk---secure-from-the-gallery"></a>TOPdesk - Hozzáadás a katalógusból biztonságossá tétele

Biztonságos TOPdesk - integrációjának konfigurálása az Azure AD-be kell TOPdesk hozzáadása – a katalógusból mezőlistához felügyelt SaaS alkalmazások védelme.

**TOPdesk - hozzáadása a katalógusból biztonságos, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **TOPdesk – biztonságos**, jelölje be **TOPdesk – biztonságos** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![TOPdesk – biztonságos a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az TOPdesk – biztonságos alapján nevű tesztfelhasználó **Britta Simon**.
Az egyszeri bejelentkezés működjön, egy hivatkozás egy Azure AD-felhasználót és a kapcsolódó felhasználó TOPdesk - hozható létre biztonságos kell közötti kapcsolat.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés TOPdesk – a biztonságos, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[TOPdesk – biztonságos egyszeri bejelentkezés konfigurálása](#configure-topdesk---secure-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre TOPdesk – biztonságos tesztfelhasználó](#create-topdesk---secure-test-user)**  - a-megfelelője a Britta Simon TOPdesk – biztonságos a felhasználó Azure ad-ben reprezentációja kapcsolódó rendelkezik.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

TOPdesk – az Azure AD egyszeri bejelentkezés konfigurálása biztonságos, hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **TOPdesk – biztonságos** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    ![TOPdesk – biztonságos tartomány és URL-címek egyszeri bejelentkezési adatait](common/sp-identifier-reply.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<companyname>.topdesk.net`

    b. Az a **azonosító** mezőbe írja be a következő minta használatával URL-cím: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<companyname>.topdesk.net/tas/public/login/saml`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-, azonosítóját és válasz URL-cím. Kapcsolattartó [TOPdesk – biztonságos ügyfél-támogatási csapatának](https://www.topdesk.com/us/support/) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. Az a **TOPdesk – biztonságos beállítása** területén másolja megfelelően a követelmény a megfelelő URL-címe.

    ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure Ad Identifier

    c. Kijelentkezési URL

### <a name="configure-topdesk---secure-single-sign-on"></a>TOPdesk konfigurálása – a biztonságos egyszeri bejelentkezés

1. Jelentkezzen be a **TOPdesk – biztonságos** rendszergazdaként a vállalati webhely.

2. Az a **TOPdesk** menüben kattintson a **beállítások**.

    ![Beállítások](./media/topdesk-secure-tutorial/ic790598.png "beállításai")

3. Kattintson a **bejelentkezés beállításai**.

    ![Bejelentkezési beállítások](./media/topdesk-secure-tutorial/ic790599.png "bejelentkezési beállítások")

4. Bontsa ki a **bejelentkezési beállítások** menüre, majd **általános**.

    ![Általános](./media/topdesk-secure-tutorial/ic790600.png "általános")

5. Az a **biztonságos** szakaszában a **SAML bejelentkezési** konfigurációs szakaszban, hajtsa végre az alábbi lépéseket:

    ![Műszaki beállítások](./media/topdesk-secure-tutorial/ic790855.png "műszaki beállítások")

    a. Kattintson a **letöltése** töltse le a nyilvános metaadatait tartalmazó fájl, és mentse helyileg a számítógépen.

    b. Nyissa meg a metaadat-fájlt, és keresse meg a **AssertionConsumerService** csomópont.

    ![Helyességi feltétel fogyasztói szolgáltatás](./media/topdesk-secure-tutorial/ic790856.png "helyességi feltétel fogyasztói szolgáltatás")

    c. Másolás a **AssertionConsumerService** értékét, illessze be ezt az értéket a válasz-URL szövegmezőjébe **TOPdesk – biztonságos tartománya és URL-címek** szakaszban.

6. A tanúsítványfájl létrehozásához hajtsa végre az alábbi lépéseket:

    ![Tanúsítvány](./media/topdesk-secure-tutorial/ic790606.png "tanúsítvány")

    a. Az Azure Portalról nyissa meg a letöltött metaadatait tartalmazó fájl.

    b. Bontsa ki a **található Securitytokenservicetype** csomópont, amely rendelkezik egy **xsi: type** , **megváltoztat: ApplicationServiceType**.

    c. Másolja az értéket, a **X509Certificate** csomópont.

    d. Mentse a másolt **X509Certificate** érték helyileg a számítógépen egy fájlban.

7. Az a **nyilvános** területén kattintson **Hozzáadás**.

    ![Adjon hozzá](./media/topdesk-secure-tutorial/ic790607.png "hozzáadása")

8. Az a **SAML-konfigurációja Segéd** párbeszédpanel lapon, a következő lépésekkel:

    ![SAML-konfigurációja Segéd](./media/topdesk-secure-tutorial/ic790608.png "SAML-konfigurációja Segéd")

    a. Az Azure Portalról letöltött metaadatfájl feltöltése a **összevonási metaadatok**, kattintson a **Tallózás**.

    b. A feltölteni a tanúsítványfájlt, **tanúsítvány (RSA)**, kattintson a **Tallózás**.

    c. A **titkos kulcs (RSA, PKCS8, DER)**, feltöltheti saját titkos kulcs, vagy felveheti a kapcsolatot [TOPdesk – biztonságos ügyfél-támogatási csapatának](http://www.topdesk.com/us/support) beolvasni a titkos kulcsot.

    d. A kapott az TOPdesk ügyfélszolgálathoz alatt embléma fájlt feltölteni **embléma ikon**, kattintson a **Tallózás**.

    e. Az a **felhasználói név attribútum** szövegmezőbe írja be `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress`.

    f. Az a **megjelenítendő név** szövegmezőbe írja be a konfiguráció nevét.

    g. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőbe írja be **brittasimon\@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés TOPdesk – biztonságos Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **TOPdesk – biztonságos**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **TOPdesk – biztonságos**.

    ![A TOPdesk – biztonságos hivatkozás alkalmazásainak listájában](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-topdesk---secure-test-user"></a>TOPdesk – biztonságos tesztfelhasználó létrehozása

Ahhoz, hogy TOPdesk - szolgáltatásba való bejelentkezéshez az Azure AD-felhasználók biztonságos, azok ki kell építeni TOPdesk – biztonságos be.  
Esetén TOPdesk – biztonságos, kiépítés a manuális feladat.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a **TOPdesk – biztonságos** rendszergazdaként a vállalati webhely.

2. A felső menüben kattintson **TOPdesk \> új \> támogatófájljait \> operátor**.

    ![Operátor](./media/topdesk-secure-tutorial/ic790610.png "operátor")

3. Az a **új operátor** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Új operátor](./media/topdesk-secure-tutorial/ic790611.png "új operátor")

    a. Kattintson a **általános** fülre.

    b. Az a **Vezetéknév** szövegmezőbe, például a felhasználó vezetékneve típusú **Simon**.

    c. Válassza ki a **hely** a fiókhoz a **helye** szakasz.

    d. Az a **bejelentkezési név** a szövegmezőbe a **TOPdesk bejelentkezési** területén adja meg a felhasználó bejelentkezési nevét.

    e. Kattintson a **Save** (Mentés) gombra.

> [!NOTE]
> Bármely más TOPdesk – biztonságos felhasználói fiók létrehozása eszközök vagy TOPdesk – biztonságos AAD felhasználói fiókok kiépítése által nyújtott API-kat is használhat.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

TOPdesk – a hozzáférési panelen, a biztonságos csempére kattintva, érdemes lehet automatikusan bejelentkezett a TOPdesk – biztonságos, amelynek beállítása egyszeri bejelentkezés. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

