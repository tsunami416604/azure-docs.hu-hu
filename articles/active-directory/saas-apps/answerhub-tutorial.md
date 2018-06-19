---
title: 'Oktatóanyag: Azure Active Directoryval integrált AnswerHub |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és AnswerHub között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 818b91d7-01df-4b36-9706-f167c710a73c
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: a0f55b6ee22b617372656584ed5ca505bddaffa7
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35937783"
---
# <a name="tutorial-azure-active-directory-integration-with-answerhub"></a>Oktatóanyag: Azure Active Directoryval integrált AnswerHub

Ebben az oktatóanyagban elsajátíthatja AnswerHub integrálása az Azure Active Directory (Azure AD).

AnswerHub integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a AnswerHub hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett AnswerHub (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs AnswerHub, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy AnswerHub egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből AnswerHub hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-answerhub-from-the-gallery"></a>A gyűjteményből AnswerHub hozzáadása
Az Azure AD integrálása a AnswerHub konfigurálásához kell hozzáadnia AnswerHub a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből AnswerHub hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **AnswerHub**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/answerhub-tutorial/tutorial_answerhub_search.png)

5. Az eredmények panelen válassza ki a **AnswerHub**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/answerhub-tutorial/tutorial_answerhub_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján AnswerHub.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó AnswerHub a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a AnswerHub közötti kapcsolat kapcsolatot kell létrehozni.

AnswerHub, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a AnswerHub tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egy AnswerHub tesztfelhasználó létrehozása](#creating-an-answerhub-test-user)**  - való Britta Simon valami AnswerHub, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az AnswerHub alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés AnswerHub, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **AnswerHub** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/answerhub-tutorial/tutorial_answerhub_samlbase.png)

3. Az a **AnswerHub tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/answerhub-tutorial/tutorial_answerhub_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<company>.answerhub.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<company>.answerhub.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [AnswerHub ügyfél-támogatási csoport](mailto:success@answerhub.com) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/answerhub-tutorial/tutorial_answerhub_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/answerhub-tutorial/tutorial_general_400.png)

6. A a **AnswerHub konfigurációs** kattintson **konfigurálása AnswerHub** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/answerhub-tutorial/tutorial_answerhub_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a AnswerHub vállalati webhely rendszergazdaként.
   
    >[!NOTE]
    >Ha AnswerHub segítségre van szüksége, forduljon a [AnswerHub tartozó támogatási csoport](mailto:success@answerhub.com.).
   
8. Ugrás a **felügyeleti**.

9. Kattintson a **felhasználók és csoportok** fülre.

10. A navigációs ablakban a bal oldalon található a **közösségi beállítások** területen kattintson **SAML-alapú telepítő**.

11. Kattintson a **IDP Config** fülre.

12. Az a **IDP Config** lapra, hajtsa végre a következő lépéseket:

     ![SAML-alapú telepítő](./media/answerhub-tutorial/ic785172.png "SAML-alapú telepítő")  
  
     a. A **IDP bejelentkezési URL-cím** szövegmezőhöz Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.
  
     b. A **IDP kijelentkezési URL-cím** szövegmezőhöz Beillesztés **Sign-Out URL-cím** érték, amely az Azure-portálon másolta.
     
     c. A **IDP azonosító formátuma** szövegmező, adja meg a felhasználói azonosító érték ugyanaz, mint a kijelölt Azure-portálon a **felhasználói attribútumok** szakasz.
  
     d. Kattintson a **a kulcsoknak és tanúsítványoknak**.

13. A kulcsoknak és tanúsítványoknak lapon hajtsa végre a következő lépéseket:
    
     ![A kulcsoknak és tanúsítványoknak](./media/answerhub-tutorial/ic785173.png "a kulcsoknak és tanúsítványoknak")  
 
     a. Nyissa meg a base-64 kódolású tanúsítványt, amely a Jegyzettömbben az Azure portálról letöltött a tartalmának másolása a vágólapra és illessze be azt a **kiállító terjesztési hely nyilvános kulcsát (x 509-formátumú)** szövegmező.
  
     b. Kattintson a **Save** (Mentés) gombra.

14. Az a **IDP Config** lapra, majd **mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/answerhub-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/answerhub-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/answerhub-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/answerhub-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-answerhub-test-user"></a>Egy AnswerHub tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók AnswerHub bejelentkezni, akkor ki kell építenie a AnswerHub.  
AnswerHub, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **AnswerHub** vállalati hely rendszergazdaként.

2. Ugrás a **felügyeleti**.

3. Kattintson a **felhasználók és csoportok** fülre.

4. A navigációs ablakban a bal oldalon található a **felhasználók kezelése** területen kattintson **létrehozása vagy importálása felhasználók**.
   
   ![Felhasználók és csoportok](./media/answerhub-tutorial/ic785175.png "felhasználók és csoportok")

5. Típus a **E-mail cím**, **felhasználónév** és **jelszó** egy érvényes Azure Active Directory-fiók a kapcsolódó szövegmezők kiépíteni, és kattintson a kívánt **mentése**.

>[!NOTE]
>Bármely más AnswerHub felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz AnswerHub által nyújtott API-k.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés AnswerHub Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése AnswerHub, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **AnswerHub**.

    ![Egyszeri bejelentkezés konfigurálása](./media/answerhub-tutorial/tutorial_answerhub_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen AnswerHub csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az AnswerHub alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/answerhub-tutorial/tutorial_general_01.png
[2]: ./media/answerhub-tutorial/tutorial_general_02.png
[3]: ./media/answerhub-tutorial/tutorial_general_03.png
[4]: ./media/answerhub-tutorial/tutorial_general_04.png

[100]: ./media/answerhub-tutorial/tutorial_general_100.png

[200]: ./media/answerhub-tutorial/tutorial_general_200.png
[201]: ./media/answerhub-tutorial/tutorial_general_201.png
[202]: ./media/answerhub-tutorial/tutorial_general_202.png
[203]: ./media/answerhub-tutorial/tutorial_general_203.png

