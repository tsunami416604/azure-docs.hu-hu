---
title: 'Oktatóanyag: Azure Active Directoryval integrált iQualify LMS |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és iQualify LMS között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 8a3caaff-dd8d-4afd-badf-a0fd60db3d2c
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: 166dfa8c5b3456de59055e5f62a566064540db31
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36225024"
---
# <a name="tutorial-azure-active-directory-integration-with-iqualify-lms"></a>Oktatóanyag: Azure Active Directoryval integrált iQualify LMS

Ebben az oktatóanyagban elsajátíthatja iQualify LMS integrálása az Azure Active Directory (Azure AD).

IQualify LMS integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér iQualify LMS szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett iQualify LMS (egyszeri bejelentkezés) a saját Azure AD-fiókok számára.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs iQualify LMS konfigurálni, kell a következő elemek:

- Az Azure AD szolgáltatásra
- Egy iQualify LMS egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből iQualify LMS hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-iqualify-lms-from-the-gallery"></a>A gyűjteményből iQualify LMS hozzáadása
Az Azure AD integrálása a iQualify LMS konfigurálásához kell hozzáadnia iQualify LMS a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből iQualify LMS hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **iQualify LMS**, jelölje be **iQualify LMS** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![az eredménylistában LMS iQualify](./media/iqualify-tutorial/tutorial_iqualify_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a iQualify LMS "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD az Azure AD-ben a felhasználó van LMS iQualify a párjukhoz felhasználó tudnia kell. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a iQualify hivatkozás kapcsolatának LMS kell létrehozni.

IQualify LMS, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a iQualify LMS tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy iQualify LMS tesztfelhasználó](#create-an-iqualify-lms-test-user)**  - Britta Simon egy partner, a felhasználó az Azure AD ábrázolását kapcsolódó LMS iQualify rendelkezik.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az iQualify LMS alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés iQualify LMS, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **iQualify LMS** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/iqualify-tutorial/tutorial_iqualify_samlbase.png)

3. Az a **iQualify LMS tartomány és az URL-címek** szakaszban, ha szeretne beállítani az alkalmazás által kezdeményezett IDP módban, hajtsa végre az alábbi lépéseket:

    ![információ iQualify LMS tartomány és az URL-címek egyszeri bejelentkezést.](./media/iqualify-tutorial/tutorial_iqualify_url.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: 
    | |
    |--|--|
    | Éles környezetben: `https://<yourorg>.iqualify.com/`|
    | Tesztelési környezetben: `https://<yourorg>.iqualify.io`|
    
    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: 
    | |
    |--|--|
    | Éles környezetben: `https://<yourorg>.iqualify.com/auth/saml2/callback` |
    | Tesztelési környezetben: `https://<yourorg>.iqualify.io/auth/saml2/callback` |

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![információ iQualify LMS tartomány és az URL-címek egyszeri bejelentkezést.](./media/iqualify-tutorial/tutorial_iqualify_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:
    | |
    |--|--|
    | Éles környezetben: `https://<yourorg>.iqualify.com/login` |
    | Tesztelési környezetben: `https://<yourorg>.iqualify.io/login` |
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [iQualify LMS ügyfél-támogatási csoport](https://www.iqualify.com) beolvasni ezeket az értékeket. 

5. A iQualify LMS alkalmazás vár a Security Assertion Markup Language (SAML) helyességi feltételek megjelenik egy meghatározott formátumnak. A jogcímek konfigurálása és kezelése az attribútumok értékének a **felhasználói attribútumok** iQualify alkalmazás integrációs lap részében, az alábbi képernyőfelvételen látható módon:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/iqualify-tutorial/atb.png)

6. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanel az alábbi táblázatban szereplő minden egyes sorára hajtsa végre a következőket:
    
    | Attribútum neve | Attribútum értéke |
    | --- | --- |    
    | e-mailben | User.userPrincipalName |
    | first_name | User.givenName |
    | last_name | User.surname |
    | person_id | "attribútum" | 

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/iqualify-tutorial/atb2.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/iqualify-tutorial/atb3.png)
    
    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.
    
    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson a **Ok**

    e. Ismételje meg a "a" a "d". a következő táblázat sorait. 

    > [!Note]
    > A lépések "a" a "d" ismétlődő a **person_id** attribútum **nem kötelező**

7. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base 64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/iqualify-tutorial/tutorial_iqualify_certificate.png) 

8. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/iqualify-tutorial/tutorial_general_400.png)
    
