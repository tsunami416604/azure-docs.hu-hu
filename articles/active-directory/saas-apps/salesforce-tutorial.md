---
title: 'Oktatóanyag: Azure Active Directory-integráció a Salesforce-szal |} A Microsoft Docs'
description: Ismerje meg, hogyan egyszeri bejelentkezés konfigurálása Azure Active Directory és a Salesforce között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d2d7d420-dc91-41b8-a6b3-59579e043b35
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/16/2018
ms.author: jeedes
ms.openlocfilehash: 64b94baeaede9b05e953b69324648c63d97cea8e
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39045438"
---
# <a name="tutorial-azure-active-directory-integration-with-salesforce"></a>Oktatóanyag: Azure Active Directory-integráció a Salesforce-szal

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a Salesforce-hoz az Azure Active Directory (Azure AD).

Az Azure AD integrálása a Salesforce nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a Salesforce-hoz az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Salesforce-hoz (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Salesforce-szal, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A Salesforce egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A Salesforce hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-salesforce-from-the-gallery"></a>A Salesforce hozzáadása a katalógusból
Konfigurálhatja az Azure AD integrálása a Salesforce, hozzá kell Salesforce a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Salesforce-hoz a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Salesforce**, jelölje be **Salesforce** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában a Salesforce-ban](./media/salesforce-tutorial/tutorial_salesforce_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Salesforce, a teszt "Britta Simon" nevű felhasználó.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a Salesforce-ban a partner felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval hivatkozás kapcsolatot egy Azure AD-felhasználót és a kapcsolódó felhasználó között a Salesforce-ban kell létrehozni.

A Salesforce-ban, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a Salesforce-szal tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy Salesforce tesztfelhasználót](#create-a-salesforce-test-user)**  - a-megfelelője a Britta Simon rendelkezik a Salesforce-ban, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Salesforce alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés, a Salesforce-szal, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Salesforce** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/salesforce-tutorial/tutorial_salesforce_samlbase.png)

3. Az a **Salesforce tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Salesforce-tartománnyal és URL-címek egyszeri bejelentkezési adatait](./media/salesforce-tutorial/tutorial_salesforce_url.png)
    
    a. Az a **bejelentkezési URL-** szövegmezőbe írja be az értéket a következő minta használatával:
    
    Vállalati fiók: `https://<subdomain>.my.salesforce.com`

    Fejlesztői fiók: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    b. Az a **azonosító** szövegmezőbe írja be az értéket a következő minta használatával:
    
    Vállalati fiók: `https://<subdomain>.my.salesforce.com`

    Fejlesztői fiók: `https://<subdomain>-dev-ed.my.salesforce.com`
    
    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL-cím és azonosító. Kapcsolattartó [a Salesforce-ügyfél-támogatási csapatának](https://help.salesforce.com/support) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/salesforce-tutorial/tutorial_salesforce_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/salesforce-tutorial/tutorial_general_400.png)

6. Az a **Salesforce konfigurációs** területén kattintson **konfigurálása Salesforce** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Salesforce-konfiguráció](./media/salesforce-tutorial/tutorial_salesforce_configure.png) 

7. Nyissa meg egy új lapot a böngészőben, és a Salesforce-rendszergazdai fiókjával való bejelentkezéshez.

8. Kattintson a a **telepítő** alatt **beállítások ikon** az oldal jobb felső sarkában található.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/configure1.png)

9. Görgessen le a **beállítások** navigációs ablaktábláján kattintson **identitás** a kapcsolódó szakasz kibontásához. Kattintson a **egyszeri bejelentkezési beállításainak**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-admin-sso.png)

10. Az a **egyszeri bejelentkezési beállításainak** lap, kattintson a **szerkesztése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-admin-sso-edit.png)
    
    > [!NOTE]
    > Ha Ön nem lehet engedélyezni az egyszeri bejelentkezés beállításainak Salesforce-fiókjához, szükség lehet kapcsolódni a [a Salesforce-ügyfél-támogatási csapatának](https://help.salesforce.com/support). 

11. Válassza ki **SAML engedélyezett**, és kattintson a **mentése**.

      ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-enable-saml.png)
12. Az SAML egyszeri bejelentkezési beállításainak konfigurálásához kattintson **új**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-admin-sso-new.png)

