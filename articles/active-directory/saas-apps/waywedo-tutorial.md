---
title: 'Oktatóanyag: Az Azure Active Directory-integráció módja végzünk az |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a úgy tesszük között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84fc4f36-ecd1-42c6-8a70-cb0f3dc15655
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2018
ms.author: jeedes
ms.openlocfilehash: bc415ec7c577e221a1ab5af585dff5b4fc9ab7dc
ms.sourcegitcommit: c2c64fc9c24a1f7bd7c6c91be4ba9d64b1543231
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2018
ms.locfileid: "39259892"
---
# <a name="tutorial-azure-active-directory-integration-with-way-we-do"></a>Oktatóanyag: Az Azure Active Directory-integráció módja végzünk az

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a úgy tesszük az Azure Active Directory (Azure AD).

Úgy tesszük integrálása az Azure ad-vel nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá, így még a nem Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett, így még a nem (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Úgy tesszük az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A úgy, hogy egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Úgy tesszük hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-way-we-do-from-the-gallery"></a>Úgy tesszük hozzáadása a katalógusból
Az Azure AD-be, így még a nem az integráció konfigurálásához hozzá kell úgy tesszük a galériából a felügyelt SaaS-alkalmazások listájára.

**Úgy tesszük hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **úgy tesszük**válassza **úgy tesszük** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Így még a nem a találatok listájában](./media/waywedo-tutorial/tutorial_waywedo_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés módja még a nem a tesztelési "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználót úgy tesszük mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó úgy tesszük a hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés módja végzünk az tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Úgy tesszük tesztfelhasználó létrehozása](#create-a-way-we-do-test-user)**  – így még a nem kapcsolódó felhasználói reprezentációja az Azure AD-megfelelője a Britta Simon van.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és úgy tesszük alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálni úgy tesszük, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **úgy tesszük** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/waywedo-tutorial/tutorial_waywedo_samlbase.png)

3. Az a **módon azt tegye tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Így azt tegye tartomány és URL-címek egyszeri bejelentkezési adatait](./media/waywedo-tutorial/tutorial_waywedo_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<SUBDOMAIN>.waywedo.com/Authentication/ExternalSignIn`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<SUBDOMAIN>.waywedo.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [módon azt tegye ügyfél-támogatási csapatának](mailto:support@waywedo.com) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **tanúsítvány (Raw)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/waywedo-tutorial/tutorial_waywedo_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/waywedo-tutorial/tutorial_general_400.png)

6. Az a **módon azt tegye konfigurációs** területén kattintson **konfigurálja úgy tesszük** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Úgy tesszük konfiguráció](./media/waywedo-tutorial/tutorial_waywedo_configure.png) 

7. Egy másik böngészőablakban, bejelentkezés módja végzünk egy biztonsági-rendszergazdaként.

8. Kattintson a **személy ikon** az bármely módon végzünk az oldal jobb felső sarkában kattintson a **fiók** a legördülő menüből.

    ![Úgy tesszük fiók](./media/waywedo-tutorial/tutorial_waywedo_account.png) 

9. Kattintson a **menüikonra** nyissa meg a leküldéses navigációs menüben, majd kattintson a **az egyszeri bejelentkezést**.

    ![Úgy tesszük egyetlen](./media/waywedo-tutorial/tutorial_waywedo_single.png)

10. Az a **egyszeri bejelentkezés beállítása** lapon, a következő lépésekkel:

    ![Mentés végzünk módja](./media/waywedo-tutorial/tutorial_waywedo_save.png)

    a. Kattintson a **kapcsolja be az egyszeri bejelentkezés** kapcsolót **Igen** egyszeri bejelentkezés engedélyezéséhez.

    b. Az a **egyetlen bejelentkezési nevet** szövegmezőbe írja be a nevét.

    c. A a **Entitásazonosító** szövegmezőjébe illessze be az értéket, **SAML Entitásazonosító**, az Azure Portalról másolt.

    d. A a **SAML egyszeri bejelentkezési URL-cím** szövegmezőjébe illessze be az értéket, **SAML egyszeri bejelentkezési szolgáltatás URL-**, az Azure Portalról másolt.

    e. Gombra kattintva töltse fel a tanúsítványt a **kiválasztása** megjelenítő gombra **tanúsítvány**.

    f. **Választható beállítások** -
    
    * Engedélyezi a jelszavak – Ha ez a beállítás le van tiltva, a rendszeres jelszó függvények a úgy tesszük, hogy a felhasználók csak használhatják az egyszeri bejelentkezés.

    * Engedélyezze az Automatikus kiépítés – Ha ez engedélyezve van, a bejelentkezéshez használt e-mail-cím lesz automatikusan összehasonlítva úgy tesszük a felhasználók listája. Ha az e-mail-cím nem egyezik meg az aktív felhasználók az úgy tesszük, automatikusan hozzáadja egy új felhasználói fiók jelentkezik be, a hiányzó információit kérő személy.

      > [!NOTE]
      > Egyszeri bejelentkezés során hozzáadott felhasználók általános felhasználói is hozzáadja, és nincsenek hozzárendelve egy szerepkörhöz a rendszerben. Rendszergazdaként nyissa meg, és módosíthatja az ő biztonsági szerepkörük-szerkesztőben vagy rendszergazdaként, és szerepkörök is hozzárendelhet egy vagy több szervezeti diagram. 

    g. Kattintson a **mentése** megőrizni a beállításokat.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/waywedo-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/waywedo-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/waywedo-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/waywedo-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-way-we-do-test-user"></a>Úgy tesszük tesztfelhasználó létrehozása

Ez a szakasz célja úgy tesszük Britta Simon nevű felhasználó létrehozásához. Úgy tesszük támogatja a just-in-time-kiépítés, amely alapértelmezésben engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó próbál hozzáférni a úgy tesszük, ha még nem létezik jön létre.

> [!Note]
> Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [módon azt tegye ügyfél-támogatási csapatának](mailto:support@waywedo.com).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés úgy tesszük az Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel úgy tesszük, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **úgy tesszük**.

    ![Az alkalmazások listáját a úgy tesszük hivatkozás](./media/waywedo-tutorial/tutorial_waywedo_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen úgy tesszük csempére kattint, kell lekérése automatikusan bejelentkezett a úgy tesszük alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/waywedo-tutorial/tutorial_general_01.png
[2]: ./media/waywedo-tutorial/tutorial_general_02.png
[3]: ./media/waywedo-tutorial/tutorial_general_03.png
[4]: ./media/waywedo-tutorial/tutorial_general_04.png

[100]: ./media/waywedo-tutorial/tutorial_general_100.png

[200]: ./media/waywedo-tutorial/tutorial_general_200.png
[201]: ./media/waywedo-tutorial/tutorial_general_201.png
[202]: ./media/waywedo-tutorial/tutorial_general_202.png
[203]: ./media/waywedo-tutorial/tutorial_general_203.png

