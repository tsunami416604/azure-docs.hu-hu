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
ms.date: 10/03/2018
ms.author: jeedes
ms.openlocfilehash: 12ab224481c519db36ae21dd11916649ff0bfbe3
ms.sourcegitcommit: f58fc4748053a50c34a56314cf99ec56f33fd616
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2018
ms.locfileid: "48269034"
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

- Azure AD-előfizetés
- Az ArcGIS Online egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.
Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A katalógusból az ArcGIS Online hozzáadása
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-arcgis-online-from-the-gallery"></a>A katalógusból az ArcGIS Online hozzáadása

Az Azure ad-ben az ArcGIS Online integráció konfigurálásához hozzá kell az ArcGIS Online a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a az ArcGIS Online a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![image](./media/arcgis-tutorial/selectazuread.png)

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![image](./media/arcgis-tutorial/a_select_app.png)
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![image](./media/arcgis-tutorial/a_new_app.png)

4. A Keresés mezőbe írja be a **az ArcGIS Online**, jelölje be **az ArcGIS Online** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

     ![image](./media/arcgis-tutorial/a_add_app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az ArcGIS online-nal a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó az ArcGIS Online mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó az ArcGIS Online hivatkozás kapcsolata kell létrehozni.

Az ArcGIS Online hozzárendelése értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Konfigurálása és az Azure AD egyszeri bejelentkezés az ArcGIS online-nal való teszteléséhez hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Hozzon létre egy ArcGIS Online tesztfelhasználót](#create-an-arcgis-online-test-user)**  - a-megfelelője a Britta Simon rendelkezik az ArcGIS online-ban, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az ArcGIS Online alkalmazásban egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása az ArcGIS online-nal, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com/), a a **az ArcGIS Online** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![image](./media/arcgis-tutorial/b1_b2_select_sso.png)

2. Kattintson a **módosítása egyszeri bejelentkezési mód** felett válassza ki a képernyő a **SAML** mód.

      ![image](./media/arcgis-tutorial/b1_b2_saml_ssso.png)

3. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen kattintson **kiválasztása** a **SAML** módot az egyszeri bejelentkezés engedélyezése.

    ![image](./media/arcgis-tutorial/b1_b2_saml_sso.png)

4. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** gombra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![image](./media/arcgis-tutorial/b1-domains_and_urlsedit.png)

5. Az a **alapszintű SAML-konfigurációja** szakaszban, hajtsa végre az alábbi lépéseket:

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be egy URL-címet a következő mintával: `https://<companyname>.maps.arcgis.com`.

    b. Az a **azonosító** szövegmezőbe írja be egy URL-címet a következő mintával: `<companyname>.maps.arcgis.com`.

    ![image](./media/arcgis-tutorial/b1-domains_and_urls.png)

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [az ArcGIS Online ügyfél-támogatási csapatának](http://support.esri.com/en/) beolvasni ezeket az értékeket.

6. Az a **SAML-aláíró tanúsítvány** szakaszt, kattintson a **letöltése** letöltéséhez **összevonási metaadatainak XML** , és mentse az xml-fájlt a számítógépen.

    ![image](./media/arcgis-tutorial/federationxml.png)

7. Automatizálhatja a konfigurációra **az ArcGIS Online**, telepítenie kell **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** kattintva **a bővítmény telepítése**.

    ![image](./media/arcgis-tutorial/install_extension.png)

8. A felvett bővítmény a böngészőre, kattintson a **beállítása az ArcGIS Online** átirányítja azt az ArcGIS Online alkalmazás. Itt adja meg a rendszergazdai hitelesítő adatait az ArcGIS Online-ba való bejelentkezéshez. A webböngésző-bővítmény automatikusan konfigurálja az alkalmazást, és 9-13 lépések automatizálásához.

9. Ha szeretné beállítani az ArcGIS Online manuálisan, nyisson meg egy új böngészőablakban, és jelentkezzen be rendszergazdaként vállalati ArcGIS webhelyét, és hajtsa végre az alábbi lépéseket:

10. Kattintson a **beállítások szerkesztése**.

    ![Beállítások szerkesztése](./media/arcgis-tutorial/ic784742.png "beállításainak szerkesztése")

11. Kattintson a **biztonsági**.

    ![Biztonsági](./media/arcgis-tutorial/ic784743.png "biztonsági")

12. A **vállalati bejelentkezések**, kattintson a **IDENTITÁSSZOLGÁLTATÓ beállítása**.

    ![Vállalati bejelentkezések](./media/arcgis-tutorial/ic784744.png "vállalati bejelentkezések")

13. Az a **identitásszolgáltató beállítása** konfiguráció lapon, a következő lépésekkel:

    ![Állítsa be az identitásszolgáltató](./media/arcgis-tutorial/ic784745.png "identitásszolgáltató beállítása")

    a. Az a **neve** szövegmezőbe írja be a szervezet nevét.

    b. A **metaadatait a vállalati identitásszolgáltató használatával fog adni**válassza **A fájl**.

    c. A letöltött metaadatfájl feltöltése, kattintson a **fájl kiválasztása**.

    d. Kattintson a **SET IDENTITÁSSZOLGÁLTATÓ**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![image](./media/arcgis-tutorial/d_users_and_groups.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![image](./media/arcgis-tutorial/d_adduser.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![image](./media/arcgis-tutorial/d_userproperties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **tulajdonságok**, jelölje be a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Létrehozás** gombra.

### <a name="create-an-arcgis-online-test-user"></a>Az ArcGIS Online tesztfelhasználó létrehozása

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

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**válassza **minden alkalmazás**.

    ![image](./media/arcgis-tutorial/d_all_applications.png)

2. Az alkalmazások listájában jelölje ki a **az ArcGIS Online**.

    ![image](./media/arcgis-tutorial/d_all_application.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![image](./media/arcgis-tutorial/d_leftpaneusers.png)

4. Válassza ki a **Hozzáadás** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![image](./media/arcgis-tutorial/d_assign_user.png)

4. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

5. Az a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelése** gombra.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen az ArcGIS Online csempére kattint, akkor kell lekérése automatikusan bejelentkezett az ArcGIS Online alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



