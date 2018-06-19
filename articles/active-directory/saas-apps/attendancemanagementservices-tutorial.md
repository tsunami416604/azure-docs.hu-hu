---
title: 'Oktatóanyag: Azure Active Directory-integráció jelenlét szolgáltatásokhoz |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a jelenléti szolgáltatások között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 1f56e612-728b-4203-a545-a81dc5efda00
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: jeedes
ms.openlocfilehash: e54eaec680ace9f74624c8ec29df62bfc9c1addb
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923395"
---
# <a name="tutorial-azure-active-directory-integration-with-attendance-management-services"></a>Oktatóanyag: Azure Active Directory-integráció a jelenléti szolgáltatások

Ebben az oktatóanyagban elsajátíthatja jelenlét szolgáltatások integrálása az Azure Active Directory (Azure AD).

Jelenlét szolgáltatások integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure ad-ben mutató szolgáltatások hozzáféréssel rendelkező szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett jelenlét szolgáltatásoknak (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a jelenléti szolgáltatások, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy mutató szolgáltatások egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Jelenlét szolgáltatások hozzáadásával a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-attendance-management-services-from-the-gallery"></a>Jelenlét szolgáltatások hozzáadásával a gyűjteményből
Az Azure AD integrálása a jelenléti szolgáltatások konfigurálásához kell hozzáadnia jelenlét szolgáltatások a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A felügyeleti szolgáltatások jelenlét a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **jelenlét szolgáltatások**, jelölje be **jelenlét szolgáltatások** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában jelenlét szolgáltatások](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján jelenlét szolgáltatások.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó jelenlét felügyeleti szolgáltatások területén a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a jelenléti szolgáltatások közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a jelenléti szolgáltatások tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy jelenlét szolgáltatások tesztfelhasználó](#create-an-attendance-management-service-test-user)**  - való egy megfelelője a Britta Simon jelenlét szolgáltatások, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez jelenlét szolgáltatások alkalmazásában.

**Konfigurálja az Azure AD az egyszeri bejelentkezés jelenlét szolgáltatásokhoz, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **jelenlét szolgáltatások** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_samlbase.png)

3. Az a **jelenlét felügyeleti szolgáltatások tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk jelenlét felügyeleti szolgáltatások tartomány és az URL-címek](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://id.obc.jp/<tenant information >/`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://id.obc.jp/<tenant information >/`

    > [!NOTE]
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [jelenlét Management Services Client támogatási csoport](http://www.obcnet.jp/) beolvasni ezeket az értékeket.

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/attendancemanagementservices-tutorial/tutorial_general_400.png)

6. A a **jelenlét Felügyeletkonfigurálási szolgáltatások** kattintson **jelenlét szolgáltatások konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Felügyeleti jelenlét konfigurálása](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_configure.png) 

7. Egy másik böngészőablakban bejelentkezés és a jelenléti szolgáltatások vállalati hely számára rendszergazdaként.

8. Kattintson a **SAML-alapú hitelesítés** alatt a **biztonsági felügyeleti szakasz**.

    ![Felügyeleti jelenlét konfigurálása](./media/attendancemanagementservices-tutorial/user1.png)

9. Hajtsa végre a következő lépéseket:

    ![Felügyeleti jelenlét konfigurálása](./media/attendancemanagementservices-tutorial/user2.png)

    a. Válassza ki **használható SAML-alapú hitelesítés**.

    b. Az a **azonosítója** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító**, amely az Azure-portálon másolta. 

    c. Az a **hitelesítési végponti URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálon másolta.

    d. Kattintson a **válasszon ki egy fájlt** töltse fel a tanúsítványt, amely letöltötte az Azure AD.

    e. Válassza ki **jelszó-hitelesítés letiltása**.

    f. Kattintson a **regisztrációs**

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor! Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/attendancemanagementservices-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/attendancemanagementservices-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/attendancemanagementservices-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/attendancemanagementservices-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-attendance-management-services-test-user"></a>Egy mutató szolgáltatások tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók jelenlét szolgáltatások bejelentkezni, akkor ki kell építenie jelenlét-Management szolgáltatás. Jelenlét felügyeleti szolgáltatások esetében kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be rendszergazdaként a jelenléti szolgáltatások vállalati webhely.

2. Kattintson a **felhasználókezelés** alatt a **biztonsági felügyeleti szakasz**.

    ![Alkalmazott hozzáadása](./media/attendancemanagementservices-tutorial/user5.png)

3. Kattintson a **új szabályok bejelentkezési**.

    ![Alkalmazott hozzáadása](./media/attendancemanagementservices-tutorial/user3.png)

4. Az a **OBCiD információk** területen tegye a következőket:

    ![Alkalmazott hozzáadása](./media/attendancemanagementservices-tutorial/user4.png)

    a. Az a **OBCiD** szövegmezőben, az e-mailt a felhasználó típusát, például **BrittaSimon@contoso.com**.

    b. Az a **jelszó** szövegmező, írja be a felhasználó jelszavát.

    c. Kattintson a **regisztrációs**


### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon használandó által biztosított hozzáférés jelenlét szolgáltatások, Azure egyszeri bejelentkezés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése jelenlét szolgáltatások, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **jelenlét szolgáltatások**.

    ![Az alkalmazások listáját a szolgáltatások mutató hivatkozásra](./media/attendancemanagementservices-tutorial/tutorial_attendancemanagementservices_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési Panel jelenlét szolgáltatások mozaik gombra kattint, akkor kell beolvasása automatikusan bejelentkezett jelenlét szolgáltatások Alkalmazásmódosítások.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/attendancemanagementservices-tutorial/tutorial_general_01.png
[2]: ./media/attendancemanagementservices-tutorial/tutorial_general_02.png
[3]: ./media/attendancemanagementservices-tutorial/tutorial_general_03.png
[4]: ./media/attendancemanagementservices-tutorial/tutorial_general_04.png

[100]: ./media/attendancemanagementservices-tutorial/tutorial_general_100.png

[200]: ./media/attendancemanagementservices-tutorial/tutorial_general_200.png
[201]: ./media/attendancemanagementservices-tutorial/tutorial_general_201.png
[202]: ./media/attendancemanagementservices-tutorial/tutorial_general_202.png
[203]: ./media/attendancemanagementservices-tutorial/tutorial_general_203.png

