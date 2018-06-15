---
title: 'Oktatóanyag: Azure Active Directoryval integrált SpaceIQ |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és SpaceIQ között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 5b55ae29-491f-401f-9299-d3a6b64a1b99
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/04/2017
ms.author: jeedes
ms.openlocfilehash: f76be8db123558d87dfa5c1756f5928aed97d63f
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34351613"
---
# <a name="tutorial-azure-active-directory-integration-with-spaceiq"></a>Oktatóanyag: Azure Active Directoryval integrált SpaceIQ

Ebben az oktatóanyagban elsajátíthatja SpaceIQ integrálása az Azure Active Directory (Azure AD).

SpaceIQ integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér SpaceIQ szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett SpaceIQ (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs SpaceIQ, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy SpaceIQ egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből SpaceIQ hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-spaceiq-from-the-gallery"></a>A gyűjteményből SpaceIQ hozzáadása
Az Azure AD integrálása a SpaceIQ konfigurálásához kell hozzáadnia SpaceIQ a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből SpaceIQ hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **SpaceIQ**, jelölje be **SpaceIQ** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában SpaceIQ](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján SpaceIQ

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó SpaceIQ a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a SpaceIQ közötti kapcsolat kapcsolatot kell létrehozni.

SpaceIQ, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a SpaceIQ tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[SpaceIQ tesztfelhasználó létrehozása](#create-a-spaceiq-test-user)**  - való Britta Simon valami SpaceIQ, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az SpaceIQ alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés SpaceIQ, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **SpaceIQ** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_samlbase.png)

3. Az a **SpaceIQ tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk SpaceIQ tartomány és az URL-címek](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_url.png)

    a. Az a **azonosító** szövegmező, írja be az URL-cím: `https://api.spaceiq.com`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://api.spaceiq.com/saml/<instanceid>/callback`

    > [!NOTE] 
    > Frissítheti ezeket az értékeket a tényleges válasz URL-CÍMEN és az oktatóanyag későbbi részében ismertetett azonosítója.
 
4. Az a **SAML-aláíró tanúsítványa** kattintson **(tanúsítvány Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-spaceiq-tutorial/tutorial_general_400.png)

6. A a **SpaceIQ konfigurációs** kattintson **konfigurálása SpaceIQ** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító** a a **rövid összefoglaló szakasz.**

    ![SpaceIQ konfiguráció](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_configure.png) 

7.  Nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként SpaceIQ környezetét.

8. Miután bejelentkezett, kattintson a jobb felső sarokban Kirakós bejelentkezési, majd a **"Integrációja"**

    ![Fiókbeállítások](./media/active-directory-saas-spaceiq-tutorial/setting1.png) 

9. A **összes KIÉPÍTÉS & SSO**, kattintson a a **Azure** adja hozzá az Azure példánya IDP csempére.

    ![SAML ikon](./media/active-directory-saas-spaceiq-tutorial/setting2.png)

10. Az a **SSO** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![SAML-alapú hitelesítési beállítások](./media/active-directory-saas-spaceiq-tutorial/setting3.png)

    a. Az a **SAML kiállítójának URL-címe** mezőbe illessze be a **SAML Entitásazonosító** az Azure AD alkalmazás konfigurációs ablakának átmásolja értéket.
    
    b. Másolás a **SAML visszahívási végponti URL-Címének (csak olvasható)** értékét, és illessze be az értéket a **válasz URL-CÍMEN** mezőben az Azure portál, SpaceIQ **tartomány és az URL-címek** szakasz.
    
    c. Másolás a **SAML célközönség URI-azonosítója (csak olvasható)** értékét, és illessze be az értéket a **azonosító** mezőben az Azure portál, SpaceIQ **tartomány és az URL-címek** szakasz.

    d. Nyissa meg a letöltött fájlt a Jegyzettömbben, másolja a tartalmat, és majd illessze be a **X.509 tanúsítvány** mezőbe.
    
    e. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-spaceiq-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-spaceiq-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-spaceiq-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-spaceiq-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-spaceiq-test-user"></a>SpaceIQ tesztfelhasználó létrehozása

Ebben a szakaszban egy SpaceIQ Britta Simon nevű felhasználót hoz létre. Munkahelyi [SpaceIQ támogatási csoport](mailto:eng@spaceiq.com) a felhasználók hozzáadása a SpaceIQ platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SpaceIQ Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése SpaceIQ, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **SpaceIQ**.

    ![Az alkalmazások listáját a SpaceIQ hivatkozás](./media/active-directory-saas-spaceiq-tutorial/tutorial_spaceiq_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen SpaceIQ csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az SpaceIQ alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-spaceiq-tutorial/tutorial_general_203.png

