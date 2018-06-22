---
title: 'Oktatóanyag: Azure Active Directoryval integrált Shmoop a iskolákat |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az iskolai Shmoop között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 1d75560a-55b3-42e9-bda1-92b01c572d8e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/23/2018
ms.author: jeedes
ms.openlocfilehash: e9dc784d84c43600f3ba4fda92e023c01afe8995
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301140"
---
# <a name="tutorial-azure-active-directory-integration-with-shmoop-for-schools"></a>Oktatóanyag: Azure Active Directoryval integrált Shmoop a iskolákat

Ebben az oktatóanyagban elsajátíthatja a iskolákat Shmoop integrálása az Azure Active Directory (Azure AD).

Shmoop a iskolákat integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD-hozzáféréssel rendelkező Shmoop a iskolákat szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Shmoop az iskola a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen--az Azure-portálon kezelheti.

Az Azure AD SaaS integrálásáról további információért lásd: [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Shmoop a iskolákat, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Shmoop a iskolákat egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Nem ajánlott, éles környezetben teszteléséhez lépéseit az oktatóanyag segítségével.

Ez az oktatóanyag lépéseit teszteléséhez a következőket javasoljuk:

- A termelési evironment használ, csak akkor, ha szükséges.
- Első egy [ingyenes egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/) Ha még nem telepítette az Azure AD próbaverziójának környezetben.

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Az ebben az oktatóanyagban a forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Shmoop a iskolákat hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="add-shmoop-for-schools-from-the-gallery"></a>Adja hozzá a Shmoop az iskola a gyűjteményből
Az Azure AD integrálása a Shmoop a iskolákat konfigurálásához kell hozzáadnia Shmoop a iskolákat a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Shmoop a iskolákat hozzáadásához tegye a következőket:**

1. Az a [Azure-portálon](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Ugrás a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Egy új alkalmazást szeretne telepíteni, válassza ki a **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Shmoop a iskolákat**. Válassza ki **Shmoop a iskolákat** az eredményeket, majd válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Shmoop a iskolákat](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban konfigurálása, és tesztelés az Azure AD az egyszeri bejelentkezés Shmoop a iskolákat "Britta Simon." nevű tesztfelhasználó alapján

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, aki a párjukhoz felhasználó Shmoop az iskolai egy felhasználó számára az Azure AD. Más szóval kell egy Azure AD-felhasználó és a kapcsolódó felhasználó a Shmoop az iskolai közötti kapcsolatot.

Az Azure AD egyszeri bejelentkezést a Shmoop a iskolákat tesztelése és konfigurálása, végezze el a következő építőelemeket:

1. [Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy a felhasználók számára a szolgáltatás használatához.
2. [Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. [Shmoop a iskolákat tesztfelhasználó létrehozása](#create-a-shmoop-for-schools-test-user) való Britta Simon valami Shmoop a iskolákat, amely csatolva van a felhasználó az Azure AD ábrázolását.
4. [Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) , hogy működik-e a konfiguráció ellenőrzése.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Shmoop az iskolai alkalmazásokhoz.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Shmoop a iskolákat, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Shmoop a iskolákat** alkalmazás integrációs lapon jelölje be **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédpanelen, a legördülő menü alatti **egyszeri bejelentkezés mód**, jelölje be **SAML-alapú bejelentkezés**.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_samlbase.png)

3. Az a **Shmoop a iskolákat tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_url.png)

    a. Az a **bejelentkezési URL-cím** mezőbe írja be a következő mintát olyan URL-címe: `https://schools.shmoop.com/public-api/saml2/start/<uniqueid>`

    b. Az a **azonosító** mezőbe írja be a következő mintát olyan URL-címe: `https://schools.shmoop.com/<uniqueid>`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Lépjen kapcsolatba a [Shmoop a iskolákat ügyfél-támogatási csoport](mailto:support@shmoop.com) beolvasni ezeket az értékeket. 
 
4. A Shmoop a iskolákat alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. A következő jogcímek alkalmazás konfigurálása. Ezek az attribútumok értékének kezelheti a **felhasználói attribútumok** szakaszban az alkalmazás-integráció lapján. Az alábbi képernyőfelvételen látható a helyességi feltételek konfigurálása:

    ![Egyszeri bejelentkezés konfigurálása](./media/shmoopforschools-tutorial/tutorial_attribute.png)

    > [!NOTE]
    > Az iskolai Shmoop két szerepkörök támogatja a felhasználók számára: **tanárának** és **Student**. Állítsa be ezeket a szerepköröket az Azure ad-ben, hogy a felhasználók a megfelelő szerepkörök rendelhetők. Szerepkörök konfigurálása az Azure AD ismertetése: [RBAC és az Azure-portálon-hozzáférés kezelése](../../role-based-access-control/role-assignments-portal.md).
    
5. Az a **felhasználói attribútumok** szakasz a **egyszeri bejelentkezés** párbeszédpanel a SAML-jogkivonat attribútum adja meg az előző ábrán látható módon.  Ezután a következő lépéseket:

    | Attribútum neve | Attribútum-érték |
    | -------------- | --------------- |
    | szerepkör           | User.assignedroles |

    a. Lehetőségre a **attribútum hozzáadása** párbeszédpanelen jelölje ki **Hozzáadás attribútum**.
    
    ![Egyszeri bejelentkezés konfigurálása ](./media/shmoopforschools-tutorial/tutorial_attribute_04.png)
    
    ![Egyszeri bejelentkezés konfigurálása](./media/shmoopforschools-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** mezőbe írja be az adott sorhoz feltüntetett attribútumot nevet.
    
    c. Az a **érték** listára, válassza ki az adott sorhoz feltüntetett attribútumot érték.

    d. Hagyja a **Namespace** mező üres.
    
    e. Válassza ki **Ok**.

6. Válassza ki a **Mentés** gombot.

    ![Egyszeri bejelentkezés konfigurálása](./media/shmoopforschools-tutorial/tutorial_general_400.png)

7. Az a **SAML-aláíró tanúsítványa** területen kattintson a Másolás gombra másolása **alkalmazás összevonási metaadatainak URL-címe** és illessze be a Jegyzettömbbe.

    ![A tanúsítvány letöltési hivatkozását](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_certificate.png)

8. Egyszeri bejelentkezés konfigurálása a **Shmoop a iskolákat** oldalon kell küldeniük a **alkalmazás összevonási metaadatainak URL-címét** való a [Shmoop a iskolákat támogatási csoport](mailto:support@shmoop.com).

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja az Azure portálon Britta Simon nevű tesztfelhasználó létrehozása.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure ad-ben, a következő lépéseket:**

1. Az Azure portálon a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/shmoopforschools-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**. Válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/shmoopforschools-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/shmoopforschools-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanel mezőben a következő lépéseket:

    ![A felhasználó párbeszédpanel](./media/shmoopforschools-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-shmoop-for-schools-test-user"></a>Shmoop a iskolákat tesztfelhasználó létrehozása

Ez a szakasz célja Shmoop a iskolákat Britta Simon nevű felhasználót létrehozni. Shmoop a iskolákat támogatja közvetlenül az időponthoz kötött kiosztást, amely alapértelmezés szerint engedélyezve van. Nincs ebben a szakaszban az Ön művelet elem. Ha egy új felhasználó még nem létezik, Shmoop a iskolákat elérésére tett kísérlet során létrejön.

>[!NOTE]
>Ha a felhasználó manuális létrehozása, forduljon a [Shmoop a iskolákat támogatási csoport](mailto:support@shmoop.com).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Britta Simon hozzáférést biztosít az iskolai Shmoop által használandó Azure egyszeri bejelentkezés engedélyezése.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Shmoop a iskolákat, tegye a következőket:**

1. Nyissa meg az alkalmazások megtekintése az Azure-portálon. Ezután lépjen **vállalati alkalmazások** a könyvtár nézetben.  Válassza ki, **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Shmoop a iskolákat**.

    ![Az alkalmazások listáját a Shmoop a iskolákat hivatkozás](./media/shmoopforschools-tutorial/tutorial_shmoopforschools_app.png)  

3. A bal oldali menüben válasszon ki **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza ki a **Hozzáadás** gombra. Ezt követően a a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki **felhasználók és csoportok**.

    ![A hozzárendelés hozzáadása panelen][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a felhasználók listában.

6. Az a **felhasználók és csoportok** párbeszédpanel, kattintson a **válasszon** gombra. 

7. Az a **hozzáadása hozzárendelés** párbeszédpanelen jelölje ki a **hozzárendelése** gombra.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési Panel segítségével tesztelheti.

Ha bejelöli a **Shmoop a iskolákat** csempére a hozzáférési Panel kell beolvasni automatikusan jelentkezett be a Shmoop a iskolákat alkalmazás.

A hozzáférési panel kapcsolatos további információkért lásd: [a hozzáférési panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az SaaS-alkalmazások integrálása az Azure Active Directory bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/shmoopforschools-tutorial/tutorial_general_01.png
[2]: ./media/shmoopforschools-tutorial/tutorial_general_02.png
[3]: ./media/shmoopforschools-tutorial/tutorial_general_03.png
[4]: ./media/shmoopforschools-tutorial/tutorial_general_04.png

[100]: ./media/shmoopforschools-tutorial/tutorial_general_100.png

[200]: ./media/shmoopforschools-tutorial/tutorial_general_200.png
[201]: ./media/shmoopforschools-tutorial/tutorial_general_201.png
[202]: ./media/shmoopforschools-tutorial/tutorial_general_202.png
[203]: ./media/shmoopforschools-tutorial/tutorial_general_203.png

