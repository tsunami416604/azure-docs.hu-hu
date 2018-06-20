---
title: 'Oktatóanyag: Azure Active Directory-integráció OfficeSpace szoftverrel |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és OfficeSpace szoftverek között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 95d8413f-db98-4e2c-8097-9142ef1af823
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 5e77a8d557c44b76335ac4abab2cb19e099c7d8b
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36215919"
---
# <a name="tutorial-azure-active-directory-integration-with-officespace-software"></a>Oktatóanyag: Azure Active Directory-integráció OfficeSpace szoftverrel

Ebben az oktatóanyagban elsajátíthatja OfficeSpace szoftver integrálása az Azure Active Directory (Azure AD).

OfficeSpace szoftver integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér OfficeSpace szoftver szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett OfficeSpace szoftverre (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása OfficeSpace szoftverrel, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy OfficeSpace szoftver egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből OfficeSpace szoftver hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-officespace-software-from-the-gallery"></a>A gyűjteményből OfficeSpace szoftver hozzáadása
Az Azure AD integrálása a OfficeSpace szoftver konfigurálásához kell hozzáadnia OfficeSpace szoftver a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**OfficeSpace hozzáadása a gyűjteményből, hajtsa végre a következő lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **OfficeSpace szoftver**, jelölje be **OfficeSpace szoftver** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában OfficeSpace szoftver](./media/officespace-tutorial/tutorial_officespace_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálása és tesztelése az Azure AD az egyszeri bejelentkezés OfficeSpace szoftverrel "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó OfficeSpace szoftver a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó OfficeSpace szoftver közötti kapcsolat kapcsolatot kell létrehozni.

OfficeSpace szoftver, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés OfficeSpace szoftverrel tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[OfficeSpace szoftver tesztfelhasználó létrehozása](#create-a-officespace-software-test-user)**  - való egy megfelelője a Britta Simon OfficeSpace szoftver, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a OfficeSpace alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD az egyszeri bejelentkezés OfficeSpace szoftverrel, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **OfficeSpace szoftver** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/officespace-tutorial/tutorial_officespace_samlbase.png)

3. Az a **OfficeSpace szoftver tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk OfficeSpace szoftver tartomány és az URL-címek](./media/officespace-tutorial/tutorial_officespace_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<company name>.officespacesoftware.com/users/sign_in/saml`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `<company name>.officespacesoftware.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [OfficeSpace szoftver ügyfél-támogatási csoport](mailto:support@officespacesoftware.com) beolvasni ezeket az értékeket. 

4. OfficeSpace alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. Állítsa be a következő jogcímeket ehhez az alkalmazáshoz. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a.
    
    ![Attribútum konfigurálása](./media/officespace-tutorial/tutorial_officespace_attribute.png)

5. Az a **felhasználói attribútumok** a szakasz a **egyszeri bejelentkezés** párbeszédablakban válassza **user.mail** , **felhasználói azonosító** és az alábbi táblázatban szereplő minden egyes sorhoz kapcsolódóan végezze el a következő lépéseket:
    
    | Attribútum neve | Attribútum értéke |
    | --- | --- |    
    | e-mailben | user.mail |
    | név | user.displayname |
    | first_name | User.givenName |
    | last_name | User.surname |

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Konfigurálása hozzáadása ](./media/officespace-tutorial/tutorial_attribute_04.png)

    ![Attribútum konfigurálása](./media/officespace-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.
    
    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.
    
    d. Kattintson a **Ok**
 
6. Az a **SAML-aláíró tanúsítványa** szakaszban, másolja a **UJJLENYOMAT** érték a tanúsítvány.

    ![A tanúsítvány letöltési hivatkozását](./media/officespace-tutorial/tutorial_officespace_certificate.png) 

7. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/officespace-tutorial/tutorial_general_400.png)

8. A a **OfficeSpace szoftverkonfigurációt** kattintson **OfficeSpace szoftver konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![OfficeSpace szoftverkonfigurációjának összeállítása](./media/officespace-tutorial/tutorial_officespace_configure.png) 

9. Egy másik webes böngészőablakban bejelentkezni a OfficeSpace szoftver Bérlői rendszergazda.

10. Ugrás a **beállítások** kattintson **összekötők**.

    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása](./media/officespace-tutorial/tutorial_officespace_002.png)

11. Kattintson a **SAML-alapú hitelesítés**.

    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása](./media/officespace-tutorial/tutorial_officespace_003.png)

12. Az a **SAML-alapú hitelesítés** területen tegye a következőket:

    ![Alkalmazás ügyféloldali egyszeri bejelentkezés konfigurálása](./media/officespace-tutorial/tutorial_officespace_004.png)

    a. Az a **kijelentkezési szolgáltató URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím** ami Azure-portálon másolta.

    b. Az a **ügyfél idp célként megadott url** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.

    c. Beillesztés a **ujjlenyomat** érték, amely az Azure portálról másolta a **ügyfél IDP tanúsítvány-ujjlenyomat** szövegmező. 

    d. Kattintson a **beállítások mentése**.


> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/officespace-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/officespace-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/officespace-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/officespace-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-officespace-software-test-user"></a>OfficeSpace szoftver tesztfelhasználó létrehozása

Ez a szakasz célja OfficeSpace szoftver Britta Simon nevű felhasználót létrehozni. OfficeSpace szoftver, amellyel just-in-time átadása, amely alapértelmezés szerint van engedélyezve.

Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre az OfficeSpace szoftver elérésére, ha még nem létezik tett kísérlet során.

> [!NOTE]
> Ha manuálisan hozzon létre egy felhasználó van szüksége, lépjen kapcsolatba kell [OfficeSpace szoftver támogatási csoport](mailto:support@officespacesoftware.com).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés által biztosított hozzáférés OfficeSpace szoftver használatára.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése OfficeSpace szoftver, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **OfficeSpace szoftver**.

    ![Az alkalmazások listáját a OfficeSpace szoftver hivatkozás](./media/officespace-tutorial/tutorial_officespace_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen OfficeSpace szoftver csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az OfficeSpace szoftverfrissítések alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/officespace-tutorial/tutorial_general_01.png
[2]: ./media/officespace-tutorial/tutorial_general_02.png
[3]: ./media/officespace-tutorial/tutorial_general_03.png
[4]: ./media/officespace-tutorial/tutorial_general_04.png

[100]: ./media/officespace-tutorial/tutorial_general_100.png

[200]: ./media/officespace-tutorial/tutorial_general_200.png
[201]: ./media/officespace-tutorial/tutorial_general_201.png
[202]: ./media/officespace-tutorial/tutorial_general_202.png
[203]: ./media/officespace-tutorial/tutorial_general_203.png

