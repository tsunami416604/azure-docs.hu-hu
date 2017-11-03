---
title: "Oktatóanyag: Azure Active Directory-integráció Wizergos termelékenység szoftverrel |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a termelékenység szoftver Wizergos között."
services: active-directory
documentationcenter: 
author: jeevansd
manager: femila
editor: 
ms.assetid: acc04396-13c5-4c24-ab9a-30fbc9234ebd
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/20/2017
ms.author: jeedes
ms.openlocfilehash: 73b3bc05aeb337c12acb7e47c0dbebe6d0196530
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-wizergos-productivity-software"></a>Oktatóanyag: Azure Active Directory-integráció Wizergos termelékenység szoftverrel
Ez az oktatóanyag célja Wizergos termelékenység szoftver integrálása az Azure Active Directory (Azure AD) mutatjuk be.

Wizergos termelékenység szoftver integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

* Szabályozhatja az Azure AD, aki hozzáfér Wizergos termelékenység szoftver
* Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Wizergos termelékenység szoftver egyszeri bejelentkezés (SSO) és az Azure AD-fiókok
* Kezelheti a fiókokat, egy központi helyen - a klasszikus Azure portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek
Az Azure AD-integráció konfigurálása Wizergos termelékenység szoftverrel, a következőkre van szükség:

* Az Azure AD szolgáltatásra
* Egy Wizergos termelékenység szoftver SSO előfizetés engedélyezése

>[!NOTE]
>Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben. 
> 

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

* Ne használja az éles környezetben, ha ez nem szükséges.
* Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, beszerezheti a [egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ez az oktatóanyag célja ahhoz, hogy az Azure AD SSO teszteléséhez tesztkörnyezetben.

Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Wizergos termelékenység szoftver hozzáadása
2. Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

## <a name="adding-wizergos-productivity-software-from-the-gallery"></a>A gyűjteményből Wizergos termelékenység szoftver hozzáadása
Az Azure AD integrálása a Wizergos termelékenység szoftver konfigurálásához kell hozzáadnia Wizergos termelékenység szoftver a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Wizergos termelékenység szoftver hozzáadása a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a **a klasszikus Azure portálon**, a bal oldali navigációs ablaktábláján kattintson **Active Directory**. 
   
    ![Active Directory][1]
2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.
3. A könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.
   
    ![Alkalmazások][2]
4. Kattintson a **Hozzáadás** az oldal alján.
   
    ![Alkalmazások][3]
5. Az a **mi történjen a teendő** párbeszédpanel, kattintson a **hozzáadhat egy alkalmazást a katalógusból**.
   
    ![Alkalmazások][4]
6. Írja be a keresőmezőbe, **Wizergos termelékenység szoftver**.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_01.png)
7. Az eredmények panelen válassza ki a **Wizergos termelékenység szoftver**, és kattintson a **Complete** hozzáadása az alkalmazáshoz.
   
    ![Az alkalmazás kiválasztása a katalógusban](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_001.png)

## <a name="configure-and-test-azure-ad-sso"></a>Azure AD egyszeri bejelentkezés tesztelése és konfigurálása
Ez a szakasz célja mutatjuk be, hogyan lehet konfigurálni és tesztelése az Azure AD SSO "Britta Simon" nevű tesztfelhasználó alapján Wizergos termelékenység szoftverrel.

Az egyszeri bejelentkezés működjön az Azure AD tudnia kell, a partner felhasználó Wizergos termelékenység szoftver egy olyan felhasználó számára az Azure ad-ben van. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó Wizergos termelékenység szoftver közötti kapcsolat kapcsolatot kell létrehozni.

A hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** Wizergos termelékenység szoftverben.

Az Azure AD egyszeri bejelentkezést a BynWizergos termelékenység Softwareder tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Wizergos termelékenység szoftver tesztfelhasználó létrehozása](#creating-a-wizergos-productivity-software-test-user)**  - való egy megfelelője a Britta Simon Wizergos termelékenység szoftver, amely csatolva van rá, hogy az Azure AD ábrázolása.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-sso"></a>Az Azure AD-egyszeri bejelentkezés konfigurálása
Ebben a szakaszban az Azure AD egyszeri bejelentkezéssel a klasszikus portálon, és a Wizergos termelékenység alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD az egyszeri bejelentkezés Wizergos termelékenység szoftverrel, hajtsa végre az alábbi lépéseket:**

1. A klasszikus portálon a a **Wizergos termelékenység szoftver** alkalmazás integráció lapján, kattintson a **konfigurálása egyszeri bejelentkezéshez** megnyitásához a **konfigurálása egyszeri bejelentkezéshez**  párbeszédpanel.
   
    ![Egyszeri bejelentkezés konfigurálása][6] 
2. A a **hová felhasználók bejelentkezhetnek a Wizergos termelékenység szoftver** lapon jelölje be **az Azure AD az egyszeri bejelentkezés**, és kattintson a **következő**:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_03.png)
3. Az a **Alkalmazásbeállítások konfigurálása** párbeszédpanel lap, kattintson a **következő**:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_04.png)
4. A a **konfigurálhatja az egyszeri bejelentkezés Wizergos termelékenység szoftver** kattintson **tanúsítvánnyal letöltés**, majd mentse a fájlt a számítógépen:
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_05.png)
5. Egy másik webes böngészőablakban bejelentkezés a Wizergos termelékenység szoftver bérlő rendszergazdaként.
6. A Hamburg menüben válassza ki a **Admin**.
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_000.png)
7. A rendszergazda lap bal oldali menüben válassza ki **hitelesítési** , majd kattintson a **az Azure AD**.
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_002.png)
8. Hajtsa végre a következő lépéseket **hitelesítési** szakasz.
   
    ![Egyszeri bejelentkezés az alkalmazás ügyféloldali konfigurálása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_003.png)
  1. Kattintson a **FELTÖLTÉSE** gomb az Azure AD a letöltött tanúsítvány feltöltése. 
  2. Az a **kiállítójának URL-címe** szövegmezőbe írja be az értéket a **kiállítójának URL-címe** az Azure AD alkalmazás-konfigurációs varázsló.
  3. Az a **egyszeri bejelentkezési URL-cím** szövegmezőbe írja be az értéket a **egyszeri bejelentkezési URL-címe** az Azure AD alkalmazás-konfigurációs varázsló.
  4. Az a **egyetlen Sign-Out URL-címet** szövegmezőbe írja be az értéket a **egyetlen Sign-out URL-címe** az Azure AD alkalmazás-konfigurációs varázsló.
  5. Kattintson a **mentése** gombra.
