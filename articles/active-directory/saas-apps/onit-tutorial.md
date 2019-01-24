---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Onit |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és Onit között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: bc479a28-8fcd-493f-ac53-681975a5149c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/24/2017
ms.author: jeedes
ms.openlocfilehash: 80ae8d6a1e27d13bc6750779f2e38649c49e2498
ms.sourcegitcommit: 98645e63f657ffa2cc42f52fea911b1cdcd56453
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2019
ms.locfileid: "54814749"
---
# <a name="tutorial-azure-active-directory-integration-with-onit"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező Onit

Ebben az oktatóanyagban elsajátíthatja, hogyan Onit integrálása az Azure Active Directory (Azure AD).

Onit integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Onit Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Onit (egyszeri bejelentkezés), az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Onit az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy Onit egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Onit hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-onit-from-the-gallery"></a>Onit hozzáadása a katalógusból
Az Azure AD integrálása a Onit konfigurálásához hozzá kell Onit a katalógusból a felügyelt SaaS-alkalmazások listájára.

**Onit hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Onit**válassza **Onit** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredmények listájában Onit](./media/onit-tutorial/tutorial_onit_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálja, és a teszt "Britta Simon." nevű felhasználó Onit az Azure AD egyszeri bejelentkezés tesztelése

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Onit mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Onit hivatkozás kapcsolata kell létrehozni.

Onit, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az Onit tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Onit tesztfelhasználót](#create-an-onit-test-user)**  – egy megfelelője a Britta Simon Onit, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és Onit alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Onit, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Onit** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/onit-tutorial/tutorial_onit_samlbase.png)

1. Az a **Onit tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Onit tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/onit-tutorial/tutorial_onit_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<sub-domain>.onit.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<sub-domain>.onit.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [Onit ügyfél-támogatási csapatának](https://www.onit.com/support) beolvasni ezeket az értékeket. 
 
1. Az a **SAML-aláíró tanúsítvány** területén másolja a **UJJLENYOMAT** tanúsítvány értékét.

    ![A tanúsítvány letöltési hivatkozás](./media/onit-tutorial/tutorial_onit_certificate.png) 

1. Onit alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Állítsa be a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **"Atrribute"** az alkalmazás lapján. Az alábbi képernyőfelvételen látható erre egy példa látható. 

    ![Egyszeri bejelentkezés konfigurálása](./media/onit-tutorial/tutorial_onit_attribute.png) 

1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az ábrán látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | ------------------- | -------------------- |
    | e-mail | user.mail |
    
    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/onit-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/onit-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    d. Hagyja a **Namespace** üres.
    
    e. Kattintson az **OK** gombra.

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/onit-tutorial/tutorial_general_400.png)

1. Az a **Onit konfigurációs** területén kattintson **konfigurálása Onit** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címet, a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Onit konfiguráció](./media/onit-tutorial/tutorial_onit_configure.png)

1. Egy másik böngészőablakban jelentkezzen be a Onit vállalati hely rendszergazdaként.

1. A felső menüben kattintson **felügyeleti**.
   
   ![Felügyeleti](./media/onit-tutorial/IC791174.png "felügyelete")
1. Kattintson a **szerkesztési Corporation**.
   
   ![Szerkesztés Corporation](./media/onit-tutorial/IC791175.png "Corporation szerkesztése")
   
1. Kattintson a **biztonsági** fülre.
    
    ![Vállalat adatainak szerkesztése](./media/onit-tutorial/IC791176.png "vállalat adatainak szerkesztése")

1. Az a **biztonsági** fülre, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés](./media/onit-tutorial/IC791177.png "egyszeri bejelentkezés")

    a. Mint **hitelesítési stratégia**válassza **az egyszeri bejelentkezést és a jelszó**.
    
    b. A **identitásszolgáltató cél URL-cím** szövegmező, illessze be az értéket a **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, az Azure Portalról másolt.

    c. A **identitásszolgáltató kijelentkezési URL-címe** szövegmezőjébe illessze be az értéket, **kijelentkezéses URL-cím**, az Azure Portalról másolt.

    d. A **identitásszolgáltató tanúsítvány-ujjlenyomat (SHA1)** szövegmezőjébe illessze be a **ujjlenyomat** tanúsítványt, amely az Azure Portalról másolt értékét.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/onit-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/onit-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/onit-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/onit-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-onit-test-user"></a>Hozzon létre egy Onit tesztfelhasználó számára

Ahhoz, hogy az Azure AD-felhasználók Onit szolgáltatásba való bejelentkezéshez, akkor ki kell építeni Onit be.  

Onit, esetén kiépítése a manuális feladat.

**Felhasználók átadásának konfigurálása, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Onit** rendszergazdaként a vállalati webhely.
1. Kattintson a **felhasználó hozzáadása**.
   
   ![Felügyeleti](./media/onit-tutorial/IC791180.png "felügyelete")
1. Az a **felhasználó hozzáadása** párbeszédpanel lapon, a következő lépésekkel:
   
   ![Felhasználó hozzáadása](./media/onit-tutorial/IC791181.png "felhasználó hozzáadása")
   
  1. Írja be a **neve** és a **E-mail cím** egy érvényes Azure AD-fiókot, a kapcsolódó szövegmezőkben kiépítendő.
  1. Kattintson a **Create** (Létrehozás) gombra.    
   
 > [!NOTE]
 > Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a egy hivatkozást, mielőtt aktívvá válik, győződjön meg arról, hogy fiókjuk követi.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Onit Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel Onit, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Onit**.

    ![Az alkalmazások listáját a Onit hivatkozásra](./media/onit-tutorial/tutorial_onit_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Onit csempére kattint, meg kell lekérése automatikusan bejelentkezett az Onit alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/onit-tutorial/tutorial_general_01.png
[2]: ./media/onit-tutorial/tutorial_general_02.png
[3]: ./media/onit-tutorial/tutorial_general_03.png
[4]: ./media/onit-tutorial/tutorial_general_04.png

[100]: ./media/onit-tutorial/tutorial_general_100.png

[200]: ./media/onit-tutorial/tutorial_general_200.png
[201]: ./media/onit-tutorial/tutorial_general_201.png
[202]: ./media/onit-tutorial/tutorial_general_202.png
[203]: ./media/onit-tutorial/tutorial_general_203.png

