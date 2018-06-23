---
title: 'Oktatóanyag: Azure Active Directory-integráció a képernyőfelvétel-O-felosztásban |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a képernyőfelvétel-O-értéket.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 525ad47d-5488-40e2-aeaf-ae6183745682
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/21/2018
ms.author: jeedes
ms.openlocfilehash: 7212e0b07b525286f0b194a53c6780269630ad9c
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320729"
---
# <a name="tutorial-azure-active-directory-integration-with-screencast-o-matic"></a>Oktatóanyag: Képernyőfelvétel-O-értéket az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a képernyőfelvétel-O-értéket az Azure Active Directoryval (Azure AD).

Képernyőfelvétel-O-felosztásban integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Azt is szabályozhatja az Azure AD, aki hozzáfér a képernyőfelvétel-O-értéket.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a képernyőfelvétel-O-értéket (egyszeri bejelentkezés) a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a képernyőfelvétel-O-értéket, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- A képernyőfelvétel-O-értéket az egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Képernyőfelvétel-O-felosztásban hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-screencast-o-matic-from-the-gallery"></a>Képernyőfelvétel-O-felosztásban hozzáadása a gyűjteményből
Az Azure AD integrálása a képernyőfelvétel-O-felosztásban konfigurálásához kell hozzáadnia képernyőfelvétel-O-értéket a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a képernyőfelvétel-O-értéket a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **képernyőfelvétel-O-értéket**, jelölje be **képernyőfelvétel-O-felosztásban** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Képernyőfelvétel-O-értéket az eredménylistában](./media/screencast-tutorial/tutorial_screencast_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés képernyőfelvétel-O-értéket "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD számára a képernyőfelvétel-O-értéket a partner felhasználót egy felhasználó számára az Azure AD kell. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a képernyőfelvétel-O-felosztásban közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a képernyőfelvétel-O-felosztásban, tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Képernyőfelvétel-O-felosztásban tesztfelhasználó létrehozása](#create-a-screencast-o-matic-test-user)**  - való Britta Simon egy megfelelője a képernyőfelvétel-O-értéket, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a képernyőfelvétel-O-felosztásban alkalmazásban egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezést a képernyőfelvétel-O-értéket, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **képernyőfelvétel-O-felosztásban** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/screencast-tutorial/tutorial_screencast_samlbase.png)

3. Az a **képernyőfelvétel-O-automatikus tartomány- és URL-címek** területen tegye a következőket:

    ![Képernyőfelvétel-O-automatikus tartomány- és URL-címeket az egyszeri bejelentkezés információk](./media/screencast-tutorial/tutorial_screencast_url.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://screencast-o-matic.com/<InstanceName>`

    > [!NOTE] 
    > A bejelentkezési URL-cím értéke nincs valós. Frissítse az értéket a tényleges bejelentkezési URL-címet. Ügyfél [képernyőfelvétel-O-felosztásban, ügyfél-támogatási csoport](mailto:support@screencast-o-matic.com) az értéket be kell olvasni. 
 
4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/screencast-tutorial/tutorial_screencast_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/screencast-tutorial/tutorial_general_400.png)

6. Egy másik webes böngészőablakban, jelentkezzen be a képernyőfelvétel-O-felosztásban rendszergazdaként.

7. Kattintson a **előfizetés**.

    ![Az előfizetés](./media/screencast-tutorial/tutorial_screencast_sub.png)

8. A **lapot** szakaszban kattintson **telepítő**.

    ![A hozzáférés](./media/screencast-tutorial/tutorial_screencast_setup.png)

9. Az a **hozzáférés telepítőből**, hajtsa végre a következő lépéseket:

    * A **URL-CÍMEN** területen írja be a példánynév a megadott szövegmezőben.

    ![A hozzáférés](./media/screencast-tutorial/tutorial_screencast_access.png)

    * Válassza ki **szükséges tartományi felhasználó** alatt **SAML felhasználói korlátozása (nem kötelező)** szakasz.

    * A **IDP metaadatok XML-fájl feltöltése**, kattintson a **Choose File** a metaadatokat, amelyek az Azure-portálról letöltött feltölteni.

    * Kattintson az **OK** gombra. 

    ![A hozzáférés](./media/screencast-tutorial/tutorial_screencast_save.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/screencast-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/screencast-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/screencast-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/screencast-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-screencast-o-matic-test-user"></a>Képernyőfelvétel-O-felosztásban tesztfelhasználó létrehozása

Ez a szakasz célja Britta Simon nevű képernyőfelvétel-O-értéket a felhasználó létrehozásához. Képernyőfelvétel-O-értéket támogatja a just-in-time kiosztást, amely alapértelmezés szerint van engedélyezve. Nincs ebben a szakaszban az Ön művelet elem. Új felhasználó jön létre, hozzáférési képernyőfelvétel-O-értéket, ha még nem létezik tett kísérlet során.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználó van szüksége, forduljon a [képernyőfelvétel-O-felosztásban, ügyfél-támogatási csoport](mailto:support@screencast-o-matic.com).

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezéshez használandó hozzáférést biztosít a képernyőfelvétel-O-értéket.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése képernyőfelvétel-O-értéket, hajtsa végre a következő lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **képernyőfelvétel-O-felosztásban**.

    ![Az alkalmazások listáját a képernyőfelvétel-O-felosztásban hivatkozás](./media/screencast-tutorial/tutorial_screencast_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Amikor a hozzáférési panelen a képernyőfelvétel-O-felosztásban csempére kattint, meg kell beolvasása automatikusan bejelentkezett a képernyőfelvétel-O-felosztásban alkalmazásba.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/screencast-tutorial/tutorial_general_01.png
[2]: ./media/screencast-tutorial/tutorial_general_02.png
[3]: ./media/screencast-tutorial/tutorial_general_03.png
[4]: ./media/screencast-tutorial/tutorial_general_04.png

[100]: ./media/screencast-tutorial/tutorial_general_100.png

[200]: ./media/screencast-tutorial/tutorial_general_200.png
[201]: ./media/screencast-tutorial/tutorial_general_201.png
[202]: ./media/screencast-tutorial/tutorial_general_202.png
[203]: ./media/screencast-tutorial/tutorial_general_203.png