9. A klasszikus portálon, válassza ki az egyszeri bejelentkezés konfigurációs megerősítő, és kattintson **következő**.
   
    ![Az Azure AD-egyszeri bejelentkezés][10]
10. Az a **az egyszeri bejelentkezés megerősítő** kattintson **Complete**.  
    
    ![Az Azure AD-egyszeri bejelentkezés][11]

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz célja a tesztfelhasználó létrehozása Britta Simon neve a klasszikus portálon.

![Az Azure AD-felhasználó létrehozása][20]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **a klasszikus Azure portálon**, a bal oldali navigációs ablaktábláján kattintson **Active Directory**.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_09.png)
2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.
3. A felhasználók listájának megjelenítéséhez a felső menüben, kattintson a **felhasználók**.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_03.png)
4. Lehetőségre a **felhasználó hozzáadása** párbeszédpanel alján, az eszköztárban kattintson **felhasználó hozzáadása**.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_04.png)
5. Az a **adja meg azt a felhasználó** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_05.png) 
  1. A felhasználó típusát válassza ki az új felhasználót a szervezetében.
  2. A felhasználó nevében **szövegmező**, típus **BrittaSimon**.
  3. Kattintson a **Tovább** gombra.
6. Az a **felhasználói profil** párbeszédpanel lapon, a következő lépésekkel:
   
   ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_06.png)
  1. Az a **Utónév** szövegmezőhöz típus **Britta**.  
  2. Az a **Vezetéknév** szövegmezőhöz típusa, **Simon**.
  3. Az a **megjelenített név** szövegmezőhöz típus **Britta Simon**.
  4. Az a **szerepkör** listáról válassza ki **felhasználói**.
  5. Kattintson a **Tovább** gombra.
7. Az a **ideiglenes jelszó beszerzése** párbeszédpanel lap, kattintson a **létrehozása**.
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_07.png)
8. Az a **ideiglenes jelszó beszerzése** párbeszédpanel lapon, a következő lépésekkel:
   
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/create_aaduser_08.png)
  1. Jegyezze fel az értéket a **új jelszó**.
  2. Kattintson a **Befejezés** gombra.   

### <a name="create-a-wizergos-productivity-software-test-user"></a>Wizergos termelékenység szoftver tesztfelhasználó létrehozása
Ebben a szakaszban egy felhasználó Britta Simon meghívta Wizergos termelékenység szoftver hoz létre. Adjon Wizergos termelékenység szoftver támogatási csoport keresztül együttműködésre [ support@wizergos.com ](emailTo:support@wizergos.com) a felhasználók hozzáadása a Wizergos termelékenység szoftver platform.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó
Ez a szakasz célja Britta Simon nyújtó saját Wizergos termelékenység szoftver Azure SSO használandó engedélyezése.

  ![Felhasználó hozzárendelése][200]

**Britta Simon hozzárendelése Wizergos termelékenység szoftver, hajtsa végre az alábbi lépéseket:**

1. A klasszikus portál megnyitásához az alkalmazások megtekintése a könyvtár nézetben kattintson **alkalmazások** a felső menüben.
   
    ![Felhasználó hozzárendelése][201]
2. Az alkalmazások listában válassza ki a **Wizergos termelékenység szoftver**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_wizergosproductivitysoftware_50.png)
3. Kattintson a felső menüben **felhasználók**.
   
    ![Felhasználó hozzárendelése][203]
4. A felhasználók listában válassza ki a **Britta Simon**.
5. Kattintson az alsó eszköztár **hozzárendelése**.
   
    ![Felhasználó hozzárendelése][205]

### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése
Ez a szakasz célja a hozzáférési panelen az Azure AD SSO-konfigurációjának tesztelése.

Ha a hozzáférési panelen Wizergos termelékenység szoftver csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Wizergos termelékenység szoftverfrissítések alkalmazására.

## <a name="additional-resources"></a>További források
* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)

<!--Image references-->

[1]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-wizergosproductivitysoftware-tutorial/tutorial_general_205.png
