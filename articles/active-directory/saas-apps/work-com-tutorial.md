---
title: 'Oktatóanyag: Azure Active Directoryval integrált Work.com |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Work.com között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 98e6739e-eb24-46bd-9dd3-20b489839076
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 143cf41f597050cb27b97ff2584860a6c22aae74
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36213535"
---
# <a name="tutorial-azure-active-directory-integration-with-workcom"></a>Oktatóanyag: Azure Active Directoryval integrált Work.com

Ebben az oktatóanyagban elsajátíthatja Work.com integrálása az Azure Active Directory (Azure AD).

Work.com integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Work.com hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Work.com (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Work.com, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Work.com egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Adja hozzá a Work.com a gyűjteményből
2. Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

## <a name="add-workcom-from-the-gallery"></a>Adja hozzá a Work.com a gyűjteményből
Az Azure AD integrálása a Work.com konfigurálásához kell hozzáadnia Work.com a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Work.com hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Work.com**, jelölje be **Work.com** eredmények panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Adja hozzá a gyűjteményből](./media/work-com-tutorial/tutorial_work-com_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Work.com.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Work.com a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Work.com közötti kapcsolat kapcsolatot kell létrehozni.

Work.com, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Work.com tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Work.com tesztfelhasználó létrehozása](#create-a-workcom-test-user)**  - való Britta Simon valami Work.com, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Work.com alkalmazásban.

>[!NOTE]
>Egyszeri bejelentkezés beállításához szüksége egy Work.com egyéni tartománynév beállítása még. Adja meg legalább egy tartomány nevét, a tartománynév tesztelése és telepíteni kell a teljes szervezet kell.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Work.com, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Work.com** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![SAML-alapú bejelentkezés](./media/work-com-tutorial/tutorial_work-com_samlbase.png)

3. Az a **Work.com tartomány és az URL-címek** területen tegye a következőket:

    ![Work.com tartomány és az URL-címek szakasz](./media/work-com-tutorial/tutorial_work-com_url.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `http://<companyname>.my.salesforce.com`

    > [!NOTE] 
    > Ez az érték nincs valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címet. Ügyfél [Work.com ügyfél-támogatási csoport](https://help.salesforce.com/articleView?id=000159855&type=3) lekérni ezt az értéket. 

4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![SAML-aláíró tanúsítványa szakasz](./media/work-com-tutorial/tutorial_work-com_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Mentés gomb](./media/work-com-tutorial/tutorial_general_400.png)

6. A a **Work.com konfigurációs** kattintson **konfigurálása Work.com** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Work.com konfigurációs szakasz](./media/work-com-tutorial/tutorial_work-com_configure.png) 
7. Jelentkezzen be rendszergazdaként a Work.com bérlő.

8. Ugrás a **telepítő**.
   
    ![A telepítő](./media/work-com-tutorial/ic794108.png "beállítása")

9. A bal oldali navigációs panelen a a **Administer** kattintson **tartományok** bontsa ki a kapcsolódó szakaszt, és kattintson **saját tartomány** megnyitásához a **saját tartomány** lap. 
   
    ![Saját tartomány](./media/work-com-tutorial/ic767825.png "saját tartomány")

10. Győződjön meg arról, hogy a tartomány megfelelően van beállítva, győződjön meg arról, hogy "**4. lépés telepíti a felhasználók számára**", és tekintse át a "**saját tartománybeállítások**".
   
    ![A tartományi felhasználó számára központilag telepített](./media/work-com-tutorial/ic784377.png "tartományi felhasználó számára központilag telepített")

11. Jelentkezzen be a Work.com bérlő.

12. Ugrás a **telepítő**.
    
    ![A telepítő](./media/work-com-tutorial/ic794108.png "beállítása")

13. Bontsa ki a **biztonsági vezérlők** menüben, majd kattintson **egyszeri bejelentkezési beállítások**.
    
    ![Az egyszeri bejelentkezés beállítások](./media/work-com-tutorial/ic794113.png "az egyszeri bejelentkezés beállításai")

14. Az a **egyszeri bejelentkezési beállítások** párbeszédpanel lapon, a következő lépésekkel:
    
    ![A SAML engedélyezett](./media/work-com-tutorial/ic781026.png "SAML engedélyezett")
    
    a. Válassza ki **SAML engedélyezett**.
    
    b. Kattintson az **Új** lehetőségre.

15. Az a **SAML-alapú egyszeri bejelentkezés beállítások** területen tegye a következőket:
    
    ![SAML-alapú egyszeri bejelentkezés beállítása](./media/work-com-tutorial/ic794114.png "SAML-alapú egyszeri bejelentkezés beállítása")
    
    a. Az a **neve** szövegmező, adja meg a konfiguráció nevét.  
       
    > [!NOTE]
    > Értéket biztosító **neve** automatikusan feltölti a **API-név** szövegmező.
    
    b. A **kibocsátó** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** ami Azure-portálon másolta.
    
    c. Az Azure-portálról letöltött tanúsítvány feltöltése, kattintson a **Tallózás**.
    
    d. Az a **entitásazonosító** szövegmezőhöz típus `https://salesforce-work.com`.
    
    e. Mint **SAML identitástípus**, jelölje be **helyességi feltételt tartalmaz az összevonási azonosító felhasználó**.
    
    f. Mint **SAML-alapú identitás hely**, jelölje be **identitás a tulajdonos utasítás NameIdentfier elemében van**.
    
    g. A **Identity Provider bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.

    h. A **Identity Provider kijelentkezési URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím** ami Azure-portálon másolta.
    
    i. Mint **szolgáltató által kezdeményezett kérelem Szolgáltatáskötés**, jelölje be **HTTP Post**.
    
    j. Kattintson a **Save** (Mentés) gombra.

16. Kattintson a bal oldali navigációs panelen a Work.com klasszikus portál **tartományok** bontsa ki a kapcsolódó szakaszt, és kattintson **saját tartomány** megnyitásához a **saját tartomány** lap. 
    
    ![Saját tartomány](./media/work-com-tutorial/ic794115.png "saját tartomány")

17. Az a **saját tartomány** lap a **bejelentkezési oldal vállalati arculata** kattintson **szerkesztése**.
    
    ![Bejelentkezési oldal vállalati arculatán alkalmazott](./media/work-com-tutorial/ic767826.png "bejelentkezési oldal vállalati arculatán alkalmazott")

14. Az a **bejelentkezési oldal vállalati arculata** lap a **hitelesítési szolgáltatás** részben, a neve a **SAML SSO beállítások** jelenik meg. Válassza ki azt, és kattintson **mentése**.
    
    ![Bejelentkezési oldal vállalati arculatán alkalmazott](./media/work-com-tutorial/ic784366.png "bejelentkezési oldal vállalati arculatán alkalmazott")

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/work-com-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Felhasználók és csoportok -> minden felhasználó](./media/work-com-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Hozzáadás](./media/work-com-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![A felhasználó párbeszédpanel lap](./media/work-com-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-workcom-test-user"></a>Work.com tesztfelhasználó létrehozása
Az Azure Active Directory-felhasználók jelentkezhetnek be kell hogy ki kell építenie Work.com. Work.com, ha egy kézi tevékenység.

### <a name="to-configure-user-provisioning-perform-the-following-steps"></a>Adja meg a felhasználók átadása, hajtsa végre az alábbi lépéseket:
1. Jelentkezzen be rendszergazdaként a Work.com vállalati webhely.

2. Ugrás a **telepítő**.
   
    ![A telepítő](./media/work-com-tutorial/IC794108.png "beállítása")
3. Ugrás a **felhasználók kezelése \> felhasználók**.
   
    ![Felhasználók kezelése](./media/work-com-tutorial/IC784369.png "felhasználók kezelése")

4. Kattintson a **új felhasználó**.
   
    ![Minden felhasználó](./media/work-com-tutorial/IC794117.png "minden felhasználó")

5. A felhasználó szerkesztése a szakaszban a következő lépésekkel, az attribútumok egy érvényes Azure AD-fiókot szeretné azokat a kapcsolódó szövegmezők rendelkezni:
   
    ![Felhasználó szerkesztése](./media/work-com-tutorial/ic794118.png "felhasználó szerkesztése")
   
    a. Az a **Utónév** szövegmezőhöz típusa a **Utónév** felhasználó **Britta**.
    
    b. Az a **Vezetéknév** szövegmezőhöz típusa a **Vezetéknév** felhasználó **Simon**.
    
    c. Az a **Alias** szövegmezőhöz típusa a **neve** felhasználó **BrittaS**.
    
    d. Az a **E-mail** szövegmezőhöz típusa a **e-mail cím** felhasználó **Brittasimon@contoso.com**.
    
    e. Az a **felhasználónév** szövegmező, írja be például a felhasználó a felhasználónév **Brittasimon@contoso.com**.
    
    f. Az a **becenév** szövegmező, adjon meg egy **becenév** felhasználó **Simon**.
    
    g. Válassza ki **szerepkör**, **felhasználói licenc**, és **profil**.
    
    h. Kattintson a **Save** (Mentés) gombra.  
      
    > [!NOTE]
    > Az Azure AD fióktulajdonos kap egy e-mailt hivatkozással erősítse meg a fiókot, mielőtt aktívvá válik.
    > 
    > 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Work.com Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Work.com, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Work.com**.

    ![Alkalmazás listában Work.com](./media/work-com-tutorial/tutorial_work-com_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Work.com csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Work.com alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)


<!--Image references-->

[1]: ./media/work-com-tutorial/tutorial_general_01.png
[2]: ./media/work-com-tutorial/tutorial_general_02.png
[3]: ./media/work-com-tutorial/tutorial_general_03.png
[4]: ./media/work-com-tutorial/tutorial_general_04.png

[100]: ./media/work-com-tutorial/tutorial_general_100.png

[200]: ./media/work-com-tutorial/tutorial_general_200.png
[201]: ./media/work-com-tutorial/tutorial_general_201.png
[202]: ./media/work-com-tutorial/tutorial_general_202.png
[203]: ./media/work-com-tutorial/tutorial_general_203.png

