---
title: 'Oktatóanyag: Azure Active Directory-integráció az Zendesk |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Zendesk és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 9d7c91e5-78f5-4016-862f-0f3242b00680
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: e5ef74329e2adb6f3b8b60f547231a245a03b1fe
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39050595"
---
# <a name="tutorial-azure-active-directory-integration-with-zendesk"></a>Oktatóanyag: Zendesk-Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Zendesk integrálása az Azure Active Directory (Azure AD).

Zendesk integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a Zendeskhez Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Zendeskhez (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Zendesk, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy Zendesk egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Zendesk hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-zendesk-from-the-gallery"></a>Zendesk hozzáadása a katalógusból
Adja meg Zendesk integrációja az Azure AD-be, szüksége Zendesk hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Adja hozzá a Zendeskhez a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Zendesk**válassza **Zendesk** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![A Zendesk a találatok listájában](./media/zendesk-tutorial/tutorial_zendesk_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Zendesk, a teszt "Britta Simon" nevű felhasználó.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó a Zendeskben mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Zendeskben hivatkozás kapcsolatát kell létrehozni.

Zendesk, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés Zendesk-tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Zendesk-tesztfelhasználót](#create-a-zendesk-test-user)**  – a felhasználó Azure ad-ben reprezentációja kapcsolódó Zendeskben egy megfelelője a Britta Simon rendelkeznie.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Zendesk-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Zendesk, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Zendesk** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/zendesk-tutorial/tutorial_zendesk_samlbase.png)

3. Az a **Zendesk-tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Zendesk-tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/zendesk-tutorial/tutorial_zendesk_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.zendesk.com`

    b. Az a **azonosító** szövegmezőbe írja be az értéket a következő minta használatával: `<subdomain>.zendesk.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [Zendesk-ügyfél-támogatási csapatának](https://support.zendesk.com/hc/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítvány** területén másolja a **UJJLENYOMAT** tanúsítvány értékét.

    ![A tanúsítvány letöltési hivatkozás](./media/zendesk-tutorial/tutorial_zendesk_certificate.png)

5. Zendesk a SAML helyességi feltételek vár egy megadott formátumban. SAML attribútum sem kötelező, de igény szerint hozzáadhat egy attribútum az **felhasználói attribútumok** a következő szakaszban az alábbi lépéseket: 

     ![Egyszeri bejelentkezés konfigurálása](./media/zendesk-tutorial/tutorial_zendesk_attributes1.png)

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása hozzáadása](./media/zendesk-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés addattb konfigurálása](./media/zendesk-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson az **OK** gombra.

    > [!NOTE]
    > A bővítményattribútumok használatával ad hozzá az attribútumokat, amelyek nem szerepelnek az Azure AD alapértelmezés szerint. Kattintson a [felhasználói attribútumok SAML-beállítható](https://support.zendesk.com/hc/en-us/articles/203663676-Using-SAML-for-single-sign-on-Professional-and-Enterprise-) teljes listájának beolvasásához SAML attribútumok, amelyek **Zendesk** fogad el.

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/zendesk-tutorial/tutorial_general_400.png)

7. Az a **Zendesk konfigurációs** területén kattintson **konfigurálása Zendesk** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-CÍMÉT és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Zendesk-konfiguráció](./media/zendesk-tutorial/tutorial_zendesk_configure.png) 

8. Egy másik böngészőablakban jelentkezzen be a Zendesk vállalati hely rendszergazdaként.

9. Kattintson a **rendszergazdai**.

10. Kattintson a bal oldali navigációs ablaktáblában **beállítások**, és kattintson a **biztonsági**.

11. Az a **biztonsági** lapon, a következő lépésekkel: 

     ![Biztonsági](./media/zendesk-tutorial/ic773089.png "biztonsági")

    ![Egyszeri bejelentkezés](./media/zendesk-tutorial/ic773090.png "egyszeri bejelentkezés")

     a. Kattintson a **rendszergazda & ügynökök** fülre.

     b. Válassza ki **egyszeri bejelentkezés (SSO) és az SAML**, majd válassza ki **SAML**.

     c. A **SAML egyszeri bejelentkezési URL-cím** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt. 

     d. A **távoli kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket a **kijelentkezéses URL-cím** Azure Portalról másolt.

     e. A **tanúsítvány-ujjlenyomat** szövegmezőjébe illessze be a **ujjlenyomat** tanúsítvány, amely az Azure Portalról másolt érték.

     f. Kattintson a **Save** (Mentés) gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/zendesk-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/zendesk-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/zendesk-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/zendesk-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-zendesk-test-user"></a>Zendesk tesztfelhasználó létrehozása

Ez a szakasz célja a Zendeskben Britta Simon nevű felhasználó létrehozásához. Zendesk támogatja a felhasználók automatikus átadása, amely alapértelmezés szerint van engedélyezve. További részleteket talál [Itt](zendesk-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

**Ha szeretné manuálisan létrehozni a felhasználói, hajtson végre a következő lépéseket:**

> [!NOTE]
> **A végfelhasználói** automatikusan fiókokat bejelentkezéskor. **Az ügynök** és **rendszergazdai** fiókokat kell manuálisan ki kell építeni **Zendesk** bejelentkezés előtt.

1. Jelentkezzen be a **Zendesk** bérlő.

2. Válassza ki a **Ügyféllistára** fülre.

3. Válassza ki a **felhasználói** fülre, majd **Hozzáadás**.

    ![Felhasználó hozzáadása](./media/zendesk-tutorial/ic773632.png "felhasználó hozzáadása")
4. Írja be a **neve** és **E-mail** egy meglévő Azure AD-fiókot kíván üzembe helyezni, és kattintson a **mentése**.

    ![Új felhasználó](./media/zendesk-tutorial/ic773633.png "új felhasználó")

> [!NOTE]
> Bármely más Zendesk felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését Zendesk által biztosított API-k.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával a Zendeskhez Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon rendel a Zendesk, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **Zendesk**.

    ![A Zendesk-hivatkozás alkalmazásainak listájában](./media/zendesk-tutorial/tutorial_zendesk_app.png)

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Zendesk-csempére kattint, meg kell lekérése automatikusan bejelentkezett a Zendesk-alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Felhasználók átadásának konfigurálása](zendesk-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/zendesk-tutorial/tutorial_general_01.png
[2]: ./media/zendesk-tutorial/tutorial_general_02.png
[3]: ./media/zendesk-tutorial/tutorial_general_03.png
[4]: ./media/zendesk-tutorial/tutorial_general_04.png

[100]: ./media/zendesk-tutorial/tutorial_general_100.png

[200]: ./media/zendesk-tutorial/tutorial_general_200.png
[201]: ./media/zendesk-tutorial/tutorial_general_201.png
[202]: ./media/zendesk-tutorial/tutorial_general_202.png
[203]: ./media/zendesk-tutorial/tutorial_general_203.png
