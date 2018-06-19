---
title: 'Oktatóanyag: Azure Active Directoryval integrált PagerDuty |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és PagerDuty között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 0410456a-76f7-42a7-9bb5-f767de75a0e0
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jeedes
ms.openlocfilehash: 87ac0b0ceb6790b91017b1a74825fc192d3dcf52
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35926152"
---
# <a name="tutorial-azure-active-directory-integration-with-pagerduty"></a>Oktatóanyag: Azure Active Directoryval integrált PagerDuty

Ebben az oktatóanyagban elsajátíthatja PagerDuty integrálása az Azure Active Directory (Azure AD).

PagerDuty integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a PagerDuty hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett PagerDuty (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs PagerDuty, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy PagerDuty egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből PagerDuty hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-pagerduty-from-the-gallery"></a>A gyűjteményből PagerDuty hozzáadása
Az Azure AD integrálása a PagerDuty konfigurálásához kell hozzáadnia PagerDuty a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből PagerDuty hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **PagerDuty**, jelölje be **PagerDuty** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/pagerduty-tutorial/tutorial_pagerduty_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján PagerDuty.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó PagerDuty a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a PagerDuty közötti kapcsolat kapcsolatot kell létrehozni.

PagerDuty, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a PagerDuty tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[PagerDuty tesztfelhasználó létrehozása](#create-a-pagerduty-test-user)**  - való Britta Simon valami PagerDuty, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az PagerDuty alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés PagerDuty, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **PagerDuty** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/pagerduty-tutorial/tutorial_pagerduty_samlbase.png)

3. Az a **PagerDuty tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk PagerDuty tartomány és az URL-címek](./media/pagerduty-tutorial/tutorial_pagerduty_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<tenant-name>.pagerduty.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<tenant-name>.pagerduty.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [PagerDuty ügyfél-támogatási csoport](https://www.pagerduty.com/support/) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/pagerduty-tutorial/tutorial_pagerduty_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/pagerduty-tutorial/tutorial_general_400.png)

6. A a **PagerDuty konfigurációs** kattintson **konfigurálása PagerDuty** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out és SAML-alapú egyszeri bejelentkezés szolgáltatás URL-címe** a a **rövid összefoglaló szakasz.**

    ![PagerDuty konfiguráció](./media/pagerduty-tutorial/tutorial_pagerduty_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a Pagerduty vállalati webhely rendszergazdaként.

8. Kattintson a felső menüben **Fiókbeállítások**.
   
    ![Fiókbeállítások](./media/pagerduty-tutorial/ic778535.png "Fiókbeállítások")

9. Kattintson a **egyszeri bejelentkezés**.
   
    ![Egyszeri bejelentkezés](./media/pagerduty-tutorial/ic778536.png "egyszeri bejelentkezés")

10. Az a **engedélyezése egyszeri bejelentkezés (SSO)** lapon, a következő lépésekkel:
   
    ![Egyszeri bejelentkezés engedélyezése](./media/pagerduty-tutorial/ic778537.png "egyszeri bejelentkezés engedélyezése")
   
    a. Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben az Azure portálról letöltött, annak tartalmának másolása a vágólapra és illessze be azt a **X.509 tanúsítvány** szövegmező
  
    b. Az a **bejelentkezési URL-címe** szövegmezőhöz Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.
  
    c. Az a **kijelentkezési URL-cím** szövegmező, Beillesztés **Sign-Out URL-cím** ami Azure-portálon másolta.
 
    d. Válassza ki **kapcsolja be egyszeri bejelentkezés**.
 
    e. Kattintson a **módosítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](./media/pagerduty-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/pagerduty-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![A Hozzáadás gombra.](./media/pagerduty-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![A felhasználó párbeszédpanel](./media/pagerduty-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-pagerduty-test-user"></a>PagerDuty tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók PagerDuty bejelentkezni, akkor ki kell építenie a PagerDuty.  
PagerDuty, ha egy kézi tevékenység.

>[!NOTE]
>Bármely más Pagerduty felhasználói fiók létrehozása eszközök vagy API-k által biztosított Pagerduty kiépítését Azure Active Directory felhasználói fiókokat.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Pagerduty** bérlő.

2. Kattintson a felső menüben **felhasználók**.

3. Kattintson a **felhasználók hozzáadása az**.
   
    ![Felhasználók hozzáadása az](./media/pagerduty-tutorial/ic778539.png "felhasználók hozzáadása")

4.  Az a **hívhat meg a csoport** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![A csoport meghívása](./media/pagerduty-tutorial/ic778540.png "hívhat meg a csoport")

    a. Típus a **első és utolsó neve** például a felhasználó **Britta Simon**. 
   
    b. Adja meg **E-mail** felhasználó címét, például **brittasimon@contoso.com**.
   
    c. Kattintson a **Hozzáadás**, és kattintson a **küldése felkéri**.
   
    >[!NOTE]
    >Minden hozzáadott felhasználó kap egy összehíváshoz PagerDuty-fiók létrehozása.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés PagerDuty Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon hozzárendelése PagerDuty, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **PagerDuty**.

    ![Az alkalmazások listáját a PagerDuty hivatkozás](./media/pagerduty-tutorial/tutorial_pagerduty_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Kattintva a hozzáférési Panelyou PagerDuty mozaik kell beolvasni automatikusan bejelentkezett az PagerDuty alkalmazására.

A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/pagerduty-tutorial/tutorial_general_01.png
[2]: ./media/pagerduty-tutorial/tutorial_general_02.png
[3]: ./media/pagerduty-tutorial/tutorial_general_03.png
[4]: ./media/pagerduty-tutorial/tutorial_general_04.png

[100]: ./media/pagerduty-tutorial/tutorial_general_100.png

[200]: ./media/pagerduty-tutorial/tutorial_general_200.png
[201]: ./media/pagerduty-tutorial/tutorial_general_201.png
[202]: ./media/pagerduty-tutorial/tutorial_general_202.png
[203]: ./media/pagerduty-tutorial/tutorial_general_203.png

