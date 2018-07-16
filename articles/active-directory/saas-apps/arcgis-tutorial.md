---
title: 'Oktatóanyag: Azure Active Directory-integráció az ArcGIS online-nal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az ArcGIS Online között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: a9e132a4-29e7-48bf-beb9-4148e617c8a9
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/13/2017
ms.author: jeedes
ms.openlocfilehash: de41838b95b524780ca9df56ff036a200fd5f73a
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/14/2018
ms.locfileid: "39043703"
---
# <a name="tutorial-azure-active-directory-integration-with-arcgis-online"></a>Oktatóanyag: Azure Active Directory-integráció az ArcGIS online-nal

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható az ArcGIS Online az Azure Active Directoryval (Azure AD).

Az Azure AD integrálása az ArcGIS Online nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá az ArcGIS Online az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az ArcGIS online-hoz (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az ArcGIS online-nal, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy ArcGIS Online egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A katalógusból az ArcGIS Online hozzáadása
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-arcgis-online-from-the-gallery"></a>A katalógusból az ArcGIS Online hozzáadása
Az Azure ad-ben az ArcGIS Online integráció konfigurálásához hozzá kell az ArcGIS Online a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a az ArcGIS Online a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **az ArcGIS Online**, jelölje be **az ArcGIS Online** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az ArcGIS Online, a találatok listájában](./media/arcgis-tutorial/tutorial_arcgisonline_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az ArcGIS online-nal a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó az ArcGIS Online mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó az ArcGIS Online hivatkozás kapcsolata kell létrehozni.

Az ArcGIS Online hozzárendelése értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Konfigurálása és az Azure AD egyszeri bejelentkezés az ArcGIS online-nal való teszteléséhez hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy ArcGIS Online tesztfelhasználót](#create-a-arcgis-online-test-user)**  - a-megfelelője a Britta Simon rendelkezik az ArcGIS online-ban, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az ArcGIS Online alkalmazásban egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása az ArcGIS online-nal, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **az ArcGIS Online** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/arcgis-tutorial/tutorial_arcgisonline_samlbase.png)

3. Az a **az ArcGIS Online tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Az ArcGIS Online tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/arcgis-tutorial/tutorial_arcgisonline_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.maps.arcgis.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `<companyname>.maps.arcgis.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [az ArcGIS Online ügyfél-támogatási csapatának](http://support.esri.com/en/) beolvasni ezeket az értékeket. 
 


4. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/arcgis-tutorial/tutorial_arcgisonline_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/arcgis-tutorial/tutorial_general_400.png)

6. Egy másik böngészőablakban jelentkezzen be az ArcGIS vállalati hely rendszergazdaként.

7. Kattintson a **beállítások szerkesztése**.

    ![Beállítások szerkesztése](./media/arcgis-tutorial/ic784742.png "beállításainak szerkesztése")

8. Kattintson a **biztonsági**.

    ![Biztonsági](./media/arcgis-tutorial/ic784743.png "biztonsági")

9. A **vállalati bejelentkezések**, kattintson a **IDENTITÁSSZOLGÁLTATÓ beállítása**.

    ![Vállalati bejelentkezések](./media/arcgis-tutorial/ic784744.png "vállalati bejelentkezések")

10. Az a **identitásszolgáltató beállítása** konfiguráció lapon, a következő lépésekkel:
   
    ![Állítsa be az identitásszolgáltató](./media/arcgis-tutorial/ic784745.png "identitásszolgáltató beállítása")
   
    a. Az a **neve** szövegmezőbe írja be a szervezet nevét.

    b. A **metaadatait a vállalati identitásszolgáltató használatával fog adni**válassza **A fájl**.

    c. A letöltött metaadatfájl feltöltése, kattintson a **fájl kiválasztása**.

    d. Kattintson a **SET IDENTITÁSSZOLGÁLTATÓ**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/arcgis-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/arcgis-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/arcgis-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/arcgis-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-arcgis-online-test-user"></a>Az ArcGIS Online tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók az ArcGIS Online-ba való bejelentkezéshez, akkor ki kell építeni az ArcGIS Online szolgáltatásba.  
Az ArcGIS Online esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **ArcGIS** bérlő.

2. Kattintson a **tagok MEGHÍVÁSA**.
   
    ![A tagok meghívása](./media/arcgis-tutorial/ic784747.png "tagok meghívása")

3. Válassza ki **tagok hozzáadása automatikusan elküldése e-mailt nélkül**, és kattintson a **tovább**.
   
    ![Tagok hozzáadása automatikusan](./media/arcgis-tutorial/ic784748.png "automatikusan tagok hozzáadása")

4. Az a **tagok** párbeszédpanel lapon, a következő lépésekkel:
   
     ![Adja hozzá, és tekintse át](./media/arcgis-tutorial/ic784749.png "hozzáadása és áttekintése")
    
     a. Adja meg a **E-mail**, **Utónév**, és **Vezetéknév** kiépítendő érvényes AAD-fiókok.
  
     b. Kattintson a **hozzáadása és felülvizsgálati**.
5. Tekintse át az adatokat a megadott, és kattintson a **tagok hozzáadása**.
   
    ![Tag hozzáadása](./media/arcgis-tutorial/ic784750.png "tag hozzáadása")
        
    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a egy hivatkozásra kattintva a fiók megerősítéséhez, mielőtt aktívvá válik.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon az ArcGIS online-hoz való hozzáférés biztosításával Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon az ArcGIS online-hoz való hozzárendeléséhez végezze el az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **az ArcGIS Online**.

    ![Az alkalmazások listáját az ArcGIS Online hivatkozás](./media/arcgis-tutorial/tutorial_arcgisonline_app.png)  

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen az ArcGIS Online csempére kattint, akkor kell lekérése automatikusan bejelentkezett az ArcGIS Online alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/arcgis-tutorial/tutorial_general_01.png
[2]: ./media/arcgis-tutorial/tutorial_general_02.png
[3]: ./media/arcgis-tutorial/tutorial_general_03.png
[4]: ./media/arcgis-tutorial/tutorial_general_04.png

[100]: ./media/arcgis-tutorial/tutorial_general_100.png

[200]: ./media/arcgis-tutorial/tutorial_general_200.png
[201]: ./media/arcgis-tutorial/tutorial_general_201.png
[202]: ./media/arcgis-tutorial/tutorial_general_202.png
[203]: ./media/arcgis-tutorial/tutorial_general_203.png

