---
title: "Oktatóanyag: Azure Active Directoryval integrált FreshDesk |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és FreshDesk között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.assetid: c2a3e5aa-7b5a-4fe4-9285-45dbe6e8efcc
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/20/2017
ms.author: jeedes
ms.reviewer: jeedes
ms.openlocfilehash: f4b47e345a40b64f69ad8a4618564662b4a6c879
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-freshdesk"></a>Oktatóanyag: Azure Active Directoryval integrált FreshDesk

Ebben az oktatóanyagban elsajátíthatja FreshDesk integrálása az Azure Active Directory (Azure AD).

FreshDesk integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a FreshDesk hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett FreshDesk (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure felügyeleti portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs FreshDesk, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy FreshDesk egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, ha ez nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből FreshDesk hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-freshdesk-from-the-gallery"></a>A gyűjteményből FreshDesk hozzáadása
Az Azure AD integrálása a FreshDesk konfigurálásához kell hozzáadnia FreshDesk a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből FreshDesk hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure felügyeleti portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Kattintson a **Hozzáadás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **FreshDesk**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_search.png)

5. Az eredmények panelen válassza ki a **FreshDesk**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján FreshDesk.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó FreshDesk a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a FreshDesk közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** FreshDesk a.

Az Azure AD egyszeri bejelentkezést a FreshDesk tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[FreshDesk tesztfelhasználó létrehozása](#creating-a-freshdesk-test-user)**  - való Britta Simon valami FreshDesk, amely csatolva van rá, hogy az Azure AD ábrázolása.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure felügyeleti portálon, és konfigurálása egyszeri bejelentkezéshez az FreshDesk alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés FreshDesk, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálján a a **FreshDesk** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. A a **egyszeri bejelentkezés** párbeszédpanel, mint **mód** kiválasztása **SAML-alapú bejelentkezés** a engedélyezése az egyszeri bejelentkezéshez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_samlbase.png)

3. A a **FreshDesk tartomány és az URL-címek** területen írja be a **bejelentkezési URL-cím** mint: `https://<tenant-name>.freshdesk.com` vagy bármely más érték Freshdesk javasolt.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_url.png)

    > [!NOTE] 
    > Ne feledje, hogy ez a nem a tényleges érték. Frissítse az értéket a tényleges bejelentkezési URL-címmel rendelkezik. Ügyfél [FreshDesk ügyfél-támogatási csoport](https://freshdesk.com/helpdesk-software?utm_source=Google-AdWords&utm_medium=Search-IND-Brand&utm_campaign=Search-IND-Brand&utm_term=freshdesk&device=c&gclid=COSH2_LH7NICFVUDvAodBPgBZg) lekérni ezt az értéket.  

4. Az a **SAML-aláíró tanúsítványa** kattintson **tanúsítvány** , és mentse a tanúsítványt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-freshdesk-tutorial/tutorial_general_400.png)

6. A a **FreshDesk konfigurációs** kattintson **konfigurálása FreshDesk** konfigurálása bejelentkezés ablak megnyitásához. Másolja a SAML-alapú egyszeri bejelentkezési URL-címe és Sign-Out URL-CÍMÉT a **rövid összefoglaló** szakasz.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_configure.png)

7. Egy másik webes böngészőablakban jelentkezzen be a Freshdesk vállalati webhely rendszergazdaként.

8. Kattintson a felső menüben **Admin**.
   
   ![Felügyeleti](./media/active-directory-saas-freshdesk-tutorial/IC776768.png "rendszergazda")

9. Az a **általános beállítások** lapra, majd **biztonsági**.
   
   ![Biztonsági](./media/active-directory-saas-freshdesk-tutorial/IC776769.png "biztonsági")

10. Az a **biztonsági** területen tegye a következőket:
   
    ![Egyszeri bejelentkezés](./media/active-directory-saas-freshdesk-tutorial/IC776770.png "egyszeri bejelentkezés")
   
    a. A **egyszeri bejelentkezés (SSO)**, jelölje be **a**.

    b. Válassza ki **SAML SSO**.

    c. Típus a **SAML-alapú egyszeri bejelentkezési URL-címe** másolta az Azure-portálon a **SAML bejelentkezési URL-cím** szövegmező.

    d. Típus a **Sign-Out URL-cím** másolta az Azure-portálon a **kijelentkezési URL-cím** szövegmező.

    e. Másolás a **ujjlenyomat** Azure portálról letöltött tanúsítvány értéket, és illessze be azt a **biztonsági tanúsítvány-ujjlenyomat** szövegmező.  
 
    >[!TIP]
    >További részletekért lásd: [hogyan lehet lekérni egy tanúsítvány-ujjlenyomat értékének](http://youtu.be/YKQF266SAxI). 
    
    f. Kattintson a **Save** (Mentés) gombra.


### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure felügyeleti portálján Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure Management portal**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_01.png) 

2. Ugrás a **felhasználók és csoportok** kattintson **minden felhasználó** azon felhasználók listájának megjelenítéséhez.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_02.png) 

3. Kattintson a párbeszédpanel tetején **Hozzáadás** megnyitásához a **felhasználói** párbeszédpanel.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-freshdesk-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-freshdesk-test-user"></a>FreshDesk tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók FreshDesk bejelentkezni, akkor ki kell építenie FreshDesk be.  
FreshDesk, ha egy kézi tevékenység.

**A felhasználói fiókok létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Freshdesk** bérlő.
2. Kattintson a felső menüben **Admin**.
   
   ![Felügyeleti](./media/active-directory-saas-freshdesk-tutorial/IC776772.png "rendszergazda")

3. Az a **általános beállítások** lapra, majd **ügynökök**.
   
   ![Ügynökök](./media/active-directory-saas-freshdesk-tutorial/IC776773.png "ügynökök")

4. Kattintson a **új ügynök**.
   
    ![Az új ügynök](./media/active-directory-saas-freshdesk-tutorial/IC776774.png "új ügynök")

5. Az ügynök adatait párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
   ![Az ügynök adatait](./media/active-directory-saas-freshdesk-tutorial/IC776775.png "ügynök adatait")
   
   a. Az a **teljes nevét** szövegmező, írja be a kiépítés kívánt Azure AD-fiókot.

   b. Az a **E-mail** szövegmezőhöz típus az Azure AD e-mail címe rendelkezés kívánt Azure AD-fiókot.

   c. Az a **cím** szövegmező, írja be a kívánt rendelkezés Azure AD-fiókot.

   d. Válassza ki **ügynökök szerepkör**, és kattintson a **hozzárendelése**.
       
   e. Kattintson a **Save** (Mentés) gombra.     
   
    >[!NOTE]
    >Az Azure AD fióktulajdonos kap egy e-mailt, amely tartalmaz egy hivatkozást a fiók megerősítéséhez, mielőtt aktívvá válik. 
    > 
    
    >[!NOTE]
    >Bármely más Freshdesk felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz Freshdesk által nyújtott API-k. a FreshDesk.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon nyújtó saját mezőben Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése FreshDesk, hajtsa végre az alábbi lépéseket:**

1. Az Azure felügyeleti portálra, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **FreshDesk**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-freshdesk-tutorial/tutorial_freshdesk_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen FreshDesk csempére kattint, az beszerzése bejelentkezett FreshDesk Alkalmazásmódosítások bejelentkezési oldalt szerezheti be.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-freshdesk-tutorial/tutorial_general_203.png

