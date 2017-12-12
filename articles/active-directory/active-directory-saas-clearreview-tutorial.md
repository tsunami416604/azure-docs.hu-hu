---
title: "Oktatóanyag: Azure Active Directoryval integrált egyértelmű áttekintése |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directoryban, és törölje a jelet nézze között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8264159a-11a2-4a8c-8285-4efea0adac8c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/07/2017
ms.author: jeedes
ms.openlocfilehash: e999e375d11f5d2a4657b360cf774ae10c28b0e0
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-clear-review"></a>Oktatóanyag: Azure Active Directoryval integrált egyértelmű áttekintése

Ebben az oktatóanyagban elsajátíthatja, törölje a jelet felülvizsgálati integrálása az Azure Active Directory (Azure AD).

Törölje a jelet felülvizsgálati integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, törölje a jelet felülvizsgálati hozzáféréssel rendelkező Azure AD-ben.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett egyértelmű felülvizsgálandó (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Törölje a jelet tekintse át az Azure AD-integrációs konfigurálni, kell a következő elemek:

- Az Azure AD szolgáltatásra
- Egy egyszerű tekintse át az egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Törölje a jelet felülvizsgálat hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-clear-review-from-the-gallery"></a>Törölje a jelet felülvizsgálat hozzáadása a gyűjteményből
Az Azure AD-be egyértelmű nézze át az integráció konfigurálásához kell hozzáadnia egyértelmű tekintse át a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Törölje a jelet tekintse át a gyűjteményből hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **egyértelmű felülvizsgálati**, jelölje be **egyértelmű felülvizsgálati** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Tekintse át az eredménylistában törlése](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapuló egyértelmű áttekintése.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó törlése felülvizsgálat alatt a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó törlése felülvizsgálat alatt közötti kapcsolat kapcsolatot kell létrehozni.

Törölje a jelet felülvizsgálat alatt, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést törölje a jelet tekintse át a tesztelése és konfigurálása, kell végrehajtani a következő építőelemeket:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Törölje a jelet felülvizsgálati tesztfelhasználó létrehozása](#create-a-clear-review-test-user)**  - való egy megfelelője a Britta Simon egyértelmű tekintse át, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és törölje a jelet felülvizsgálati alkalmazásában egyszeri bejelentkezés konfigurálása.

**Konfigurálása az Azure AD egyszeri bejelentkezést törölje a jelet felülvizsgálati, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **egyértelmű felülvizsgálati** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_samlbase.png)

3. Az a **egyértelmű felülvizsgálati tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **kezdeményezett IdP** mód:

    ![Törölje a jelet felülvizsgálati tartomány és URL-címek az egyszeri bejelentkezés információk](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_url.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe:`https://<customer name>.clearreview.com/sso/metadata`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://<customer>.clearreview.com/sso/acs/`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Törölje a jelet felülvizsgálati tartomány és URL-címek az egyszeri bejelentkezés információk](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_url_sp.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<customer name>.clearreview.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója és a válasz URL-CÍMEN. Ügyfél [egyértelmű felülvizsgálati támogatási csoport](https://clearreview.com/contact/) beolvasni ezeket az értékeket.

5. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_certificate.png)

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-clearreview-tutorial/tutorial_general_400.png)

7. Az a **felülvizsgálati konfiguráció törlése** területen kattintson **egyértelmű felülvizsgálati konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Törölje a konfiguráció áttekintése](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_configure.png) 

8. Egyszeri bejelentkezés konfigurálása **egyértelmű felülvizsgálati** oldalán, nyissa meg a **egyértelmű felülvizsgálati** portál, rendszergazdai hitelesítő adataival.

9. Válassza ki **Admin** a bal oldali navigációs sávon.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_app_admin1.png)

10. Válassza ki **módosítás** az oldal alján.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_app_admin2.png)

11. Hajtsa végre a következő lépéseket **egyszeri bejelentkezési beállítások** lap

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_app_admin3.png)

    a. Az a **kiállítójának URL-címe** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** ami Azure-portálon másolta.

    b. Az a **SAML-végpont** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.    

    c. Az a **SLO végpont** szövegmezőhöz illessze be az értékét **bejelentkezési URL-címe** ami Azure-portálon másolta. 

    d. A letöltött tanúsítvány megnyitása a Jegyzettömbben, és illessze be a tartalom a **X.509 tanúsítvány** szövegmező.   

12. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-clearreview-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-clearreview-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-clearreview-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-clearreview-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-clear-review-test-user"></a>Törölje a jelet felülvizsgálati tesztfelhasználó létrehozása

Ebben a szakaszban Britta Simon nevű egyértelmű tekintse át a felhasználó létrehozása. Adjon együttműködve [egyértelmű felülvizsgálati támogatási csoport](https://clearreview.com/contact/) a felhasználók hozzáadása a tiszta felülvizsgálati platform.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés egyértelmű áttekintése Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Törölje a jelet felülvizsgálati Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **egyértelmű felülvizsgálati**.

    ![Az alkalmazások listáját a tiszta felülvizsgálati hivatkozás](./media/active-directory-saas-clearreview-tutorial/tutorial_clearreview_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési Panel egyértelmű felülvizsgálati mozaik gombra kattint, akkor kell beolvasása automatikusan bejelentkezett egyértelmű felülvizsgálati Alkalmazásmódosítások.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clearreview-tutorial/tutorial_general_203.png

