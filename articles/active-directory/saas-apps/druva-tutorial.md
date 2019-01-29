---
title: 'Oktatóanyag: Az Azure Active Directory-integrációt kínál a Druva együtt |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Druva között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: ab92b600-1fea-4905-b1c7-ef8e4d8c495c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/23/2017
ms.author: jeedes
ms.openlocfilehash: 0429a7d0845b8590570dfb8ffc513ca3bb0ce998
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55195946"
---
# <a name="tutorial-azure-active-directory-integration-with-druva"></a>Oktatóanyag: Az Azure Active Directory-integrációt kínál a Druva együtt

Ebben az oktatóanyagban elsajátíthatja, hogyan kínál a Druva integrálható az Azure Active Directory (Azure AD).

Az Azure AD integrálása a Druva nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a Druva Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a Druva (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Druva együtt, a következőkre van szükség:

- Azure AD-előfizetés
- A Druva egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Druva hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-druva-from-the-gallery"></a>Druva hozzáadása a katalógusból
Az Azure AD integrálása a Druva konfigurálásához hozzá kell kínál a Druva a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a Druva a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **kínál a Druva**, jelölje be **kínál a Druva** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában a Druva](./media/druva-tutorial/tutorial_druva_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezést kínál a Druva a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a Druva tartozó felhasználó Mi az a felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Druva hivatkozás kapcsolatát kell létrehozni.

Druva, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést kínál a Druva együtt tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy kínál a Druva tesztfelhasználót](#create-a-druva-test-user)**  - a-megfelelője a Britta Simon szerepel, amely kapcsolódik az Azure AD felhasználói ábrázolása Druva.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon és a Druva alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezést kínál a Druva, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **kínál a Druva** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/druva-tutorial/tutorial_druva_samlbase.png)

1. Az a **kínál a Druva tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/druva-tutorial/tutorial_druva_url.png)

    Az a **azonosító** szövegmezőbe írja be a karakterláncot: `druva-cloud`
    
1. Ellenőrizze **speciális URL-beállítások megjelenítése**. Ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Egyszeri bejelentkezés konfigurálása](./media/druva-tutorial/tutorial_druva_url1.png)
    
    Az a **bejelentkezési URL-** szövegmezőbe írja be az URL-cím: `https://cloud.druva.com/home`

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/druva-tutorial/tutorial_druva_certificate.png) 

1. A Druva alkalmazás a SAML helyességi feltételek vár egy megadott formátumban, amelyhez egyéni attribútumleképezések való hozzáadása a **SAML-jogkivonat attribútumai** konfigurációja. 

    ![Egyszeri bejelentkezés konfigurálása](./media/druva-tutorial/tutorial_druva_attribute.png)

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az előző képen látható módon, és hajtsa végre az alábbi lépéseket:

    | Attribútum neve      | Attribútum értéke      |
    | ------------------- | -------------------- |
    | insync\_auth\_jogkivonat |Adja meg a token létrehozott értéket |
    
    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/druva-tutorial/tutorial_attribute_04.png)
    
    ![Egyszeri bejelentkezés konfigurálása](./media/druva-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket. Az oktatóanyag későbbi kifejtett a token létrehozott értéket.
    
    d. Kattintson az **OK** gombra.    

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/druva-tutorial/tutorial_general_400.png)

1. Az a **kínál a Druva konfigurációs** területén kattintson **konfigurálása kínál a Druva** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-CÍMÉT és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/druva-tutorial/tutorial_druva_configure.png) 

1. Egy másik böngészőablakban jelentkezzen be a Druva vállalati hely rendszergazdaként.

1. Lépjen a **kezelése \> beállítások**.

    ![Beállítások](./media/druva-tutorial/ic795091.png "beállításai")

1. Egyszeri bejelentkezés beállításai párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés beállításai](./media/druva-tutorial/ic795092.png "egyszeri bejelentkezés beállításai")
    
    a. A **azonosító szolgáltató bejelentkezési URL-cím** szövegmezőbe, illessze be az értéket a **egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.
        
    b. A **azonosító szolgáltató kijelentkezési URL-címe** szövegmező, illessze be az értéket a **kijelentkezéses URL-cím**, az Azure Portalról másolt
        
    c. Nyissa meg a base-64 kódolású tanúsítványt a Jegyzettömbben, a tartalmát a vágólapra másolja és illessze be azt a **azonosító társzolgáltató tanúsítványának** szövegmező
     
    d. Megnyitásához a **beállítások** kattintson **mentése**.

1. Az a **beállítások** kattintson **egyszeri bejelentkezési Token létrehozása**.

    ![Beállítások](./media/druva-tutorial/ic795093.png "beállításai")

1. Az a **egyszeri bejelentkezéses hitelesítési Token** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Az SSO-jogkivonat](./media/druva-tutorial/ic795094.png "SSO-jogkivonat")
    
    a. Kattintson a **másolási**, illessze be az értéket másolta a **érték** szövegmezőjébe a **attribútum hozzáadása** szakaszban az Azure Portalon.
    
    b. Kattintson a **Bezárás** gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/druva-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/druva-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/druva-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/druva-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-druva-test-user"></a>Druva tesztfelhasználó létrehozása

Ahhoz, hogy a Druva jelentkezzen be az Azure AD-felhasználók, akkor ki kell építeni kínál a Druva be. Esetén a Druva kiépítés a manuális feladat.

**Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **kínál a Druva** rendszergazdaként a vállalati webhely.

1. Lépjen a **kezelése \> felhasználók**.
   
   ![Felhasználók kezelése](./media/druva-tutorial/ic795097.png "felhasználók kezelése")

1. Kattintson a **létrehozása új**.
   
   ![Felhasználók kezelése](./media/druva-tutorial/ic795098.png "felhasználók kezelése")

1. Új felhasználó létrehozása párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
   ![Hozzon létre Új_felhasználó](./media/druva-tutorial/ic795099.png "Új_felhasználó létrehozása")
   
   a. Az a **E-mail-cím** szövegmezőben adja meg az e-mail címét, például a felhasználó **brittasimon@contoso.com**.
   
   b. Az a **neve** szövegmezőbe írja be például a felhasználó nevét **BrittaSimon**.
   
   c. Kattintson a **felhasználó létrehozása**.

>[!NOTE]
>Eszközt is használhat bármilyen más kínál a Druva felhasználói fiók létrehozása, vagy az Azure AD-felhasználói fiókok kiépítése Druva által biztosított API-k.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezést kínál a Druva hozzáférésének engedélyezésére használja.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel kínál a Druva, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **kínál a Druva**.

    ![Az alkalmazások listáját a Druva hivatkozásra](./media/druva-tutorial/tutorial_druva_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Druva csempére kattint, akkor kell lekérése automatikusan bejelentkezett a Druva alkalmazásba.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/druva-tutorial/tutorial_general_01.png
[2]: ./media/druva-tutorial/tutorial_general_02.png
[3]: ./media/druva-tutorial/tutorial_general_03.png
[4]: ./media/druva-tutorial/tutorial_general_04.png

[100]: ./media/druva-tutorial/tutorial_general_100.png

[200]: ./media/druva-tutorial/tutorial_general_200.png
[201]: ./media/druva-tutorial/tutorial_general_201.png
[202]: ./media/druva-tutorial/tutorial_general_202.png
[203]: ./media/druva-tutorial/tutorial_general_203.png

