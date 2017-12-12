---
title: "Oktatóanyag: Azure Active Directoryval integrált megfelelőségi ELF |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a megfelelőségi ELF között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 69c6efc3-54c7-49ec-b827-33177c09aa13
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/2/2017
ms.author: jeedes
ms.openlocfilehash: f05c43e2d8f6e777ae128e90040d8d5c270c212b
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-compliance-elf"></a>Oktatóanyag: Azure Active Directoryval integrált megfelelőségi ELF

Ebben az oktatóanyagban elsajátíthatja megfelelőségi ELF integrálása az Azure Active Directory (Azure AD).

Megfelelőségi ELF integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér megfelelőségi ELF szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a megfelelőségi ELF (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a megfelelőségi ELF, a következő elemeket kell:

- Az Azure AD szolgáltatásra
- A megfelelőségi ELF egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Megfelelőségi ELF hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-compliance-elf-from-the-gallery"></a>Megfelelőségi ELF hozzáadása a gyűjteményből
Az Azure AD integrálása a megfelelőségi ELF konfigurálásához kell hozzáadnia megfelelőségi ELF a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből megfelelőségi ELF hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **megfelelőségi ELF**, jelölje be **megfelelőségi ELF** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Megfelelőségi ELF az eredménylistában](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés megfelelőségi ELF "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a megfelelőségi ELF tartozó felhasználót a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a megfelelőségi ELF közötti kapcsolat kapcsolatot kell létrehozni.

A megfelelőségi ELF, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a megfelelőségi ELF tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Megfelelőségi ELF tesztfelhasználó létrehozása](#create-a-compliance-elf-test-user)**  - való egy megfelelője a Britta Simon megfelelőségi ELF, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a megfelelőségi ELF alkalmazásban egyszeri bejelentkezés konfigurálása.

**Megfelelőségi ELF konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **megfelelőségi ELF** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_samlbase.png)

3. Az a **megfelelőségi ELF tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk megfelelőségi ELF tartomány és az URL-címek](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_url.png)

    Az a **azonosító** szövegmező, adja meg az URL-címet:`https://sso.cordium.com`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Megfelelőségi ELF tartomány és az URL-címek egyszeri bejelentkezést.](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe:`https://<subdomain>.complianceelf.com`
    
    > [!NOTE] 
    > Ez az érték nincs valós. Ez a frissítés a tényleges bejelentkezési URL-címet. Ügyfél [megfelelőségi ELF támogatási csoport](mailto:support@complianceelf.com) lekérni ezt az értéket.

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-complianceelf-tutorial/tutorial_general_400.png)

6. Létrehozásához a **metaadatok** URL-címe, hajtsa végre a következő lépéseket:

    a. Kattintson a **App regisztrációk**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_appregistrations.png)
   
    b. Kattintson a **végpontok** megnyitásához **végpontok** párbeszédpanel megnyitásához.  
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_endpointicon.png)

    c. Kattintson a Másolás gombra másolása **ÖSSZEVONÁSI METAADAT-dokumentum** URL-címet, és illessze be a Jegyzettömbbe.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_endpoint.png)
     
    d. Most lépjen a tulajdonságlapján **megfelelőségi ELF** , és másolja a **alkalmazásazonosító** használatával **másolási** gombra, majd illessze be a Jegyzettömbbe.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_appid.png)

    e. Készítése a **metaadatainak URL-CÍMÉT** a következő minta használatával:`<FEDERATION METADATA DOCUMENT url>?appid=<application id>`

7. Egyszeri bejelentkezés konfigurálása **megfelelőségi ELF** oldalon kell küldeniük a **metaadatainak URL-CÍMÉT** való [megfelelőségi ELF támogatási csoport](mailto:support@complianceelf.com). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-complianceelf-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-complianceelf-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-complianceelf-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-complianceelf-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-compliance-elf-test-user"></a>Megfelelőségi ELF tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy megfelelőségi ELF Britta Simon nevű felhasználót. Együttműködve [megfelelőségi ELF támogatási csoport](mailto:support@complianceelf.com) a felhasználók hozzáadása a megfelelőségi ELF platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés megfelelőségi ELF Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Megfelelőségi ELF Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **megfelelőségi ELF**.

    ![Az alkalmazások listáját a megfelelőségi ELF hivatkozás](./media/active-directory-saas-complianceelf-tutorial/tutorial_complianceelf_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen megfelelőségi ELF csempére kattint, akkor kell beolvasása automatikusan bejelentkezett a megfelelőségi ELF alkalmazásba.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-complianceelf-tutorial/tutorial_general_203.png

