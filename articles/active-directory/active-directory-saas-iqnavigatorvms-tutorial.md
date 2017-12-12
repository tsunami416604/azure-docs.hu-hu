---
title: "Oktatóanyag: Azure Active Directory-integráció IQNavigator virtuális gépek |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és IQNavigator virtuális gépek között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: a8a09b25-dfa5-4c31-aea2-53bf1853b365
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: 82c91ba8fa340fb2b179d61200a69e94ab85b6d9
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-iqnavigator-vms"></a>Oktatóanyag: Azure Active Directory-integráció IQNavigator virtuális gépek

Ebben az oktatóanyagban elsajátíthatja IQNavigator virtuális gépek integrálása az Azure Active Directory (Azure AD).

IQNavigator virtuális gépek integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja az Azure AD, aki hozzáfér a IQNavigator virtuális Gépekhez
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett IQNavigator virtuális gépek (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs konfigurálásához IQNavigator virtuális gépek, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy IQNavigator virtuális gépek egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió Itt kaphat [próbaverzió ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből IQNavigator virtuális gépek hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-iqnavigator-vms-from-the-gallery"></a>A gyűjteményből IQNavigator virtuális gépek hozzáadása
Az Azure AD-be a IQNavigator virtuális integráció konfigurálásához szüksége IQNavigator virtuális gépek felvétele a kezelt SaaS-alkalmazások listáját a gyűjteményből.

**A gyűjteményből IQNavigator virtuális gépek hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **IQNavigator virtuális gépek**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_search.png)

5. Az eredmények panelen válassza ki a **IQNavigator virtuális gépek**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálhatja, és IQNavigator virtuális gépek az Azure AD az egyszeri bejelentkezés tesztelése "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó IQNavigator virtuális gépeken a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a IQNavigator virtuális gépek közötti kapcsolat kapcsolatot kell létrehozni.

Az IQNavigator virtuális Gépeken, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés IQNavigator virtuális gépek tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[IQNavigator virtuális gépek tesztfelhasználó létrehozása](#creating-a-iqnavigator-vms-test-user)**  - kell rendelkeznie a megfelelője a Britta Simon IQNavigator virtuális gépeken, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az IQNavigator virtuális gépek alkalmazásban.

**Konfigurálja az Azure AD az egyszeri bejelentkezés IQNavigator virtuális gépek, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **IQNavigator virtuális gépek** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_samlbase.png)

3. Az a **IQNavigator virtuális gépek tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url.png)

    a. Az a **azonosító** szövegmező, írja be az URL-cím:`iqn.com`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe:`https://<subdomain>.iqnavigator.com/security/login?client_name=https://sts.window.net/<instance name>`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak**, hajtsa végre a következő lépést:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_url1.png)

    Az a **állapot továbbítása** szövegmező, adja meg a következő minta használatával URL-címe:`https://<subdomain>.iqnavigator.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges válasz URL-CÍMEN és továbbító állapottal. Ügyfél [IQNavigator virtuális gépek ügyfél-támogatási csoport](https://www.beeline.com/iqn-product-support/) beolvasni ezeket az értékeket. 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_400.png)

6. Létrehozásához a **metaadatok** URL-címe, hajtsa végre a következő lépéseket:

    a. Kattintson a **App regisztrációk**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_appregistrations.png)
   
    b. Kattintson a **végpontok** megnyitásához **végpontok** párbeszédpanel megnyitásához.  
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_endpointicon.png)

    c. Kattintson a Másolás gombra másolása **ÖSSZEVONÁSI METAADAT-dokumentum** URL-címet, és illessze be a Jegyzettömbbe.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_endpoint.png)
     
    d. Most lépjen a tulajdonságlapján **IQNavigator virtuális gépek** , és másolja a **alkalmazásazonosító** használatával **másolási** gombra, majd illessze be a Jegyzettömbbe.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_appid.png)

    e. Készítése a **metaadatainak URL-CÍMÉT** a következő minta használatával:`<FEDERATION METADATA DOCUMENT url>?appid=<application id>`

7. IQNavigator alkalmazás a névazonosítója jogcímek az egyedi felhasználói azonosító értéket várt. Ügyfél leképezheti a névazonosítója jogcímet a megfelelő értéket. Ebben az esetben azt a felhasználó hozzárendelt. UserPrincipalName bemutató erre a célra. Azonban a szervezet beállításainak megfelelően hozzá kell leképezni a megfelelő értéket.   

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_attribute.png)

8. A a **IQNavigator virtuális gépek konfigurációs** kattintson **konfigurálása IQNavigator virtuális gépek** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_configure.png) 

9. Egyszeri bejelentkezés konfigurálása **IQNavigator virtuális gépek** oldalon kell küldeniük a **metaadatainak URL-CÍMÉT**, **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** számára[ IQNavigator virtuális gépek támogatási csoport](https://www.beeline.com/iqn-product-support/). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-iqnavigatorvms-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-iqnavigatorvms-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-iqnavigatorvms-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-iqnavigatorvms-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-iqnavigator-vms-test-user"></a>IQNavigator virtuális gépek tesztfelhasználó létrehozása

Ez a szakasz célja egy felhasználó Britta Simon meghívta IQNavigator virtuális gépek létrehozásához. Együttműködve [IQNavigator virtuális gépek támogatási csoport](https://www.beeline.com/iqn-product-support/) a felhasználók hozzáadása a IQNavigator virtuális gépek fiók.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés IQNavigator virtuális gépek Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése IQNavigator virtuális Gépeket, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **IQNavigator virtuális gépek**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_iqnavigatorvms_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen IQNavigator virtuális gépek csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az IQNavigator virtuális gépek alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-iqnavigatorvms-tutorial/tutorial_general_203.png

