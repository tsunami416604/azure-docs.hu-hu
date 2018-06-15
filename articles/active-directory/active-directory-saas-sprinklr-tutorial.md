---
title: 'Oktatóanyag: Azure Active Directoryval integrált Sprinklr |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Sprinklr között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b33938a1-25a5-484c-8e75-7dc6de2d534d
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: jeedes
ms.openlocfilehash: e2222e3e71fbddaebf1077149f6d71a43eb8dea0
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352752"
---
# <a name="tutorial-azure-active-directory-integration-with-sprinklr"></a>Oktatóanyag: Azure Active Directoryval integrált Sprinklr

Ebben az oktatóanyagban elsajátíthatja Sprinklr integrálása az Azure Active Directory (Azure AD).

Sprinklr integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Sprinklr hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Sprinklr (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Sprinklr, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Sprinklr egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Sprinklr hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-sprinklr-from-the-gallery"></a>A gyűjteményből Sprinklr hozzáadása
Az Azure AD integrálása a Sprinklr konfigurálásához kell hozzáadnia Sprinklr a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Sprinklr hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Sprinklr**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_search.png)

5. Az eredmények panelen válassza ki a **Sprinklr**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása, és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon." nevű tesztfelhasználó alapján Sprinklr

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Sprinklr a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Sprinklr közötti kapcsolat kapcsolatot kell létrehozni.

Sprinklr, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Sprinklr tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Sprinklr tesztfelhasználó létrehozása](#creating-a-sprinklr-test-user)**  - való Britta Simon valami Sprinklr, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Sprinklr alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Sprinklr, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Sprinklr** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_samlbase.png)

3. Az a **Sprinklr tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.sprinklr.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.sprinklr.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítse az értéket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [Sprinklr ügyfél-támogatási csoport](https://www.sprinklr.com/contact-us/) beolvasni ezeket az értékeket. 
 
4. A a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány (Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sprinklr-tutorial/tutorial_general_400.png)

6. A a **Sprinklr konfigurációs** kattintson **konfigurálása Sprinklr** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

7. Egy másik webes böngészőablakban jelentkezzen be a Sprinklr vállalati webhely rendszergazdaként.

8. Ugrás a **felügyeleti \> beállítások**.
   
    ![Felügyeleti](./media/active-directory-saas-sprinklr-tutorial/ic782907.png "felügyeleti")

9. Ugrás a **kezelése Partner \> egyszeri bejelentkezési** a a bal oldali ablaktáblán.
   
    ![Partner kezelése](./media/active-directory-saas-sprinklr-tutorial/ic782908.png "Partner kezelése")

10. Kattintson a **+ Hozzáadás egyszeri bejelentkezéssel**.
   
    ![Az egyszeri bejelentkezés le](./media/active-directory-saas-sprinklr-tutorial/ic782909.png "az egyszeri bejelentkezés le")

11. Az a **az egyszeri bejelentkezés** lapon, a következő lépésekkel:
   
    ![Az egyszeri bejelentkezés le](./media/active-directory-saas-sprinklr-tutorial/ic782910.png "az egyszeri bejelentkezés le")

    a. Az a **neve** szövegmező, adja meg a konfiguráció nevét (például: *WAADSSOTest*).

    b. Válassza ki **engedélyezett**.

    c. Válassza ki **új SSO-tanúsítvány használatára**.
             
    e. Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben, a tartalmának másolása a vágólapra és illessze be azt a **szolgáltató Identitástanúsítvány** szövegmező.

    f. Beillesztés a **SAML Entitásazonosító** érték, amely az Azure portálról másolta a **entitásazonosító** szövegmező.

    g. Beillesztés a **SAML-alapú egyszeri bejelentkezési URL-címe** érték, amely az Azure portálról másolta a **Identity Provider bejelentkezési URL-cím** szövegmező.

    h. Beillesztés a **Sign-Out URL-cím** érték, amely az Azure portálról másolta a **Identity Provider kijelentkezési URL-cím** szövegmező.
     
    i. Mint **SAML felhasználói azonosító típusa**, jelölje be **helyességi feltételt tartalmaz felhasználói "s sprinklr.com felhasználónév**.

    j. Mint **SAML felhasználói azonosító hely**, jelölje be **felhasználói Azonosítóját a tulajdonos utasítás névazonosítója elemében van**.

    k. Kattintson a **Save** (Mentés) gombra.
       
    ![SAML](./media/active-directory-saas-sprinklr-tutorial/ic782911.png "SAML")

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sprinklr-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-sprinklr-test-user"></a>Sprinklr tesztfelhasználó létrehozása

1. Jelentkezzen be rendszergazdaként a Sprinklr vállalati webhely.

2. Ugrás a **felügyeleti \> beállítások**.
   
    ![Felügyeleti](./media/active-directory-saas-sprinklr-tutorial/ic782907.png "felügyeleti")

3. Ugrás a **kezelése ügyfél \> felhasználók** a bal oldali ablaktáblán.
   
    ![Beállítások](./media/active-directory-saas-sprinklr-tutorial/ic782914.png "beállítások")

4. Kattintson a **felhasználó hozzáadása**.
   
    ![Beállítások](./media/active-directory-saas-sprinklr-tutorial/ic782915.png "beállítások")

5. Az a **Szerkesztés felhasználói** párbeszédpanelen hajtsa végre a következő lépéseket:
   
    ![Felhasználó szerkesztése](./media/active-directory-saas-sprinklr-tutorial/ic782916.png "felhasználó szerkesztése") 

    a. Az a **E-mail**, **Utónév** és **Vezetéknév** szövegmezőből, írja be a kívánt kiépítéséhez az Azure AD felhasználói fiók adatait.

    b. Válassza ki **jelszó tiltva**.

    c. Válassza ki **nyelvi**.

    d. Válassza ki **felhasználótípust**.

    e. Kattintson a **frissítés**.
   
     >[!IMPORTANT]
     >**Jelszó tiltva** engedélyezése a felhasználónak be kell jelentkeznie az identitásszolgáltató keresztül kell lennie jelölve. 
     
6. Ugrás a **szerepkör**, majd végezze el az alábbi lépéseket:
   
    ![Szerepkörök partneri](./media/active-directory-saas-sprinklr-tutorial/ic782917.png "partneri szerepkörök")

    a. Az a **globális** listáról válassza ki **összes\_engedélyek**.  

    b. Kattintson a **frissítés**.

>[!NOTE]
>Bármely más Sprinklr felhasználói fiók létrehozása eszközök vagy Sprinklr kiépíteni az Azure AD-felhasználói fiókok által nyújtott API-k. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Sprinklr Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Sprinklr, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Sprinklr**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sprinklr-tutorial/tutorial_sprinklr_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Sprinklr csempére kattint, szerezheti be automatikusan bejelentkezett az Sprinklr alkalmazás a hozzáférési Panel további információt lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sprinklr-tutorial/tutorial_general_203.png

