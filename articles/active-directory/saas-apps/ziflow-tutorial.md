---
title: 'Oktatóanyag: Azure Active Directoryval integrált Ziflow |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Ziflow között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 84e60fa4-36fb-49c4-a642-95538c78f926
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/09/2018
ms.author: jeedes
ms.openlocfilehash: 329168f2c5212213ea50476c9865ec539d40026d
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35927751"
---
# <a name="tutorial-azure-active-directory-integration-with-ziflow"></a>Oktatóanyag: Azure Active Directoryval integrált Ziflow

Ebben az oktatóanyagban elsajátíthatja Ziflow integrálása az Azure Active Directory (Azure AD).

Ziflow integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér Ziflow szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Ziflow (egyszeri bejelentkezés) számára a saját Azure AD-fiókok.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Ziflow, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Ziflow egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Ziflow hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-ziflow-from-the-gallery"></a>A gyűjteményből Ziflow hozzáadása
Az Azure AD integrálása a Ziflow konfigurálásához kell hozzáadnia Ziflow a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Ziflow hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **Ziflow**, jelölje be **Ziflow** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában Ziflow](./media/ziflow-tutorial/tutorial_ziflow_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Ziflow.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Ziflow a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Ziflow közötti kapcsolat kapcsolatot kell létrehozni.

Az Azure AD egyszeri bejelentkezést a Ziflow tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Ziflow tesztfelhasználó létrehozása](#create-a-ziflow-test-user)**  - való Britta Simon valami Ziflow, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Ziflow alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Ziflow, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Ziflow** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/ziflow-tutorial/tutorial_ziflow_samlbase.png)

3. Az a **Ziflow tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk Ziflow tartomány és az URL-címek](./media/ziflow-tutorial/tutorial_ziflow_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.ziflow.io/#/login-sso/<Unique ID>`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `urn:auth0:ziflow-production:<Unique ID>`

    > [!NOTE] 
    > Az előző értékei nem valódi. Az egyedi azonosító értéket a azonosítóját és a bejelentkezési URL-címen frissíti a tényleges érték, amelynek az ismertetése, az oktatóanyag későbbi részében. Ügyfél [Ziflow támogatási csoport](mailto:support@ziflow.com) a bejelentkezési URL-cím altartomány értékéhez.
    
4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/ziflow-tutorial/tutorial_ziflow_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/ziflow-tutorial/tutorial_general_400.png)

6. A a **Ziflow konfigurációs** kattintson **konfigurálása Ziflow** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out és SAML-alapú egyszeri bejelentkezés szolgáltatás URL-címe** a a **rövid összefoglaló szakasz.**

    ![Ziflow konfiguráció](./media/ziflow-tutorial/tutorial_ziflow_configure.png) 

7. Egy másik webes böngészőablakban, jelentkezzen be a biztonsági-rendszergazdájaként Ziflow.


8. Kattintson az Avatarjára, a jobb felső sarokban, és kattintson **fiók kezelése**.

    ![Ziflow konfigurációs kezelése](./media/ziflow-tutorial/tutorial_ziflow_manage.png)

9. Kattintson a bal felső, **egyszeri bejelentkezés**.

    ![Ziflow konfigurációs bejelentkezési](./media/ziflow-tutorial/tutorial_ziflow_signon.png)

10. Az a **egyszeri bejelentkezés** lapon, a következő lépésekkel:

    ![Ziflow konfigurációs egyetlen](./media/ziflow-tutorial/tutorial_ziflow_page.png)

    a. Válassza ki **típus** , **SAML2.0**.

    b.In a **bejelentkezési az URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure portálról másolta.

    c. Az Azure-portálról letöltött kódolt Base64-tanúsítvány feltöltése a **aláíró tanúsítvány X509**.

    d. Az a **bejelentkezési kijelentkezési URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím**, amely az Azure portálról másolta.

    e. Az a **konfigurációs beállításait a azonosítója** szakaszt, másolja a kijelölt egyedi azonosítóérték és fűzi azokat a azonosítója és a bejelentkezési URL-címet a **Ziflow tartomány és az URL-címek szakasz** a Azure-portálon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/ziflow-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/ziflow-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/ziflow-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/ziflow-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
  
### <a name="create-a-ziflow-test-user"></a>Ziflow tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Ziflow bejelentkezni, akkor ki kell építenie a Ziflow. A Ziflow egy kézi tevékenység.

Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a biztonsági-rendszergazdájaként Ziflow.

2. Navigáljon a **személyek** felső.

    ![Ziflow konfigurációs személyek](./media/ziflow-tutorial/tutorial_ziflow_people.png)

3. Kattintson a **Hozzáadás** majd **felhasználó hozzáadása**.

    ![Ziflow konfigurációs felvenni a felhasználót](./media/ziflow-tutorial/tutorial_ziflow_add.png)

4. Az a **hozzáadni egy felhasználót** előugró ablak, hajtsa végre a következő lépéseket:

    ![Ziflow konfigurációs felvenni a felhasználót](./media/ziflow-tutorial/tutorial_ziflow_adduser.png)

    a. A **E-mail** szöveg mezőbe írja be például a felhasználó e-mail brittasimon@contoso.com.

    b. A **Keresztnév** szöveg mezőbe írja be például Britta felhasználó utónevét.

    c. A **Vezetéknév** szöveg mezőbe írja be például Simon felhasználó vezetékneve.

    d. Válassza ki a Ziflow szerepkör.

    e. Kattintson a **hozzáadása 1 felhasználó**.

    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt, és a következő hivatkozás a fiók megerősítéséhez, mielőtt aktívvá válik.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Ziflow Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése Ziflow, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Ziflow**.

    ![Az alkalmazások listáját a Ziflow hivatkozás](./media/ziflow-tutorial/tutorial_ziflow_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Ziflow csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Ziflow alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/ziflow-tutorial/tutorial_general_01.png
[2]: ./media/ziflow-tutorial/tutorial_general_02.png
[3]: ./media/ziflow-tutorial/tutorial_general_03.png
[4]: ./media/ziflow-tutorial/tutorial_general_04.png

[100]: ./media/ziflow-tutorial/tutorial_general_100.png

[200]: ./media/ziflow-tutorial/tutorial_general_200.png
[201]: ./media/ziflow-tutorial/tutorial_general_201.png
[202]: ./media/ziflow-tutorial/tutorial_general_202.png
[203]: ./media/ziflow-tutorial/tutorial_general_203.png

