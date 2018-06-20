---
title: 'Oktatóanyag: Azure Active Directoryval integrált Marketo |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Marketo között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b88c45f5-d288-4717-835c-ca965add8735
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/09/2017
ms.author: jeedes
ms.openlocfilehash: e7ff12938ea66bc218f71018247e70f0ebc1400d
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36226809"
---
# <a name="tutorial-azure-active-directory-integration-with-marketo"></a>Oktatóanyag: Azure Active Directoryval integrált Marketo

Ebben az oktatóanyagban elsajátíthatja a Marketo integrálása az Azure Active Directory (Azure AD).

A Marketo integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér a Marketo Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Marketo (egyszeri bejelentkezés) a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD rendszerrel történő integráció konfigurálása a Marketo, a következő elemeket kell:

- Az Azure AD szolgáltatásra
- A Marketo egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A Marketo hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-marketo-from-the-gallery"></a>A Marketo hozzáadása a gyűjteményből
Az Azure AD integrálása a Marketo konfigurálásához kell hozzáadnia a Marketo a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A Marketo hozzáadása a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Marketo**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/marketo-tutorial/tutorial_marketo_search.png)

5. Az eredmények panelen válassza ki a **Marketo**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/marketo-tutorial/tutorial_marketo_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a Marketo "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a Marketo tartozó felhasználót a felhasználó Azure AD-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Marketo közötti kapcsolat kapcsolatot kell létrehozni.

A Marketo, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Marketo tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[A Marketo tesztfelhasználó létrehozása](#creating-a-marketo-test-user)**  - való Britta Simon valami Marketo, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Marketo-alkalmazásban.

**A Marketo konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Marketo** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_samlbase.png)

3. Az a **Marketo-tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_url.png)

    a. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://saml.marketo.com/sp`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://login.marketo.com/saml/assertion/\<munchkinid\>`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója és a válasz URL-CÍMEN. Ügyfél [Marketo támogatási csoport](http://investors.marketo.com/contactus.cfm) beolvasni ezeket az értékeket.
 
4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_general_400.png)

6. A a **Marketo konfigurációs** kattintson **konfigurálása Marketo** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_configure.png) 

7. Ahhoz, hogy az alkalmazás Munchkin azonosítója, jelentkezzen be a rendszergazda hitelesítő adataival Marketo, majd hajtsa végre a következő műveleteket:
   
    a. Jelentkezzen be rendszergazdai hitelesítő adataival Marketo-alkalmazást.
   
    b. Kattintson a **Admin** gombra a felső navigációs ablaktáblán.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Az integráció menüben keresse meg és kattintson a **Munchkin hivatkozás**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_11.png)
   
    d. Másolja a Munchkin azonosítót, a képernyőn megjelenő, és fejezze be a válasz URL-CÍMEN az Azure AD konfigurálása varázsló.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_12.png) 

8. Az alkalmazás az SSO konfigurálásához kövesse az alábbi lépéseket:
   
    a. Jelentkezzen be rendszergazdai hitelesítő adataival Marketo-alkalmazást.
   
    b. Kattintson a **Admin** gombra a felső navigációs ablaktáblán.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Az integráció menüben keresse meg és kattintson **egyszeri bejelentkezés**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_07.png) 
   
    d. A SAML-beállítások engedélyezéséhez kattintson **szerkesztése** gombra.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_08.png) 
   
    e. **Engedélyezett** egyszeri bejelentkezés beállításait.
   
    f. Beillesztés a **SAML Entitásazonosító**, a a **kibocsátó azonosító** szövegmező.
   
    g. Az a **Entitásazonosító** szövegmező, adja meg az URL-címet `http://saml.marketo.com/sp`.
   
    h. Válassza ki a felhasználói azonosító helyének **névazonosítója elem**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_09.png)
   
    > [!NOTE]
    > Ha a felhasználói azonosító nem UPN-értéket, majd módosítsa az értéket a attribútum fülre.
   
    i. Töltse fel a tanúsítványt, amely már letöltötte az Azure AD konfigurálása varázsló. **Mentés** a beállításokat.
   
    j. Az átirányítási hibalapok beállításainak szerkesztése.
   
    k. Beillesztés a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **bejelentkezési URL-cím** szövegmező.
   
    l. Beillesztés a **Sign-Out URL-cím** a a **kijelentkezési URL-cím** szövegmező.
   
    m. Az a **hiba URL-cím**, másolása a **Marketo-példány URL-címe** kattintson **mentése** gombra kattintva mentse a beállításokat.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_10.png)

9. Ahhoz, hogy a felhasználók számára az egyszeri Bejelentkezést, hajtsa végre a következő műveletet:
   
    a. Jelentkezzen be rendszergazdai hitelesítő adataival Marketo-alkalmazást.
   
    b. Kattintson a **Admin** gombra a felső navigációs ablaktáblán.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 
   
    c. Keresse meg a **biztonsági** menüre, majd **bejelentkezési beállítások**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_13.png)
   
    d. Ellenőrizze a **szükséges SSO** beállítás és **mentése** a beállításokat.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_14.png)

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/marketo-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/marketo-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/marketo-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/marketo-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-marketo-test-user"></a>A Marketo tesztfelhasználó létrehozása

Ebben a szakaszban a Marketo Britta Simon nevű felhasználó hoz létre. kövesse az alábbi lépéseket az olyan felhasználók létrehozása az Marketo-platformokon.

1. Jelentkezzen be rendszergazdai hitelesítő adataival Marketo-alkalmazást.

2. Kattintson a **Admin** gombra a felső navigációs ablaktáblán.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_06.png) 

3. Keresse meg a **biztonsági** menüre, majd **felhasználók és szerepkörök**
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_19.png)  

4. Kattintson a **hívhat meg új felhasználói** hivatkozás a felhasználók lapon
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_15.png) 

5. Töltse ki a következő információkat a meghívót, új felhasználó varázsló
   
    a. Adja meg a felhasználó **E-mail** cím beviteli mezőben szereplő
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_16.png)
   
    b. Adja meg a **Keresztnév** a szövegmezőben
   
    c. Adja meg a **Vezetéknév** a szövegmezőben
   
    d. Kattintson a **Tovább** gombra

6. Az a **engedélyek** lapon jelölje be a **userRoles** kattintson **tovább**
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_17.png)
7. Kattintson a **küldése** gombra a felhasználó felkérést
   
    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_18.png)

8. Felhasználói az e-mail-értesítést kap, és kattintson a hivatkozásra, és módosítsa a jelszót a fiók aktiválása. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Marketo Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**A Marketo Britta Simon rendeléséhez hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Marketo**.

    ![Egyszeri bejelentkezés konfigurálása](./media/marketo-tutorial/tutorial_marketo_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Marketo csempére kattint, akkor kell beolvasása automatikusan bejelentkezett a Marketo-alkalmazásba.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/marketo-tutorial/tutorial_general_01.png
[2]: ./media/marketo-tutorial/tutorial_general_02.png
[3]: ./media/marketo-tutorial/tutorial_general_03.png
[4]: ./media/marketo-tutorial/tutorial_general_04.png

[100]: ./media/marketo-tutorial/tutorial_general_100.png

[200]: ./media/marketo-tutorial/tutorial_general_200.png
[201]: ./media/marketo-tutorial/tutorial_general_201.png
[202]: ./media/marketo-tutorial/tutorial_general_202.png
[203]: ./media/marketo-tutorial/tutorial_general_203.png

