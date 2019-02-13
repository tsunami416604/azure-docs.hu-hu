---
title: 'Oktatóanyag: Az Azure Active Directory-integráció SilkRoad életre Suite-tal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés SilkRoad életre Suite és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 729287cfcc557e167a70d6f69a5b2b34909df836
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56180980"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Oktatóanyag: Az Azure Active Directory-integráció SilkRoad életre Suite-tal

Ebben az oktatóanyagban elsajátíthatja, hogyan SilkRoad életre Suite integrálható az Azure Active Directory (Azure AD).

SilkRoad életre Suite integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá SilkRoad életre Suite Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett SilkRoad életre Suite (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az SilkRoad életre Suite, a következőkre van szükség:

- Azure AD-előfizetés
- Egy SilkRoad életre Suite egyszeri bejelentkezéses engedélyezett előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. SilkRoad életre Suite hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>SilkRoad életre Suite hozzáadása a katalógusból
Az Azure AD-be SilkRoad életre Suite integrációjának konfigurálása, hozzá kell SilkRoad életre Suite a galériából a felügyelt SaaS-alkalmazások listájára.

**SilkRoad életre Suite hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **SilkRoad életre Suite**, jelölje be **SilkRoad életre Suite** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában SilkRoad életre Suite](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés SilkRoad életre Suite egy "Britta Simon" nevű tesztelési felhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó SilkRoad életre Suite mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó SilkRoad életre Suite hivatkozás kapcsolata kell létrehozni.

SilkRoad életre Suite, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az SilkRoad életre Suite tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy SilkRoad életre Suite tesztfelhasználót](#create-a-silkroad-life-suite-test-user)**  – egy megfelelője a Britta Simon SilkRoad életre csomag, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és SilkRoad életre csomag alkalmazását az egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása SilkRoad életre Suite-tal, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **SilkRoad életre Suite** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_samlbase.png)

1. Az a **SilkRoad életre Suite tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SilkRoad életre Suite tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_url1.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP` |
    | `https://<subdomain>.silkroad.com/Authentication/SP` |

    c. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/` |
    | `https://<subdomain>.silkroad.com/Authentication/` |
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges azonosítóját, válasz URL-cím és bejelentkezési URL-ezeket az értékeket. Kapcsolattartó [SilkRoad életre Suite ügyfél-támogatási csapatának](https://www.silkroad.com/locations/) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/silkroad-life-suite-tutorial/tutorial_general_400.png)
    
1. Az a **SilkRoad életre konfigurálásának** területén kattintson **SilkRoad életre Suite konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![SilkRoad életre Suite konfigurálása](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_configure.png) 

1. Bejelentkezés a SilkRoad vállalati webhelyre rendszergazdaként. 
 
    >[!NOTE] 
    > A Microsoft Azure AD-összevonás konfigurálása a SilkRoad életre Suite hitelesítési alkalmazáshoz való hozzáférést, forduljon SilkRoad támogatása vagy az Ön SilkRoad szolgáltatások képviselőjével.

1. Lépjen a **szolgáltató**, és kattintson a **összevonási részletek**. 
   
    ![Az Azure AD egyszeri bejelentkezés][10]

1. Kattintson a **összevonási metaadatok letöltése**, majd mentse a metaadat-fájlt a számítógépen.
   
    ![Az Azure AD egyszeri bejelentkezés][11] 

1. Az a **SilkRoad** alkalmazást, kattintson a **hitelesítési források**.
   
    ![Az Azure AD egyszeri bejelentkezés][12] 

1. Kattintson a **hitelesítési forrás hozzáadása**. 
   
    ![Az Azure AD egyszeri bejelentkezés][13] 

1. Az a **hitelesítési forrás hozzáadása** szakaszban, hajtsa végre az alábbi lépéseket: 
   
    ![Az Azure AD egyszeri bejelentkezés][14]
  
    a. A **2. lehetőség – metaadatait tartalmazó fájl**, kattintson a **Tallózás** az Azure Portalról letöltött metaadatfájl feltöltése.
  
    b. Kattintson a **fájladatok használatával hozzon létre identitásszolgáltató**.

1. Az a **hitelesítési források** területén kattintson **szerkesztése**. 
    
     ![Az Azure AD egyszeri bejelentkezés][15] 

1. Az a **hitelesítési forrás szerkesztése** párbeszédpanelen hajtsa végre az alábbi lépéseket: 
    
     ![Az Azure AD egyszeri bejelentkezés][16] 

    a. Mint **engedélyezve**válassza **Igen**.

    b. A a **EntityId** szövegmezőjébe illessze be az értéket, **SAML Entitásazonosító** Azure Portalról másolt.
   
    c. Az a **identitásszolgáltató leírás** szövegmezőbe írja be a konfiguráció leírása (például: *Az Azure AD egyszeri bejelentkezés*).

    d. Az a **metaadatait tartalmazó fájl** szövegmezőbe feltöltése a **metaadatok** fájlt, amely az Azure Portalról letöltött.
  
    e. Az a **identitásszolgáltató neve** szövegmezőbe írja be a konfigurációs adott nevét (például: *Azure SP*).
  
    f. Az a **kijelentkezési szolgáltatás URL-címe** szövegmező, illessze be az értéket a **kijelentkezéses URL-cím** Azure Portalról másolt.

    g. Az a **bejelentkezés szolgáltatás URL-címe** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.

    h. Kattintson a **Save** (Mentés) gombra.

1. Tiltsa le minden más hitelesítési forrás. 
    
     ![Az Azure AD egyszeri bejelentkezés][17]

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/silkroad-life-suite-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/silkroad-life-suite-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/silkroad-life-suite-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/silkroad-life-suite-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-silkroad-life-suite-test-user"></a>SilkRoad életre Suite tesztfelhasználó létrehozása

Ebben a szakaszban egy SilkRoad életre Suite Britta Simon nevű felhasználó hoz létre. Együttműködve [SilkRoad életre Suite ügyfél-támogatási csapatának](https://www.silkroad.com/locations/) a felhasználók hozzáadása az SilkRoad életre Suite platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva. 

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával SilkRoad életre Suite Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel SilkRoad életre Suite, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **SilkRoad életre Suite**.

    ![Az alkalmazások listáját a SilkRoad életre Suite hivatkozás](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a SilkRoad életre Suite csempére kattint, meg kell lekérése automatikusan bejelentkezett az SilkRoad életre Suite alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/silkroad-life-suite-tutorial/tutorial_general_04.png

[100]: ./media/silkroad-life-suite-tutorial/tutorial_general_100.png

[200]: ./media/silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/silkroad-life-suite-tutorial/tutorial_general_202.png
[203]: ./media/silkroad-life-suite-tutorial/tutorial_general_203.png
[10]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png
