---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező AnswerHub |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és AnswerHub között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 40ca7a9e6a7bd5e42049bb07fc5e69230e6bbfd4
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55191764"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező AnswerHub

Ebben az oktatóanyagban elsajátíthatja, hogyan AnswerHub integrálása az Azure Active Directory (Azure AD).

AnswerHub integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, hogy ki férhet hozzá AnswerHub Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett AnswerHub (egyszeri bejelentkezés) az Azure AD-fiókjukkal
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

AnswerHub az Azure AD-integráció konfigurálásához a következőkre van szükség:

- Azure AD-előfizetés
- Egy AnswerHub egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a egy egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. AnswerHub hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-answerhub-from-the-gallery"></a>AnswerHub hozzáadása a katalógusból
Az Azure AD integrálása a AnswerHub konfigurálásához hozzá kell AnswerHub a katalógusból a felügyelt SaaS-alkalmazások listájára.

**AnswerHub hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. A Keresés mezőbe írja be a **AnswerHub**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/answerhub-tutorial/tutorial_answerhub_search.png)

5. Az eredmények panelen válassza ki a **AnswerHub**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/answerhub-tutorial/tutorial_answerhub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés AnswerHub a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó AnswerHub mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó AnswerHub hivatkozás kapcsolata kell létrehozni.

AnswerHub, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az AnswerHub tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. **[Egy AnswerHub tesztfelhasználó létrehozása](#creating-an-answerhub-test-user)**  – egy megfelelője a Britta Simon AnswerHub, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
4. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és AnswerHub alkalmazását az egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés AnswerHub, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **AnswerHub** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/answerhub-tutorial/tutorial_answerhub_samlbase.png)

3. Az a **AnswerHub tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/answerhub-tutorial/tutorial_answerhub_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<company>.answerhub.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<company>.answerhub.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [AnswerHub ügyfél-támogatási csapatának](mailto:success@answerhub.com) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/answerhub-tutorial/tutorial_answerhub_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/answerhub-tutorial/tutorial_general_400.png)

6. Az a **AnswerHub konfigurációs** területén kattintson **konfigurálása AnswerHub** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **kijelentkezéses URL-címet, és a SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/answerhub-tutorial/tutorial_answerhub_configure.png) 

7. Egy másik böngészőablakban jelentkezzen be a AnswerHub vállalati hely rendszergazdaként.
   
    >[!NOTE]
    >Ha AnswerHub segítségre van szüksége, forduljon a [AnswerHub a támogatási csapat](mailto:success@answerhub.com.).
   
8. Lépjen a **felügyeleti**.

9. Kattintson a **felhasználó és csoport** fülre.

10. A navigációs ablak bal oldalán az a **közösségi beállítások** területén kattintson **SAML-telepítő**.

11. Kattintson a **Identitásszolgáltató konfigurációs** fülre.

12. Az a **Identitásszolgáltató konfigurációs** fülre, hajtsa végre az alábbi lépéseket:

     ![SAML-telepítő](./media/answerhub-tutorial/ic785172.png "SAML-telepítő")  
  
     a. A **Identitásszolgáltató bejelentkezési URL-cím** szövegmezőjébe illessze be **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.
  
     b. A **Identitásszolgáltató kijelentkezési URL-címe** szövegmezőjébe illessze be **kijelentkezéses URL-cím** Azure Portalról másolt érték.
     
     c. A **Identitásszolgáltató azonosító formátuma** szövegmezőbe írja be a felhasználói azonosító érték azonos a kiválasztott az Azure Portalon, **felhasználói attribútumok** szakaszban.
  
     d. Kattintson a **kulcsok és tanúsítványok**.

13. A kulcsok és tanúsítványok lapon hajtsa végre az alábbi lépéseket:
    
     ![Kulcsok és tanúsítványok](./media/answerhub-tutorial/ic785173.png "kulcsok és tanúsítványok")  
 
     a. Nyissa meg a base-64 kódolású tanúsítvány már letöltötte az Azure Portalról a Jegyzettömbben, amely a tartalmát a vágólapra másolja és illessze be azt a **Identitásszolgáltató nyilvános kulcs (x 509-formátum)** szövegmezőbe.
  
     b. Kattintson a **Save** (Mentés) gombra.

14. Az a **Identitásszolgáltató konfigurációs** lapra, majd **mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/answerhub-tutorial/create_aaduser_01.png) 

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/answerhub-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/answerhub-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/answerhub-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-answerhub-test-user"></a>Egy AnswerHub tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók AnswerHub jelentkezzen be, akkor ki kell építeni AnswerHub be.  
AnswerHub, esetén kiépítése a manuális feladat.

**Üzembe helyez egy felhasználói fiókot, hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **AnswerHub** rendszergazdaként a vállalati webhely.

2. Lépjen a **felügyeleti**.

3. Kattintson a **felhasználók és csoportok** fülre.

4. A navigációs ablak bal oldalán az a **felhasználók kezelése** területén kattintson **felhasználók létrehozása vagy importálása**.
   
   ![Felhasználók és csoportok](./media/answerhub-tutorial/ic785175.png "felhasználók és csoportok")

5. Írja be a **E-mail-cím**, **felhasználónév** és **jelszó** érvényes Azure Active Directory-fiókok kiépítéséhez, a kapcsolódó szöveges mezőkben, majd kattintson a szeretné **Mentés**.

>[!NOTE]
>Bármely más AnswerHub felhasználói fiók létrehozása eszközöket használhatja, vagy az aad-ben a felhasználói fiókok kiépítését AnswerHub által biztosított API-k.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés AnswerHub Azure egyszeri bejelentkezés használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel AnswerHub, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listájában jelölje ki a **AnswerHub**.

    ![Egyszeri bejelentkezés konfigurálása](./media/answerhub-tutorial/tutorial_answerhub_app.png) 

3. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a AnswerHub csempére kattint, meg kell lekérése automatikusan bejelentkezett az AnswerHub alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/answerhub-tutorial/tutorial_general_01.png
[2]: ./media/answerhub-tutorial/tutorial_general_02.png
[3]: ./media/answerhub-tutorial/tutorial_general_03.png
[4]: ./media/answerhub-tutorial/tutorial_general_04.png

[100]: ./media/answerhub-tutorial/tutorial_general_100.png

[200]: ./media/answerhub-tutorial/tutorial_general_200.png
[201]: ./media/answerhub-tutorial/tutorial_general_201.png
[202]: ./media/answerhub-tutorial/tutorial_general_202.png
[203]: ./media/answerhub-tutorial/tutorial_general_203.png

