---
title: "Oktatóanyag: Azure Active Directory-integráció IMPAC kockázati-kezelővel |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a IMPAC kockázat Manager között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 4d77390e-898c-4258-a562-a1181dfe2880
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/01/2017
ms.author: jeedes
ms.openlocfilehash: ade4076917988c5747a0d10a99578b49c917e1db
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-impac-risk-manager"></a>Oktatóanyag: Azure Active Directory-integráció IMPAC kockázati-kezelővel

Ebben az oktatóanyagban elsajátíthatja IMPAC kockázat Manager integrálása az Azure Active Directory (Azure AD).

IMPAC kockázat Manager integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér a IMPAC kockázat Manager szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett IMPAC kockázat Manager (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a IMPAC kockázat Managerrel, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy IMPAC kockázat Manager egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből IMPAC kockázat kezelő hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-impac-risk-manager-from-the-gallery"></a>A gyűjteményből IMPAC kockázat kezelő hozzáadása
Az Azure AD-be a IMPAC kockázat Manager-integráció konfigurálása szüksége IMPAC kockázat Manager hozzáadása a kezelt SaaS-alkalmazások listáját a gyűjteményből.

**A gyűjteményből IMPAC kockázat Manager hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **IMPAC kockázat Manager**, jelölje be **IMPAC kockázat Manager** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában IMPAC kockázat Manager](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a IMPAC kockázat Managerrel "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó IMPAC kockázat Manager Újdonságok egy felhasználó számára az Azure ad-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználói IMPAC kockázat Manager közötti kapcsolat kapcsolatot kell létrehozni.

A IMPAC kockázat Managerben, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a IMPAC kockázat Managerrel tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[IMPAC kockázat Manager tesztfelhasználó létrehozása](#create-a-impac-risk-manager-test-user)**  – Ha egy megfelelője a Britta Simon a IMPAC kockázat Manager, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az IMPAC kockázat Manager alkalmazásban.

**Az Azure AD egyszeri bejelentkezést a IMPAC kockázat Manager konfigurálásához hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **IMPAC kockázat Manager** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_samlbase.png)

3. Az a **IMPAC kockázat Manager-tartományra és URL-címek** szakaszban, ha szeretne beállítani az alkalmazás által kezdeményezett IDP módban, hajtsa végre az alábbi lépéseket:

    ![Az egyszeri bejelentkezés információkat IMPAC kockázat Manager-tartományra és URL-címek](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_url_new.png)

    a. Az a **azonosító** szövegmezőhöz IMPAC által megadott érték típusa

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:
    | Környezet | Az URL-minta |
    | ---------------|--------------- |    
    | Éles környezetekhez |`https://www.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Átmeneti és képzési  |`https://staging.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | Fejlesztéshez  |`https://dev.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | A QA |`https://QA.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|
    | A teszt |`https://test.riskmanager.co.nz/DotNet/SSOv2/AssertionConsumerService.aspx?client=<ClientSuffix>`|

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információkat IMPAC kockázat Manager-tartományra és URL-címek](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_url1_new.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:
    | Környezet | Az URL-minta |
    | ---------------|--------------- |    
    | Éles környezetekhez |`https://www.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Átmeneti és képzési  |`https://staging.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | Fejlesztéshez  |`https://dev.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | A QA |`https://QA.riskmanager.co.nz/SSOv2/<ClientSuffix>`|
    | A teszt |`https://test.riskmanager.co.nz/SSOv2/<ClientSuffix>`|

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [IMPAC kockázat Manager-ügyfél-támogatási csoport](mailto:rmsupport@Impac.co.nz) beolvasni ezeket az értékeket.

5. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_400.png)
    
7. Az a **IMPAC kockázatú Manager Configuration** területen kattintson **IMPAC kockázat Manager konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe, SAML Entitásazonosító** és **Sign-Out URL-cím** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_configure.png)

8. Egyszeri bejelentkezés konfigurálása **IMPAC kockázat Manager** oldalon kell küldeniük a letöltött **Certificate(Base64)**, **Sign-Out URL, SAML Entitásazonosító,** és  **SAML-alapú egyszeri bejelentkezési URL-címe** való [IMPAC kockázat Manager támogatási munkacsoportjának](mailto:rmsupport@Impac.co.nz). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-impacriskmanager-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-impacriskmanager-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-impacriskmanager-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-impacriskmanager-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-impac-risk-manager-test-user"></a>IMPAC kockázat Manager tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon IMPAC kockázat Manager nevű felhasználót hoz létre. Együttműködve [IMPAC kockázat Manager támogatási munkacsoportjának](mailto:rmsupport@Impac.co.nz) a felhasználók hozzáadása a IMPAC kockázat Manager platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt. 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon hozzáférés biztosítása a IMPAC kockázat Manager által használandó Azure egyszeri bejelentkezés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200] 

**A IMPAC kockázat Manager Britta Simon hozzárendeléséhez a következő lépésekkel:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **IMPAC kockázat Manager**.

    ![Az alkalmazások listáját a IMPAC kockázat Manager hivatkozás](./media/active-directory-saas-impacriskmanager-tutorial/tutorial_impacriskmanager_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési Panel IMPAC kockázat Manager mozaik gombra kattint, meg kell beolvasása automatikusan bejelentkezett az IMPAC kockázat Manager alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-impacriskmanager-tutorial/tutorial_general_203.png

