---
title: 'Oktatóanyag: Azure Active Directory-integráció AppNeta Teljesítményfigyelővel |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Teljesítményfigyelő AppNeta között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 643a45fb-d6fc-4b32-b721-68899f8c7d44
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/10/2018
ms.author: jeedes
ms.openlocfilehash: 6c06b2819516664a367ca31925366eabd05e688e
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
---
# <a name="tutorial-azure-active-directory-integration-with-appneta-performance-monitor"></a>Oktatóanyag: Azure Active Directoryval integrált AppNeta Teljesítményfigyelő

Ebben az oktatóanyagban elsajátíthatja AppNeta Teljesítményfigyelő integrálása az Azure Active Directory (Azure AD).

Teljesítményfigyelő AppNeta integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér AppNeta Teljesítményfigyelő szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett AppNeta teljesítményének figyelése (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integrációs konfigurálásához AppNeta Teljesítményfigyelővel, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy AppNeta Teljesítményfigyelő egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. AppNeta Teljesítményfigyelő hozzáadását a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-appneta-performance-monitor-from-the-gallery"></a>AppNeta Teljesítményfigyelő hozzáadását a gyűjteményből
Az Azure AD integrálása a AppNeta Teljesítményfigyelő konfigurálásához kell hozzáadnia AppNeta Teljesítményfigyelő a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből AppNeta Teljesítményfigyelő hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **AppNeta Teljesítményfigyelő**, jelölje be **AppNeta Teljesítményfigyelő** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában AppNeta Teljesítményfigyelő](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján AppNeta Teljesítményfigyelővel.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó AppNeta Teljesítményfigyelőben a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Teljesítményfigyelőben AppNeta közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD az egyszeri bejelentkezés AppNeta Teljesítményfigyelővel tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy AppNeta Teljesítményfigyelő tesztfelhasználó](#create-an-appneta-performance-monitor-test-user)**  - kell rendelkeznie a megfelelője a Britta Simon AppNeta Teljesítményfigyelőben, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez AppNeta Teljesítményfigyelő alkalmazásában.

**Az Azure AD az egyszeri bejelentkezés AppNeta Teljesítményfigyelővel megadásához a következő lépésekkel:**

1. Az Azure portálon a a **AppNeta Teljesítményfigyelő** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_samlbase.png)

3. Az a **AppNeta teljesítményt figyelő tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk AppNeta teljesítményt figyelő tartomány és az URL-címek](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.pm.appneta.com`

    b. Az a **azonosító** szövegmező, írja be az értéket: `PingConnect`

    > [!NOTE] 
    > A bejelentkezési URL-cím értéke nincs valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címet. Ügyfél [AppNeta teljesítményt figyelő ügyfél-támogatási csoport](mailto:support@appneta.com) lekérni ezt az értéket. 

5. A Teljesítményfigyelő AppNeta alkalmazás a SAML helyességi feltételek egy meghatározott formátumban, amelyek megkövetelik olyan egyéni attribútum-leképezésekhez hozzáadása a SAML-jogkivonat attribútumok konfigurációs vár. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-appneta-tutorial/attribute.png)

6. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, az előző ábrán látható módon, és hajtsa végre a következő lépéseket:
           
    | Attribútum neve | Attribútum értéke |
    | ---------------| ----------------|
    | Utónév| User.givenName|
    | Vezetéknév| User.surname|
    | e-mail| User.userPrincipalName|
    | név| User.userPrincipalName|
    | csoportok   | User.assignedroles |
    | Telefonszám| User.telephoneNumber |
    | cím| user.jobtitle|

    > [!NOTE]
    > "csoportok" hivatkozik a biztonsági csoport Appneta, amely az Azure AD egy szerepkör van hozzárendelve. Tekintse meg [ez](https://docs.microsoft.com/azure/active-directory/active-directory-enterprise-app-role-management) doc, amely ismerteti, hogyan hozzon létre egyéni szerepkörök az Azure ad-ben.
        
    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-appneta-tutorial/tutorial_attribute_04.png)
    
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-appneta-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.

    d. Névtér hagyja üresen.
    
    e. Kattintson az **OK** gombra.  

4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-appneta-tutorial/tutorial_general_400.png)

6. Egyszeri bejelentkezés konfigurálása **AppNeta Teljesítményfigyelő** oldalon kell küldeniük a letöltött **metaadatainak XML-kódja** való [AppNeta Teljesítményfigyelő támogatási csoport](mailto:support@appneta.com). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-appneta-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-appneta-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/active-directory-saas-appneta-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/active-directory-saas-appneta-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-appneta-performance-monitor-test-user"></a>Hozzon létre egy AppNeta Teljesítményfigyelő tesztfelhasználó számára

Ez a szakasz célja a Teljesítményfigyelőben AppNeta Britta Simon nevű felhasználó létrehozásához. AppNeta Teljesítményfigyelő támogatja just-in-time kiosztást, amely alapértelmezés szerint van engedélyezve. Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre az AppNeta Teljesítményfigyelő elérésére, ha még nem létezik tett kísérlet során.
>[!Note]
>Ha manuálisan hozzon létre egy felhasználó van szüksége, forduljon a [AppNeta Teljesítményfigyelő támogatási csoport](mailto:support@appneta.com).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés AppNeta Teljesítményfigyelő Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése AppNeta Teljesítményfigyelő, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **AppNeta Teljesítményfigyelő**.

    ![Az alkalmazások listáját a AppNeta Teljesítményfigyelő hivatkozás](./media/active-directory-saas-appneta-tutorial/tutorial_appneta_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen AppNeta Teljesítményfigyelő csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az AppNeta Teljesítményfigyelő alkalmazáshoz.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-appneta-tutorial/tutorial_general_203.png

