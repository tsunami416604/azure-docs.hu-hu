---
title: "Oktatóanyag: Azure Active Directoryval integrált QPrism |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és QPrism között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 72ab75ba-132b-4f83-a34b-d28b81b6d7bc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/13/2017
ms.author: jeedes
ms.openlocfilehash: 1f697b95074e0fc9dbb3e8c7800e69f8ece9e0b3
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-qprism"></a>Oktatóanyag: Azure Active Directoryval integrált QPrism

Ebben az oktatóanyagban elsajátíthatja QPrism integrálása az Azure Active Directory (Azure AD).

QPrism integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér QPrism szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezve QPrism (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen kezelheti: az Azure-portálon.

Az Azure AD SaaS alkalmazásintegráció kapcsolatos további tudnivalókért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs QPrism, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy QPrism egyszeri bejelentkezés engedélyezve van az előfizetésben

Ez az oktatóanyag lépéseit teszteléséhez hajtsa végre az ezek az ajánlások:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből QPrism hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="add-qprism-from-the-gallery"></a>Adja hozzá a QPrism a gyűjteményből
Az Azure AD integrálása a QPrism konfigurálásához kell hozzáadnia QPrism a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**QPrism hozzáadása a gyűjteményből:**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory**. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások** > **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Egy új alkalmazást, a párbeszédpanel tetején hozzáadásához válassza **új alkalmazás**.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **QPrism**, és válassza ki **QPrism** eredmény panelen. Kattintson a **Hozzáadás** hozzáadása az alkalmazáshoz.

    ![Az eredménylistában QPrism](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a QPrism "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, aki a párjukhoz felhasználó QPrism egy felhasználó számára az Azure AD. Ez azt jelenti csatlakoznia kell egy Azure AD-felhasználó és a kapcsolódó felhasználó a QPrism csatolt kapcsolatát.

Ezt a kapcsolatot létesíteni a QPrism, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév**.

Az Azure AD egyszeri bejelentkezést a QPrism tesztelése és konfigurálása, végezze el a következő építőelemeket:

1. [Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy a felhasználók számára a szolgáltatás használatához.
2. [Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. [QPrism tesztfelhasználó létrehozása](#create-a-qprism-test-user) való Britta Simon valami QPrism kapcsolódó felhasználói az Azure AD ábrázolását.
4. [Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD az egyszeri bejelentkezés az Azure portálon, és QPrism alkalmazásában egyszeri bejelentkezés konfigurálása.

1. Az Azure portálon a a **QPrism** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédpanelen jelölje ki **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_samlbase.png)

3. Az a **QPrism tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk QPrism tartomány és az URL-címek](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmezőbe írja be egy URL-címet, amely a következő mintát használ:`https://<customer domain>.qmyzone.com/login`

    b. Az a **azonosító** szövegmezőbe írja be egy URL-címet, amely a következő mintát használ:`https://<customer domain>.qmyzone.com/metadata.php`
         
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. A tényleges azonosítójú frissítheti ezeket az értékeket és bejelentkezés URL-CÍMÉT. Ügyfél [QPrism ügyfél-támogatási csoport](mailto:qsupport-ce@quatrro.com) beolvasni ezeket az értékeket. 

4. Létrehozásához a **metaadatok** URL-címe, tegye a következőket:

    a. Válassza ki **App regisztrációk**.
    
    ![Egyszeri bejelentkezés app regisztrációk konfigurálásához](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appregistrations.png)
   
    b. Válassza ki **végpontok** megnyitásához **végpontok** párbeszédpanel megnyitásához.  
    
    ![Egyszeri bejelentkezési végpont konfigurálása](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpointicon.png)

    c. Kattintson a Másolás gombra, majd másolja a **ÖSSZEVONÁSI METAADAT-dokumentum** URL-címet, és illessze be a Jegyzettömbbe.
    
    ![Egyszeri bejelentkezési végpont konfigurálása](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_endpoint.png)
     
    d. Most lépjen a tulajdonságlapján **QPrism**, és másolja a **Alkalmazásazonosító** használatával **másolása**. Majd illessze be azt a Jegyzettömbbe.
 
    ![Egyszeri bejelentkezés Alkalmazásazonosító konfigurálása](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_appid.png)

    e. Készítése a **metaadatainak URL-CÍMÉT** használatával a következő mintát:`<FEDERATION METADATA DOCUMENT url>?appid=<application id>` 

5. Kattintson a **Mentés** gombra.

    ![Egyszeri bejelentkezés mentési gomb konfigurálása](./media/active-directory-saas-qprism-tutorial/tutorial_general_400.png)
    
6. Az egyszeri bejelentkezés beállítása a **QPrism** oldalán, küldjön a **metaadatainak URL-CÍMÉT** számára a [QPrism támogatási csoport](mailto:qsupport-ce@quatrro.com). Azok a győződjön meg arról, hogy a SAML-alapú egyszeri bejelentkezés kapcsolat mindkét oldalán megfelelően beállítva.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg a állítja be az alkalmazást. Ez az alkalmazás a hozzáadása után a **Active Directory** > **vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott eléréséhez a dokumentáció a **konfigurációs** alsó szakasz. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD dokumentációjában beágyazott]( https://go.microsoft.com/fwlink/?linkid=845985).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure ad-ben:**

1. Az Azure portálon a bal oldali panelen válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-qprism-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-qprism-tutorial/create_aaduser_02.png)

3. Lehetőségre a **felhasználói** párbeszédpanel tetején a **minden felhasználó** párbeszédpanelen jelölje ki **Hozzáadás**.

    ![A Hozzáadás gombra.](./media/active-directory-saas-qprism-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen tegye a következőket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-qprism-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-qprism-test-user"></a>QPrism tesztfelhasználó létrehozása

Ebben a szakaszban egy QPrism Britta Simon nevű felhasználót hoz létre. Együttműködik az [QPrism támogatási csoport](mailto:qsupport-ce@quatrro.com) felhasználót is hozzáadhat a QPrism platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt. 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés QPrism Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**QPrism Britta Simon hozzárendelése:**

1. Az Azure portálon az alkalmazások nézet megnyitásához, és keresse meg a könyvtár nézet. Ugrás a **vállalati alkalmazások**, és válassza ki **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **QPrism**.

    ![Az alkalmazások listáját a QPrism hivatkozás](./media/active-directory-saas-qprism-tutorial/tutorial_qprism_app.png)  

3. A bal oldali menüben válasszon ki **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza a **Hozzáadás** lehetőséget. Ekkor a **hozzáadása hozzárendelés**, jelölje be **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a a **felhasználók** listája.

6. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **válasszon**.

7. A **hozzáadása hozzárendelés**, jelölje be **hozzárendelése**.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

A hozzáférési panelen QPrism csempe kiválasztásakor kell beolvasni automatikusan jelentkezett az QPrism alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-qprism-tutorial/tutorial_general_203.png

