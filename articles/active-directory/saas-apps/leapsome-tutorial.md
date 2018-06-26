---
title: 'Oktatóanyag: Azure Active Directoryval integrált Leapsome |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Leapsome között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: cb523e97-add8-4289-b106-927bf1a02188
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/22/2018
ms.author: jeedes
ms.openlocfilehash: b23a93db7912aa25b420157241c41533f4f48a27
ms.sourcegitcommit: 828d8ef0ec47767d251355c2002ade13d1c162af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2018
ms.locfileid: "36938407"
---
# <a name="tutorial-azure-active-directory-integration-with-leapsome"></a>Oktatóanyag: Azure Active Directoryval integrált Leapsome

Ebben az oktatóanyagban elsajátíthatja Leapsome integrálása az Azure Active Directory (Azure AD).

Leapsome integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Leapsome szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Leapsome (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Leapsome, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Leapsome egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Leapsome hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-leapsome-from-the-gallery"></a>A gyűjteményből Leapsome hozzáadása
Az Azure AD integrálása a Leapsome konfigurálásához kell hozzáadnia Leapsome a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Leapsome hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Leapsome**, jelölje be **Leapsome** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Leapsome](./media/leapsome-tutorial/tutorial_leapsome_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Leapsome.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Leapsome a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Leapsome közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a Leapsome tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Leapsome tesztfelhasználó létrehozása](#create-a-leapsome-test-user)**  - való Britta Simon valami Leapsome, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Leapsome alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Leapsome, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Leapsome** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/leapsome-tutorial/tutorial_leapsome_samlbase.png)

3. Az a **Leapsome tartomány és az URL-címek** területen tegye a következőket, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Leapsome tartomány és az URL-címek](./media/leapsome-tutorial/tutorial_leapsome_url.png)

    a. Az a **azonosító** szövegmezőhöz URL-címet írja be: `https://www.leapsome.com`

    b. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/assert`

4. Ellenőrizze **megjelenítése speciális URL-beállításainak** , és végezze el a következő lépés, ha szeretne beállítani az alkalmazás **SP** kezdeményezett mód:

    ![Az egyszeri bejelentkezés információk Leapsome tartomány és az URL-címek](./media/leapsome-tutorial/tutorial_leapsome_url1.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://www.leapsome.com/api/users/auth/saml/<CLIENTID>/login`
     
    > [!NOTE] 
    > Az előző válasz URL-CÍMEN és a bejelentkezési URL-cím nincs valós értékének. Később frissíteni ezeket használja a tényleges értékek, amelyek esetén, tekintse meg az oktatóanyag későbbi részében.

5. Leapsome alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen szereplő példán látható.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/leapsome-tutorial/tutorial_Leapsome_attribute.png)

6. A a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti ábrán látható módon, és hajtsa végre a következő lépéseket:
    
    | Attribútum neve | Attribútum értéke | Névtér |
    | ---------------| --------------- | --------- |   
    | Utónév | User.givenName | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | Vezetéknév | User.surname | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | cím | user.jobtitle | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |
    | kép | Az alkalmazott kép URL-címe | http://schemas.xmlsoap.org/ws/2005/05/identity/claims |

    > [!Note]
    > A kép attribútum értéke nem valódi. Frissítse ezt az értéket a tényleges kép URL-címe. Az érték ügyfél megszerezni [Leapsome ügyfél-támogatási csoport](mailto:support@leapsome.com).
    
    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/leapsome-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/leapsome-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.
    
    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.

    d. Az a **Namespace** szövegmező, írja be a névtér URI-azonosítója a sorhoz.
    
    e. Kattintson a **Ok**

7. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/leapsome-tutorial/tutorial_leapsome_certificate.png) 

8. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/leapsome-tutorial/tutorial_general_400.png)
    
9. A a **Leapsome konfigurációs** kattintson **konfigurálása Leapsome** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Leapsome konfiguráció](./media/leapsome-tutorial/tutorial_leapsome_configure.png)

10. Egy másik webes böngészőablakban jelentkezzen be Leapsome egy biztonsági-rendszergazdaként.

11. A jobb felső részén kattintson a beállítások embléma, és kattintson **rendszergazdai beállítások**. 

    ![Leapsome beállítása](./media/leapsome-tutorial/tutorial_leapsome_admin.png)

12. Kattintson a bal oldali menüsáv **egyszeri bejelentkezés (SSO)**, majd a a **SAML-alapú egyszeri bejelentkezést (SSO)** lapon hajtsa végre a következő lépéseket:
    
    ![Leapsome saml](./media/leapsome-tutorial/tutorial_leapsome_samlsettings.png)

    a. Válassza ki **engedélyezése SAML-alapú egyszeri bejelentkezést**.

    b. Másolás a **bejelentkezési URL-címet (a felhasználók itt pont bejelentkezési indítása)** értékét, és illessze be azt a **bejelentkezési URL-cím** textbox **Leapsome tartomány és az URL-címek** Azure-portál szakaszban.

    c. Másolás a **válasz URL-CÍMEN (az identitásszolgáltató recieves válaszát)** értékét, és illessze be azt a **válasz URL-CÍMEN** textbox **Leapsome tartomány és az URL-címek** Azure-portál szakaszban.

    d. Az a **Egyszeri bejelentkezési URL-címe (identitásszolgáltató által biztosított)** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure portálról másolt.

    e. Másolja a tanúsítványt--MEGKEZDÉSÉHEZ TANÚSÍTVÁNYT és a záró tanúsítvány--megjegyzések nélkül az Azure portálról letöltött, és illessze be a **(identitásszolgáltató által megadott) tanúsítvány** szövegmező.

    f. Kattintson a **egyszeri bejelentkezési beállítások**.
    
### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/leapsome-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/leapsome-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/leapsome-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/leapsome-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-leapsome-test-user"></a>Leapsome tesztfelhasználó létrehozása

Ebben a szakaszban egy Leapsome Britta Simon nevű felhasználót hoz létre. Együttműködve [Leapsome ügyfél-támogatási csoport](mailto:support@leapsome.com) adhat a felhasználók vagy a tartományban kell lennie a Leapsome platform szerepel az engedélyezési listán. Ha a tartomány a csapata által hozzáadott, felhasználók fogja lekérni automatikusan hozzáférést kapnak a Leapsome platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt. 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Leapsome Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Leapsome, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Leapsome**.

    ![Az alkalmazások listáját a Leapsome hivatkozás](./media/leapsome-tutorial/tutorial_leapsome_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Leapsome csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Leapsome alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/leapsome-tutorial/tutorial_general_01.png
[2]: ./media/leapsome-tutorial/tutorial_general_02.png
[3]: ./media/leapsome-tutorial/tutorial_general_03.png
[4]: ./media/leapsome-tutorial/tutorial_general_04.png

[100]: ./media/leapsome-tutorial/tutorial_general_100.png

[200]: ./media/leapsome-tutorial/tutorial_general_200.png
[201]: ./media/leapsome-tutorial/tutorial_general_201.png
[202]: ./media/leapsome-tutorial/tutorial_general_202.png
[203]: ./media/leapsome-tutorial/tutorial_general_203.png