9. A a **iQualify LMS konfigurációs** kattintson **iQualify LMS konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![iQualify LMS konfiguráció](./media/iqualify-tutorial/tutorial_iqualify_configure.png) 

10.  Nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként iQualify környezetét.

11. Miután bejelentkezett, kattintson a jobb felső sarokban a profilképet, majd kattintson a **"Fiókbeállítások."**

    ![Fiókbeállítások](./media/iqualify-tutorial/setting1.png) 
12. A fiók beállítások területen kattintson a menüszalagon a bal oldali menü, és válassza a **"INTEGRÁCIÓK."**
    
    ![INTEGRÁCIÓJA](./media/iqualify-tutorial/setting2.png)

13. Az INTEGRÁCIÓ, kattintson a a **SAML** ikonra.

    ![SAML ikon](./media/iqualify-tutorial/setting3.png)

14. Az a **SAML-alapú hitelesítési beállítások** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![SAML-alapú hitelesítési beállítások](./media/iqualify-tutorial/setting4.png)

    a. Az a **SAML egyetlen SIGN-ON URL-címe** mezőbe illessze be a **SAML egyetlen Sign‑On szolgáltatás URL-címe** az Azure AD alkalmazás konfigurációs ablakának átmásolja értéket.
    
    b. Az a **SAML KIJELENTKEZÉSI URL-cím** mezőbe illessze be a **Sign‑Out URL-cím** az Azure AD alkalmazás konfigurációs ablakának átmásolja értéket.
    
    c. Nyissa meg a letöltött fájlt a Jegyzettömbben, másolja a tartalmat, és majd illessze be a **nyilvános tanúsítvány** mezőbe.
    
    d. A **bejelentkezési GOMB FELIRATÁT** adja meg a gombra, a bejelentkezési lapon megjelenő nevét.
    
    e. Kattintson a **SAVE** (Mentés) gombra.

    f. Kattintson a **frissítés**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/iqualify-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/iqualify-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/iqualify-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/iqualify-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-iqualify-lms-test-user"></a>Hozzon létre egy iQualify LMS tesztfelhasználó számára

Ebben a szakaszban egy felhasználó Britta Simon nevű iQualify jön létre. iQualify LMS támogatja just‑in‑time a felhasználók átadása, amely alapértelmezés szerint engedélyezve van.

Nincs ebben a szakaszban az Ön művelet elem. Ha a felhasználó nem létezik a iQualify, egy új iQualify LMS elérésére tett kísérlet során jön létre.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezéshez használandó iQualify LMS való hozzáférés biztosítása.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése iQualify LMS, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **iQualify LMS**.

    ![Az alkalmazások listáját a iQualify LMS hivatkozás](./media/iqualify-tutorial/tutorial_iqualify_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

A iQualify LMS a hozzáférési Panel csempére kattintva szerezheti be az iQualify LMS alkalmazás bejelentkezési oldalt. 

   ![bejelentkezési oldal](./media/iqualify-tutorial/login.png) 

Kattintson a **jelentkezzen be az Azure AD** gombra kattintva kell beolvasni automatikusan bejelentkezett az iQualify LMS alkalmazására.

A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/iqualify-tutorial/tutorial_general_01.png
[2]: ./media/iqualify-tutorial/tutorial_general_02.png
[3]: ./media/iqualify-tutorial/tutorial_general_03.png
[4]: ./media/iqualify-tutorial/tutorial_general_04.png

[100]: ./media/iqualify-tutorial/tutorial_general_100.png

[200]: ./media/iqualify-tutorial/tutorial_general_200.png
[201]: ./media/iqualify-tutorial/tutorial_general_201.png
[202]: ./media/iqualify-tutorial/tutorial_general_202.png
[203]: ./media/iqualify-tutorial/tutorial_general_203.png