13. Az a **SAML egyszeri bejelentkezési beállítás szerkesztése** lapon, győződjön meg arról, a következő beállításokat:

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-saml-config.png)

    a. Az a **neve** mezőben adja meg egy rövid nevet ehhez a konfigurációhoz. Amely egy értéket a **neve** automatikusan feltölti a **API neve** szövegmezőbe.

    b. Az a **kibocsátó** mezőbe illessze be az értéket a **SAML Entitásazonosító**, az Azure Portalról másolt.

    c. Az a **entitásazonosító szövegmező**, írja be a Salesforce-tartomány nevét, a következő minta használatával:
      
      * Vállalati fiók: `https://<subdomain>.my.salesforce.com`
      * Fejlesztői fiók: `https://<subdomain>-dev-ed.my.salesforce.com`
      
    d. Feltölteni a **Identitástanúsítványt szolgáltató**, kattintson a **fájl kiválasztása** keresse meg és válassza ki a tanúsítványfájlt, amely már letöltötte az Azure Portalról.

    e. Mint **SAML identitástípus**, a következő lehetőségek közül választhat:
    
      * Válassza ki **helyességi feltétel tartalmaz a Salesforce felhasználónév**, ha a felhasználó Salesforce felhasználónév kerül átadásra a SAML helyességi feltétel

      * Válassza ki **helyességi feltétel tartalmazza a felhasználói objektum összevonási Azonosítójára**, ha az összevonási Azonosítót a felhasználói objektum a kerül átadásra a SAML helyességi feltétel

      * Válassza ki **helyességi feltétel tartalmazza a felhasználói objektum használata Azonosítójára**, ha a felhasználói Azonosítót a felhasználói objektum a kerül átadásra a SAML helyességi feltétel

    f. A **SAML identitás hely**válassza **identitás a tulajdonos utasítás NameIdentifier elemében van**.

    g. A **szolgáltató által kezdeményezett kérelem Szolgáltatáskötést**válassza **HTTP-átirányítás**.

    h. A **Identity Provider bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt
    
    i. Végül kattintson **mentése** a SAML egyszeri bejelentkezési beállítások alkalmazásához.

14. Kattintson a bal oldali navigációs ablaktáblán, a Salesforce-ban, **vállalati beállítások** bontsa ki a kapcsolódó csomópontot, majd **saját tartomány**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-my-domain.png)

15. Görgessen le a **hitelesítési konfiguráció** szakaszt, és kattintson a **szerkesztése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-edit-auth-config.png)

16. Az a **hitelesítési konfiguráció** szakaszt, ellenőrizze a **bejelentkezési oldal** , **hitelesítés hogyan listázhatja** a SAML SSO-konfigurációt, és kattintson a  **Mentés**.

    ![Egyszeri bejelentkezés konfigurálása](./media/salesforce-tutorial/sf-auth-config.png)

    > [!NOTE]
    > Ha egynél több hitelesítési szolgáltatás ki van jelölve, felhasználók mely hitelesítési szolgáltatás, például a bejelentkezéshez egyszeri bejelentkezéshez, a Salesforce-környezet inicializálása során való megadását kéri. Ha nem szeretné, hogy fordulhat elő, akkor meg kell **ne jelölje be minden más hitelesítési szolgáltatás**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/salesforce-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/salesforce-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/salesforce-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/salesforce-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-a-salesforce-test-user"></a>A Salesforce tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó jön létre a Salesforce-ban. A Salesforce támogatja a just-in-time-kiépítés, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik, a Salesforce-ban, egy új létrejön, amikor megpróbálja elérni a Salesforce. Salesforce-hoz is támogatja a felhasználók automatikus átadása, további részleteket talál [Itt](salesforce-provisioning-tutorial.md) konfigurálásának a felhasználók automatikus átadása.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használandó Azure egyszeri bejelentkezést a hozzáférés biztosításával a Salesforce-hoz.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon rendel a Salesforce-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listájában jelölje ki a **Salesforce**.

    ![A Salesforce-hivatkozás alkalmazásainak listájában](./media/salesforce-tutorial/tutorial_salesforce_app.png)

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panel a Salesforce csempére kattint, akkor kell lekérése automatikusan bejelentkezett a Salesforce alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)
* [Felhasználók átadásának konfigurálása](salesforce-provisioning-tutorial.md)

<!--Image references-->

[1]: ./media/salesforce-tutorial/tutorial_general_01.png
[2]: ./media/salesforce-tutorial/tutorial_general_02.png
[3]: ./media/salesforce-tutorial/tutorial_general_03.png
[4]: ./media/salesforce-tutorial/tutorial_general_04.png

[100]: ./media/salesforce-tutorial/tutorial_general_100.png

[200]: ./media/salesforce-tutorial/tutorial_general_200.png
[201]: ./media/salesforce-tutorial/tutorial_general_201.png
[202]: ./media/salesforce-tutorial/tutorial_general_202.png
[203]: ./media/salesforce-tutorial/tutorial_general_203.png
