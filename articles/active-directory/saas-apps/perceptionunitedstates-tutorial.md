---
title: 'Oktatóanyag: Azure Active Directory-integrációval rendelkező Érzete Egyesült Államok (nem-UltiPro) |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a megfigyelés é Egyesült Államok (nem-UltiPro) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: b4a8f026-cb5f-41eb-9680-68eddc33565e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.openlocfilehash: c027aa3d980d22e60c24db68aceb49fbef59d101
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39046178"
---
# <a name="tutorial-azure-active-directory-integration-with-perception-united-states-non-ultipro"></a>Oktatóanyag: Azure Active Directory-integrációval rendelkező Érzete Egyesült Államok (nem-UltiPro)

Ebben az oktatóanyagban elsajátíthatja, hogyan Érzete Egyesült Államok (nem-UltiPro) integrálása az Azure Active Directory (Azure AD).

Megfigyelés é Egyesült Államok (nem-UltiPro) integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, aki hozzáfér a megfigyelés é Egyesült Államok (nem-UltiPro) Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a megfigyelés é Egyesült Államok (nem-UltiPro) (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a megfigyelés é Egyesült Államok (nem-UltiPro), a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A megfigyelés é Egyesült Államok (nem-UltiPro) egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Megfigyelés é Egyesült Államok (nem-UltiPro) hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-perception-united-states-non-ultipro-from-the-gallery"></a>Megfigyelés é Egyesült Államok (nem-UltiPro) hozzáadása a katalógusból
Megfigyelés é Amerikai Egyesült államokbeli (nem-UltiPro) integráció konfigurálásához az Azure AD-be, hozzá kell Érzete Egyesült Államok (nem-UltiPro) a galériából a felügyelt SaaS-alkalmazások listájára.

**Megfigyelés é Egyesült Államok (nem-UltiPro) hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Érzete Egyesült Államok (nem-UltiPro)**, jelölje be **Érzete Egyesült Államok (nem-UltiPro)** eredmény panelen kattintson a **Hozzáadás** gombra kattintva adhat hozzá a az alkalmazás.

    ![Megfigyelés é Egyesült Államok (nem-UltiPro) a találatok listájában](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az Érzete Egyesült Államok (nem-UltiPro) nevű, "Britta Simon" tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a megfigyelés é Egyesült Államok (nem-UltiPro) tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a megfigyelés é Egyesült Államok (nem-UltiPro) hivatkozás kapcsolatát kell létrehozni.

A megfigyelés é Egyesült Államok (nem-UltiPro), rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Érzete Egyesült Államok (nem-UltiPro) tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Megfigyelés é Egyesült Államok (nem-UltiPro) tesztfelhasználó létrehozása](#create-a-perception-united-states-non-ultipro-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon a megfigyelés é Egyesült Államok (nem-UltiPro), amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Érzete Egyesült Államok (nem-UltiPro) alkalmazásában egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása az Érzete Egyesült Államok (nem-UltiPro), hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Érzete Egyesült Államok (nem-UltiPro)** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_samlbase.png)

3. Az a **Érzete Egyesült Államok (nem-UltiPro) tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Megfigyelés é Egyesült Államok (nem-UltiPro) tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_url.png)

    a. Az a **azonosító** szövegmezőbe írja be az URL-cím: `https://perception.kanjoya.com/sp`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://perception.kanjoya.com/sso?idp=<entity_id>`

    > [!NOTE] 
    > Az érték nem valódi. A tényleges válasz URL-címe, amelynek az ismertetése az oktatóanyag későbbi részében fogja frissítse az értéket.
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/perceptionunitedstates-tutorial/tutorial_general_400.png)

6. Az a **Érzete Egyesült Államok (nem-UltiPro) konfigurációs** területén kattintson **Érzete Egyesült államokbeli konfigurálása (nem-UltiPro)** megnyitásához **bejelentkezés konfigurálása** ablak . Másolás a **SAML Entitásazonosító** származó a **gyors útmutató szakaszban.**

    a. A **Érzete Egyesült Államok (nem-UltiPro)** alkalmazáshoz szükséges a **SAML Entitásazonosító** érték, amely másolta, az uri-ként kódolni kell. Az uri-ként kódolandó értéket használja a következő hivatkozásra:**http://www.url-encode-decode.com/**.

    b. Az uri lekérdezése után kódolt érték összevonásához a a **válasz URL-cím** említett alábbi -

    `https://perception.kanjoya.com/sso?idp=<URI encooded entity_id>`
    
    c. Illessze be a fenti értéket a **válasz URL-cím** szövegmezőjébe **Érzete Egyesült Államok (nem-UltiPro) tartomány és URL-címek** szakaszban.

    ![Megfigyelés é Egyesült Államok (nem UltiPro) konfigurációja](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_configure.png) 

