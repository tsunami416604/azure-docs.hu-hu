---
title: 'Oktatóanyag: Azure Active Directoryval integrált TimeOffManager |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és TimeOffManager között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 3685912f-d5aa-4730-ab58-35a088fc1cc3
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: 4e92d1ba948626c05e7b66d2c0d469ac6c6571a2
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36228186"
---
# <a name="tutorial-azure-active-directory-integration-with-timeoffmanager"></a>Oktatóanyag: Azure Active Directoryval integrált TimeOffManager

Ebben az oktatóanyagban elsajátíthatja TimeOffManager integrálása az Azure Active Directory (Azure AD).

TimeOffManager integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a TimeOffManager hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett TimeOffManager (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs TimeOffManager, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy TimeOffManager egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Adja hozzá a TimeOffManager a gyűjteményből
2. Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

## <a name="add-timeoffmanager-from-the-gallery"></a>Adja hozzá a TimeOffManager a gyűjteményből
Az Azure AD integrálása a TimeOffManager konfigurálásához kell hozzáadnia TimeOffManager a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből TimeOffManager hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **TimeOffManager**, jelölje be **TimeOffManager** eredmény panelen, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Adja hozzá a gyűjteményből](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addfromgallery.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján TimeOffManager.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó TimeOffManager a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a TimeOffManager közötti kapcsolat kapcsolatot kell létrehozni.

TimeOffManager, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a TimeOffManager tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[TimeOffManager tesztfelhasználó létrehozása](#create-a-timeoffmanager-test-user)**  - való Britta Simon valami TimeOffManager, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az TimeOffManager alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés TimeOffManager, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **TimeOffManager** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![SAML-alapú bejelentkezés](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_samlbase.png)

3. Az a **TimeOffManager tartomány és az URL-címek** területen tegye a következőket:

     ![TimeOffManager tartomány és az URL-címek szakasz](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_url.png)

    Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://www.timeoffmanager.com/cpanel/sso/consume.aspx?company_id=<companyid>`

    > [!NOTE] 
    > Ez az érték nincs valós. Frissítse ezt az értéket a tényleges válasz URL-címet. Ezt az értéket kaphat **egyszeri bejelentkezési beállítások lapon** amely esetén, tekintse meg az oktatóanyag használatával, vagy forduljon a [TimeOffManager támogatási csoport](http://www.timeoffmanager.com/contact-us.aspx).
 
4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![SAML-aláíró tanúsítványa szakasz](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_certificate.png) 

5. Ez a szakasz célja felvázoló engedélyezése a felhasználók hitelesítéséhez TimeOffManger fiókkal az Azure AD összevonási alapján a SAML protokoll használatával.
    
    A TimeOffManger alkalmazás a SAML helyességi feltételek egy meghatározott formátumban, amelyek megkövetelik olyan egyéni attribútum-leképezésekhez hozzáadása a SAML-jogkivonat attribútumok konfigurációs vár. Az alábbi képernyőfelvételen látható egy példa a.

    ![SAML-jogkivonat attribútumok](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_attrb.png "saml-jogkivonat attribútumok")
    
    | Attribútum neve | Attribútum értéke |
    | --- | --- |
    | Utónév |User.givenName |
    | Vezetéknév |User.surname |
    | E-mail |User.mail |
    
    a.  Kattintson a fenti adatokat minden egyes sorhoz kapcsolódóan **hozzáadása a felhasználói attribútum**.
    
    ![SAML-jogkivonat attribútumok](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb.png "saml-jogkivonat attribútumok")
    
    ![SAML-jogkivonat attribútumok](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_addattrb1.png "saml-jogkivonat attribútumok")
    
    b.  Az a **attribútumnév** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.
    
    c.  Az a **attribútumérték** szövegmező, válassza ki az adott sorhoz feltüntetett attribútumot értéket.
    
    d.  Kattintson az **OK** gombra.
    
6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/timeoffmanager-tutorial/tutorial_general_400.png)

7. A a **TimeOffManager konfigurációs** kattintson **konfigurálása TimeOffManager** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![TimeOffManager konfigurációs szakasz](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_configure.png) 

8. Egy másik webes böngészőablakban jelentkezzen be a TimeOffManager vállalati webhely rendszergazdaként.

9. Nyissa meg a **fiók \> beállítások fiók \> az egyszeri bejelentkezés beállítások**.
   
   ![Az egyszeri bejelentkezés beállítások](./media/timeoffmanager-tutorial/ic795917.png "az egyszeri bejelentkezés beállításai")
7. Az a **egyszeri bejelentkezési beállítások** területen tegye a következőket:
   
   ![Az egyszeri bejelentkezés beállítások](./media/timeoffmanager-tutorial/ic795918.png "az egyszeri bejelentkezés beállításai")
   
   a. Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben, annak tartalmának másolása a vágólapra és illessze be a teljes tanúsítványt **X.509 tanúsítvány** szövegmező.
   
   b. A **Idp kibocsátó** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** ami Azure-portálon másolta.
   
   c. A **IdP végponti URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.
   
   d. Mint **kényszerítése SAML**, jelölje be **nem**.
   
   e. Mint **automatikus létrehozása felhasználók**, jelölje be **Igen**.
   
   f. A **kijelentkezési URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím** ami Azure-portálon másolta.
   
   g. Kattintson a **módosítások mentése**.

11. A **egyszeri bejelentkezési beállítások** lapján értékének másolása **helyességi feltétel ügyfél szolgáltatás URL-címe** és illessze be a **válasz URL-CÍMEN** szövegmező alatt **TimeOffManager Tartomány- és URL-címek** szakaszban az Azure portálon. 

      ![Az egyszeri bejelentkezés beállítások](./media/timeoffmanager-tutorial/ic795915.png "az egyszeri bejelentkezés beállításai")

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/timeoffmanager-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Felhasználók és csoportok--> minden felhasználó](./media/timeoffmanager-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Hozzáadás gomb](./media/timeoffmanager-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![A felhasználó párbeszédpanel lap](./media/timeoffmanager-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-timeoffmanager-test-user"></a>TimeOffManager tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók TimeOffManager bejelentkezni, akkor ki kell építenie TimeOffManager.  

TimeOffManager csak az idő a felhasználók átadása támogatja. Nincs művelet elem meg.  

A felhasználók automatikusan hozzáadja az egyszeri bejelentkezés használatával az első bejelentkezés során.

>[!NOTE]
>Bármely más TimeOffManager felhasználói fiók létrehozása eszközök vagy TimeOffManager kiépíteni az Azure AD-felhasználói fiókok által nyújtott API-k.
> 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés TimeOffManager Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése TimeOffManager, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **TimeOffManager**.

    ![TimeOffManager alkalmazáslistában](./media/timeoffmanager-tutorial/tutorial_timeoffmanager_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen TimeOffManager csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az TimeOffManager alkalmazására. A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/timeoffmanager-tutorial/tutorial_general_01.png
[2]: ./media/timeoffmanager-tutorial/tutorial_general_02.png
[3]: ./media/timeoffmanager-tutorial/tutorial_general_03.png
[4]: ./media/timeoffmanager-tutorial/tutorial_general_04.png

[100]: ./media/timeoffmanager-tutorial/tutorial_general_100.png

[200]: ./media/timeoffmanager-tutorial/tutorial_general_200.png
[201]: ./media/timeoffmanager-tutorial/tutorial_general_201.png
[202]: ./media/timeoffmanager-tutorial/tutorial_general_202.png
[203]: ./media/timeoffmanager-tutorial/tutorial_general_203.png

