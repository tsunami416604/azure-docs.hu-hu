---
title: 'Oktatóanyag: Azure Active Directory-integráció az első |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés első és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 88270b6d-2571-434a-b139-b6ccc3a2b19f
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: jeedes
ms.openlocfilehash: d0bdf3ff282152f92e1b661bf19768489d1a029b
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39438613"
---
# <a name="tutorial-azure-active-directory-integration-with-front"></a>Oktatóanyag: Azure Active Directory-integráció az előtér

Ebben az oktatóanyagban elsajátíthatja, hogyan előtér integrálása az Azure Active Directory (Azure AD).

Első integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá az első Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Előrehozás (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az első, a következőkre van szükség:

- Az Azure AD-előfizetéshez
- Egy előtér egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Első hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-front-from-the-gallery"></a>Első hozzáadása a katalógusból
Első integrálása az Azure AD beállításához szüksége a katalógus első hozzáadása a felügyelt SaaS-alkalmazások listájában.

**A katalógus első hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **első**, jelölje be **első** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában elöl](./media/front-tutorial/tutorial_front_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés az előtér egy "Britta Simon" nevű tesztelési felhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó elöl mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó elöl hivatkozás kapcsolata kell létrehozni.

Az előtérben hozzárendelése az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az első tesztelése és konfigurálása, hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy előtér-tesztfelhasználót](#create-a-front-test-user)**  – szeretné, hogy egy megfelelője a Britta Simon elöl, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az előtér-alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés konfigurálása az első, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **első** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/front-tutorial/tutorial_front_samlbase.png)

1. Az a **első tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/front-tutorial/tutorial_front_url1.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.frontapp.com`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<companyname>.frontapp.com/sso/saml/callback`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Az értékeket módosítsa a tényleges azonosítója és a válasz URL-cím amelyeket később az oktatóanyagban, vagy forduljon a [első ügyfél-támogatási csapatának](mailto:support@frontapp.com) beolvasni ezeket az értékeket. 

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/front-tutorial/tutorial_front_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/front-tutorial/tutorial_general_400.png)
    
1. Az a **első konfiguráció** területén kattintson **konfigurálja az előtérbeli** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címe, SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/front-tutorial/tutorial_front_configure.png) 

1. Bejelentkezés rendszergazdaként az előtér-bérlővel.

1. Lépjen a **beállítások (fogaskerék ikonra a bal oldali oldalsáv alján) > Beállítások**.
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/front-tutorial/tutorial_front_000.png)

1. Kattintson a **az egyszeri bejelentkezést** hivatkozásra.
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/front-tutorial/tutorial_front_001.png)

1. Válassza ki **SAML** legördülő listájában **az egyszeri bejelentkezést**.
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/front-tutorial/tutorial_front_002.png)

1. Az a **belépési pont** szövegmező értékét put **egyszeri bejelentkezési szolgáltatás URL-cím** az Azure AD alkalmazás-konfigurációs varázsló.
    
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/front-tutorial/tutorial_front_003.png)

1. Nyissa meg a letöltött **Certificate(Base64)** fájlt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **tanúsítvány aláírása** szövegmezőbe.
    
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/front-tutorial/tutorial_front_004.png)

1. Az a **szolgáltató Szolgáltatásbeállítások** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/front-tutorial/tutorial_front_005.png)

    a. Másolja az értéket a **Entitásazonosító** és illessze be azt a **azonosító** szövegmezőjébe **első tartomány és URL-címek** szakaszban az Azure Portalon.

    b. Másolja az értéket a **ACS URL** és illessze be azt a **válasz URL-cím** szövegmezőjébe **első tartomány és URL-címek** szakaszban az Azure Portalon.
    
1. Kattintson a **mentése** gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/front-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/front-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/front-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/front-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-front-test-user"></a>Első tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon elöl nevű felhasználó létrehozásához. Együttműködve [első ügyfél-támogatási csapatának](mailto:support@frontapp.com) a felhasználók hozzáadása az első platformon. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon a hozzáférés biztosításával az első Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése az előtérben, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **első**.

    ![Az alkalmazások listáját az első hivatkozásra](./media/front-tutorial/tutorial_front_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ez a szakasz célja tesztelése az Azure AD SSOconfiguration a hozzáférési panelen.

Ha rákattint az első csempe a hozzáférési panelen, meg kell lekérése automatikusan bejelentkezett az előtér-alkalmazás. 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/front-tutorial/tutorial_general_01.png
[2]: ./media/front-tutorial/tutorial_general_02.png
[3]: ./media/front-tutorial/tutorial_general_03.png
[4]: ./media/front-tutorial/tutorial_general_04.png

[100]: ./media/front-tutorial/tutorial_general_100.png

[200]: ./media/front-tutorial/tutorial_general_200.png
[201]: ./media/front-tutorial/tutorial_general_201.png
[202]: ./media/front-tutorial/tutorial_general_202.png
[203]: ./media/front-tutorial/tutorial_general_203.png