7. Egy másik böngészőablakban jelentkezzen be a megfigyelés é Egyesült Államok (nem-UltiPro) vállalati hely rendszergazdaként.

8. Az eszköztáron kattintson **fiókbeállításokat**.

    ![Megfigyelés é Egyesült Államok (nem-UltiPro) felhasználói](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_user.png)

9. Az a **fiókbeállításokat** lapon, a következő lépésekkel:

    ![Megfigyelés é Egyesült Államok (nem-UltiPro) felhasználói](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_account.png)

    a. A a **cégnév** szövegmezőbe írja be a nevét, a **vállalati**.
    
    b. Az a **fióknév** szövegmezőbe írja be a nevét, a **fiók**.

    c. A **alapértelmezett válaszcím E-mail** szöveg írja be az érvényes **E-mail**.

    d. Válassza ki **SSO identitásszolgáltató** , **SAML 2.0-s**.

10. Az a **egyszeri bejelentkezési konfiguráció** lapon, a következő lépésekkel:

    ![Egyesült Államok (nem UltiPro) SSOConfig érzete](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_ssoconfig.png)

    a. Válassza ki **SAML NameID típus** , **E-mail**.

    b. Az a **egyszeri bejelentkezési konfiguráció neve** szövegmezőbe írja be a nevét, a **konfigurációs**.
    
    c. A **identitásszolgáltató neve** szövegmezőjébe illessze be az értéket, **SAML Entitásazonosító**, az Azure Portalról másolt. 

    d. A **SAML tartomány szövegmező**, írja be például a tartomány **@contoso.com**.

    e. Kattintson a **töltse fel újra** feltölteni a **metaadatainak XML** fájlt.

    f. Kattintson a **frissítés**.


> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/perceptionunitedstates-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/perceptionunitedstates-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/perceptionunitedstates-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/perceptionunitedstates-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-perception-united-states-non-ultipro-test-user"></a>Megfigyelés é Egyesült Államok (nem-UltiPro) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon a megfigyelés é Egyesült Államok (nem-UltiPro) nevű felhasználó létrehozásához. Együttműködve [Érzete Egyesült Államok (nem-UltiPro) támogatási csapatának](http://www.ultimatesoftware.com/Contact/ContactUs) a felhasználók hozzáadása a megfigyelés é Egyesült Államok (nem-UltiPro) platform.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon ehhez biztosítson hozzáférést a megfigyelés é Egyesült Államok (nem-UltiPro) Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendelje hozzá a megfigyelés é Egyesült Államok (nem-UltiPro), hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **Érzete Egyesült Államok (nem-UltiPro)**.

    ![A megfigyelés é Egyesült Államok (nem-UltiPro) hivatkozásra az alkalmazások listáját](./media/perceptionunitedstates-tutorial/tutorial_perceptionunitedstates_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a megfigyelés é Egyesült Államok (nem-UltiPro) csempére kattint, meg kell lekérése automatikusan bejelentkezett az Érzete Egyesült Államok (nem-UltiPro) alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/perceptionunitedstates-tutorial/tutorial_general_01.png
[2]: ./media/perceptionunitedstates-tutorial/tutorial_general_02.png
[3]: ./media/perceptionunitedstates-tutorial/tutorial_general_03.png
[4]: ./media/perceptionunitedstates-tutorial/tutorial_general_04.png

[100]: ./media/perceptionunitedstates-tutorial/tutorial_general_100.png

[200]: ./media/perceptionunitedstates-tutorial/tutorial_general_200.png
[201]: ./media/perceptionunitedstates-tutorial/tutorial_general_201.png
[202]: ./media/perceptionunitedstates-tutorial/tutorial_general_202.png
[203]: ./media/perceptionunitedstates-tutorial/tutorial_general_203.png

