---
title: 'Oktatóanyag: Azure Active Directoryval integrált Cisco Spark |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Cisco Spark között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: c47894b1-f5df-4755-845d-f12f4c602dc4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2017
ms.author: jeedes
ms.openlocfilehash: 634fdc22bd4fa6b35fd20a0af4adc23865068106
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35927755"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-spark"></a>Oktatóanyag: Azure Active Directoryval integrált Cisco Spark

Ebben az oktatóanyagban elsajátíthatja Cisco Spark integrálása az Azure Active Directory (Azure AD).

Cisco Spark integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Cisco Spark hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Cisco Spark (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Cisco Spark az Azure AD-integrációs konfigurálásához a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A Cisco Spark egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Cisco Spark hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-cisco-spark-from-the-gallery"></a>Cisco Spark hozzáadása a gyűjteményből
Az Azure AD integrálása a Cisco Spark konfigurálásához kell hozzáadnia Cisco Spark a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Cisco Spark hozzáadása a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Cisco Spark**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/cisco-spark-tutorial/tutorial_ciscospark_search.png)

5. Az eredmények panelen válassza ki a **Cisco Spark**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/cisco-spark-tutorial/tutorial_ciscospark_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a Cisco Spark "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Cisco Spark a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Cisco Spark közötti kapcsolat kapcsolatot kell létrehozni.

A Cisco Spark, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Cisco Spark tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Cisco Spark tesztfelhasználó létrehozása](#creating-a-cisco-spark-test-user)**  - való Britta Simon egy megfelelője a Cisco Spark, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Cisco Spark alkalmazásban egyszeri bejelentkezés konfigurálása.

**Cisco Spark az Azure AD az egyszeri bejelentkezés konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Cisco Spark** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_ciscospark_samlbase.png)

3. Az a **Cisco Spark tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_ciscospark_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg az URL-címet: `https://web.ciscospark.com/#/signin`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://idbroker.webex.com/<companyname>`

    > [!NOTE] 
    > Ez az érték nincs valós. Frissítse ezt az értéket a tényleges azonosítója. Ügyfél [Cisco Spark ügyfél-támogatási csoport](https://support.ciscospark.com/) lekérni ezt az értéket. 
 
4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_ciscospark_certificate.png) 

5. Cisco Spark alkalmazás vár a SAML helyességi feltételek megadott attribútumokat tartalmaz. Konfigurálja a következő attribútumok ehhez az alkalmazáshoz. Ezek az attribútumok értékének kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_ciscospark_07.png) 

6. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve  | Attribútum értéke |
    | --------------- | -------------------- |    
    |   egyedi azonosítója    | User.userPrincipalName |   

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.
    
    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson az **OK** gombra.

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_general_400.png)

8. Jelentkezzen be [Cisco Cloud Collaboration felügyeleti](https://admin.ciscospark.com/) a teljes körű rendszergazdai hitelesítő adataival.

9. Válassza ki **beállítások** alatt pedig a **hitelesítési** területén kattintson **módosítás**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_cisco_spark_10.png)
    
10. Válassza ki **a 3. fél identitásszolgáltató integrálását. (Speciális)**  , és navigáljon a következő képernyő.

11. Az a **Idp metaadatok importálása** lapon, vagy húzza és dobja el az Azure AD-metaadatfájl a lapra, vagy a fájl böngésző kapcsoló használatával keresse meg és töltse fel az Azure AD-metaadatait tartalmazó fájl. Ezt követően válassza **szükséges metaadatokat (biztonságosabb) a hitelesítésszolgáltató által aláírt tanúsítvány** kattintson **következő**. 
    
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_cisco_spark_11.png)

12. Válassza ki **SSO-kapcsolat tesztelése**, és egy új böngészőlapon nyílik meg, amikor hitelesítéséhez az Azure ad-vel történő bejelentkezéssel.

13. Lépjen vissza a **Cisco Cloud Collaboration felügyeleti** böngészőlapon. Ha a teszt sikeres volt, válassza ki a **Ez a teszt sikeres volt. Engedélyezi az egyszeri bejelentkezés beállítást** kattintson **következő**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/cisco-spark-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/cisco-spark-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/cisco-spark-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/cisco-spark-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-cisco-spark-test-user"></a>Cisco Spark tesztfelhasználó létrehozása

Ebben a szakaszban egy Cisco Spark Britta Simon nevű felhasználót hoz létre. Ebben a szakaszban egy Cisco Spark Britta Simon nevű felhasználót hoz létre.

1. Lépjen a [Cisco Cloud Collaboration felügyeleti](https://admin.ciscospark.com/) a teljes körű rendszergazdai hitelesítő adataival.

2. Kattintson a **felhasználók** , majd **felhasználók kezelése**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_cisco_spark_12.png) 

3. Az a **kezelése felhasználó** ablakban válassza ki **manuális hozzáadása vagy módosítása a felhasználók** kattintson **következő**.

4. Válassza ki **nevek és E-mail cím**. Ezután adja meg a szövegmező az alábbiak szerint:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_cisco_spark_13.png) 
    
    a. Az a **Utónév** szövegmezőhöz típus **Britta**. 
    
    b. Az a **Vezetéknév** szövegmezőhöz típus **Simon**.
    
    c. Az a **E-mail cím** szövegmezőhöz típus **britta.simon@contoso.com**.

5. Kattintson a plusz jelre Britta Simon hozzáadni. Ezután kattintson a **Tovább** gombra.

6. Az a **szolgáltatások hozzáadása a felhasználók** ablak, kattintson a **mentése** , majd **Befejezés**.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Cisco Spark az Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Cisco Spark Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Cisco Spark**.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-spark-tutorial/tutorial_ciscospark_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a hozzáférési panelen az Azure AD SSO-konfigurációjának tesztelése.

Ha a hozzáférési panelen a Cisco Spark csempére kattint, akkor kell beolvasása automatikusan bejelentkezett a Cisco Spark alkalmazásba.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

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

