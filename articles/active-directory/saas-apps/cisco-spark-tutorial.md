---
title: 'Oktatóanyag: Azure Active Directory-integráció a Cisco Spark |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Cisco Spark között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: bebc8d674d7448ea0ce6a1f11b7ae80335df9cdc
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39431698"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Oktatóanyag: Azure Active Directory-integráció a Cisco Spark segítségével

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a Cisco Spark az Azure Active Directory (Azure AD).

Cisco Spark integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá a Cisco Spark az Azure AD-ben
- Az Azure AD-fiókjukat engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Cisco Spark (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Cisco Spark, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- A Cisco Spark egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Cisco Spark hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-cisco-spark-from-the-gallery"></a>Cisco Spark hozzáadása a katalógusból
Cisco Spark integrálása az Azure AD beállítása, hozzá kell Cisco Spark a galériából a felügyelt SaaS-alkalmazások listájára.

**Cisco Spark hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **Cisco Spark**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cisco-spark-tutorial/tutorial_ciscospark_search.png)

1. Az eredmények panelen válassza ki a **Cisco Spark**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cisco-spark-tutorial/tutorial_ciscospark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban konfigurálni, és a Cisco Spark, a teszt "Britta Simon." nevű felhasználó az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a Cisco Spark tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Cisco Spark hivatkozás kapcsolatát kell létrehozni.

Cisco Spark, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés a Cisco Spark tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Cisco Spark tesztfelhasználó létrehozása](#creating-a-cisco-spark-test-user)**  – egy megfelelője a Britta Simon Cisco Spark, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Cisco Spark-alkalmazás az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálásához a Cisco Spark, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Cisco Spark** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_ciscospark_samlbase.png)

1. Az a **Cisco Spark tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_ciscospark_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-cím: `https://web.ciscospark.com/#/signin`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://idbroker.webex.com/<companyname>`

    > [!NOTE] 
    > Az érték nem valódi. Ez az érték frissítse a tényleges azonosítója. Kapcsolattartó [Cisco Spark ügyfél-támogatási csapatának](https://support.ciscospark.com/) lekérni ezt az értéket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_ciscospark_certificate.png) 

1. Cisco Spark-alkalmazás vár a SAML helyességi feltételek adott attribútumok tartalmazza. Konfigurálja a következő attribútumok ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_ciscospark_07.png) 

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve  | Attribútum értéke |
    | --------------- | -------------------- |    
    |   egyedi azonosítója    | User.userPrincipalName |   

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson az **OK** gombra.

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_general_400.png)

1. Jelentkezzen be a [Cisco együttműködési Felhőfelügyelet](https://admin.ciscospark.com/) a teljes körű rendszergazdai hitelesítő adataival.

1. Válassza ki **beállítások** és a **hitelesítési** területén kattintson **módosítás**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
    
1. Válassza ki **integrálása egy 3. fél identitásszolgáltatót. (Speciális)**  , és folytassa a következő képernyőn.

1. Az a **Idp-metaadatok importálása** oldalon, vagy húzza át, és dobja el az Azure ad-ben metaadatait tartalmazó fájl a lapra, vagy a fájl a böngésző lehetőséget használva keresse meg és az Azure AD-metaadatfájl feltöltésére. Ezután válassza ki **szükséges metaadatokat (biztonságosabb) a hitelesítésszolgáltató által aláírt tanúsítvány** kattintson **tovább**. 
    
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

1. Válassza ki **SSO-kapcsolat tesztelése**, és a egy új böngészőlapon nyílik meg, amikor hitelesítéshez az Azure AD-bejelentkezés.

1. Lépjen vissza a **Cisco együttműködési Felhőfelügyelet** böngészőlapon. Ha a teszt sikeres volt, válassza ki a **a teszt sikeres volt. Egyszeri bejelentkezés a beállítás engedélyezése** kattintson **tovább**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cisco-spark-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cisco-spark-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cisco-spark-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/cisco-spark-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-cisco-spark-test-user"></a>Cisco Spark tesztfelhasználó létrehozása

Ebben a szakaszban egy Cisco Spark Britta Simon nevű felhasználó létrehozásához. Ebben a szakaszban egy Cisco Spark Britta Simon nevű felhasználó létrehozásához.

1. Nyissa meg a [Cisco együttműködési Felhőfelügyelet](https://admin.ciscospark.com/) a teljes körű rendszergazdai hitelesítő adataival.

1. Kattintson a **felhasználók** , majd **felhasználók kezelése**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

1. Az a **kezelése felhasználó** ablakban válassza **manuális hozzáadása, vagy módosítsa a felhasználók** kattintson **tovább**.

1. Válassza ki **nevek és E-mail-cím**. Ezután adja meg a szövegmezőben, hogy a következő:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 
    
    a. Az a **Utónév** szövegmezőbe írja be **Britta**. 
    
    b. Az a **Vezetéknév** szövegmezőbe írja be **Simon**.
    
    c. Az a **E-mail-cím** szövegmezőbe írja be **britta.simon@contoso.com**.

1. Kattintson a plusz jelre Britta Simon hozzáadásához. Ezután kattintson a **Tovább** gombra.

1. Az a **szolgáltatás hozzáadása a felhasználók számára** ablakban kattintson a **mentése** , majd **Befejezés**.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával Cisco Spark Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Cisco Spark Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Cisco Spark**.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_ciscospark_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a a hozzáférési Panel használatával az Azure AD egyszeri bejelentkezési konfiguráció tesztelése.

Ha a hozzáférési panelen a Cisco Spark csempére kattint, akkor kell lekérése automatikusan bejelentkezett, a Cisco Spark-alkalmazás.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-spark-tutorial/tutorial_general_01.png
[2]: ./media/cisco-spark-tutorial/tutorial_general_02.png
[3]: ./media/cisco-spark-tutorial/tutorial_general_03.png
[4]: ./media/cisco-spark-tutorial/tutorial_general_04.png
[10]: ./media/cisco-spark-tutorial/tutorial_general_060.png
[100]: ./media/cisco-spark-tutorial/tutorial_general_100.png

[200]: ./media/cisco-spark-tutorial/tutorial_general_200.png
[201]: ./media/cisco-spark-tutorial/tutorial_general_201.png
[202]: ./media/cisco-spark-tutorial/tutorial_general_202.png
[203]: ./media/cisco-spark-tutorial/tutorial_general_203.png

