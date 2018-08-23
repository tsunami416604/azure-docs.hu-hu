---
title: 'Oktatóanyag: Azure Active Directory-integráció az TOPdesk – biztonságos |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és TOPdesk – biztonságos között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 8e06ee33-18f9-4c05-9168-e6b162079d88
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/07/2018
ms.author: jeedes
ms.openlocfilehash: 8529dfda5ee4a7fc3360f91163b7f5f5bbf6c6ff
ms.sourcegitcommit: 1af4bceb45a0b4edcdb1079fc279f9f2f448140b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "42055288"
---
# <a name="tutorial-azure-active-directory-integration-with-topdesk---secure"></a>Oktatóanyag: Azure Active Directory-integráció az TOPdesk – biztonságos

Ebben az oktatóanyagban elsajátíthatja, hogyan TOPdesk - integrálása az Azure Active Directoryval (Azure AD) biztonságos.

TOPdesk – biztonságos integrálása az Azure ad-vel biztosít a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá TOPdesk – biztonságos Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett TOPdesk – biztonságos (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása TOPdesk – biztonságos, kell a következő elemek:

- Azure AD-előfizetés
- TOPdesk – a biztonságos egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. TOPdesk - Hozzáadás a katalógusból biztonságossá tétele
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-topdesk---secure-from-the-gallery"></a>TOPdesk - Hozzáadás a katalógusból biztonságossá tétele

Biztonságos TOPdesk - integrációjának konfigurálása az Azure AD-be kell TOPdesk hozzáadása – a katalógusból mezőlistához felügyelt SaaS alkalmazások védelme.

**TOPdesk - hozzáadása a katalógusból biztonságos, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **TOPdesk – biztonságos**, jelölje be **TOPdesk – biztonságos** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![TOPdesk – biztonságos a találatok listájában](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés az TOPdesk tesztelése és konfigurálása,-egy "Britta Simon" nevű tesztelési felhasználó biztonságos alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó TOPdesk – biztonságos mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó TOPdesk – biztonságos hivatkozás kapcsolatát kell létrehozni.

TOPdesk – a biztonságos, az értéket a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés TOPdesk – a biztonságos, kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy TOPdesk – biztonságos tesztfelhasználó](#create-a-topdesk---secure-test-user)**  - a-megfelelője a Britta Simon TOPdesk – biztonságos a felhasználó Azure ad-ben reprezentációja kapcsolódó rendelkezik.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a TOPdesk – biztonságos egyszeri bejelentkezés konfigurálása.

**TOPdesk – az Azure AD egyszeri bejelentkezés konfigurálása biztonságos, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **TOPdesk – biztonságos** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_samlbase.png)

3. Az a **TOPdesk – biztonságos tartománya és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![TOPdesk – biztonságos tartomány és URL-címek egyszeri bejelentkezési adatait](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.topdesk.net`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.topdesk.net/tas/secure/login/verify`

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.topdesk.net/tas/public/login/saml`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Válasz URL-cím később az oktatóanyag ismertetése. Kapcsolattartó [TOPdesk – biztonságos ügyfél-támogatási csapatának](http://www.topdesk.com/us/support) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/topdesk-secure-tutorial/tutorial_general_400.png)

6. Az a **TOPdesk - konfigurációja biztonságos** területén kattintson **konfigurálása TOPdesk – biztonságos** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![TOPdesk – biztonságos konfigurációt](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_configure.png)

7. Jelentkezzen be a **TOPdesk – biztonságos** rendszergazdaként a vállalati webhely.

8. Az a **TOPdesk** menüben kattintson a **beállítások**.

    ![Beállítások](./media/topdesk-secure-tutorial/ic790598.png "beállításai")

9. Kattintson a **bejelentkezés beállításai**.

    ![Bejelentkezési beállítások](./media/topdesk-secure-tutorial/ic790599.png "bejelentkezési beállítások")

10. Bontsa ki a **bejelentkezési beállítások** menüre, majd **általános**.

    ![Általános](./media/topdesk-secure-tutorial/ic790600.png "általános")

11. Az a **biztonságos** szakaszában a **SAML bejelentkezési** konfigurációs szakaszban, hajtsa végre az alábbi lépéseket:

    ![Műszaki beállítások](./media/topdesk-secure-tutorial/ic790855.png "műszaki beállítások")

    a. Kattintson a **letöltése** töltse le a nyilvános metaadatait tartalmazó fájl, és mentse helyileg a számítógépen.

    b. Nyissa meg a metaadat-fájlt, és keresse meg a **AssertionConsumerService** csomópont.

    ![Helyességi feltétel fogyasztói szolgáltatás](./media/topdesk-secure-tutorial/ic790856.png "helyességi feltétel fogyasztói szolgáltatás")

    c. Másolás a **AssertionConsumerService** értékét, illessze be ezt az értéket a válasz-URL szövegmezőjébe **TOPdesk – biztonságos tartománya és URL-címek** szakaszban.

12. A tanúsítványfájl létrehozásához hajtsa végre az alábbi lépéseket:

    ![Tanúsítvány](./media/topdesk-secure-tutorial/ic790606.png "tanúsítvány")

    a. Az Azure Portalról nyissa meg a letöltött metaadatait tartalmazó fájl.

    b. Bontsa ki a **található Securitytokenservicetype** csomópont, amely rendelkezik egy **xsi: type** , **megváltoztat: ApplicationServiceType**.

    c. Másolja az értéket, a **X509Certificate** csomópont.

    d. Mentse a másolt **X509Certificate** érték helyileg a számítógépen egy fájlban.

13. Az a **nyilvános** területén kattintson **Hozzáadás**.

    ![Adjon hozzá](./media/topdesk-secure-tutorial/ic790607.png "hozzáadása")

14. Az a **SAML-konfigurációja Segéd** párbeszédpanel lapon, a következő lépésekkel:

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

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/topdesk-secure-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/topdesk-secure-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/topdesk-secure-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/topdesk-secure-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-topdesk---secure-test-user"></a>Hozzon létre egy TOPdesk – biztonságos tesztfelhasználó számára

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

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés TOPdesk – biztonságos Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése TOPdesk – biztonságos, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **TOPdesk – biztonságos**.

    ![A TOPdesk – biztonságos hivatkozás alkalmazásainak listájában](./media/topdesk-secure-tutorial/tutorial_topdesk-secure_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

TOPdesk – a hozzáférési panelen, a biztonságos csempére kattintva meg kell lekérése automatikusan bejelentkezett, a TOPdesk – biztonságos alkalmazást.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/topdesk-secure-tutorial/tutorial_general_01.png
[2]: ./media/topdesk-secure-tutorial/tutorial_general_02.png
[3]: ./media/topdesk-secure-tutorial/tutorial_general_03.png
[4]: ./media/topdesk-secure-tutorial/tutorial_general_04.png

[100]: ./media/topdesk-secure-tutorial/tutorial_general_100.png

[200]: ./media/topdesk-secure-tutorial/tutorial_general_200.png
[201]: ./media/topdesk-secure-tutorial/tutorial_general_201.png
[202]: ./media/topdesk-secure-tutorial/tutorial_general_202.png
[203]: ./media/topdesk-secure-tutorial/tutorial_general_203.png