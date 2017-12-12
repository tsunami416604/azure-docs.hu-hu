---
title: "Oktatóanyag: Azure Active Directory-integráció a Splunk vállalati és Splunk felhő |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Splunk vállalati és Splunk felhő között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: b3e2b4a9-749c-4895-813d-db46f8dfdbf8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/09/2017
ms.author: jeedes
ms.openlocfilehash: 82a7f8ec5be62848b45b23fce1e012aaad3b1798
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="tutorial-azure-active-directory-integration-with-splunk-enterprise-and-splunk-cloud"></a>Oktatóanyag: Azure Active Directory-integráció a Splunk vállalati és Splunk felhő

Ebben az oktatóanyagban elsajátíthatja Splunk vállalati és Splunk felhő integrálása az Azure Active Directory (Azure AD).

Splunk vállalati és Splunk felhő integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja, aki hozzáfér Splunk vállalati és Splunk felhőalapú Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a Splunk vállalati és Splunk felhő egyszeri bejelentkezés (SSO) és az Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen - a klasszikus Azure portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálja az Azure AD-integrációs Splunk vállalati és Splunk felhő, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Splunk vállalati vagy Splunk felhő SSO engedélyezve előfizetés


>[!NOTE]
>Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.
>

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, ha ez nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, beszerezheti a [egy hónapos próbaverzió](https://azure.microsoft.com/pricing/free-trial/).


## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben.

Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. Splunk vállalati és Splunk felhő hozzáadása a gyűjteményből
2. Azure AD egyszeri bejelentkezés tesztelése és konfigurálása


## <a name="add-splunk-enterprise-and-splunk-cloud-from-the-gallery"></a>Splunk vállalati és Splunk felhő hozzáadása a gyűjteményből
Az Azure AD integrálása a Splunk vállalati és Splunk felhő konfigurálásához kell hozzáadnia Splunk vállalati és a felhő Splunk a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Splunk vállalati és Splunk felhő hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a **a klasszikus Azure portálon**, a bal oldali navigációs ablaktábláján kattintson **Active Directory**.

    ![Active Directory][1]

2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.

3. A könyvtár nézetben a alkalmazások nézet megnyitásához kattintson **alkalmazások** a felső menüben.

    ![Alkalmazások][2]

4. Kattintson a **Hozzáadás** az oldal alján.

    ![Alkalmazások][3]

5. Az a **mi történjen a teendő** párbeszédpanel, kattintson a **hozzáadhat egy alkalmazást a katalógusból**.

    ![Alkalmazások][4]

6. Írja be a keresőmezőbe, **Splunk vállalati vagy Splunk felhő**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_01.png)

7. Az eredmények ablaktáblájában válassza **Splunk vállalati és Splunk felhő**, és kattintson a **Complete** hozzáadása az alkalmazáshoz.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_02.png)

##  <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása
Ebben a szakaszban az Azure AD egyszeri bejelentkezést a vállalati Splunk tesztelése és konfigurálása, és Splunk felhő "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó Splunk vállalati és Splunk felhő Újdonságok egy felhasználó számára az Azure ad-ben. Ez azt jelenti egy Azure AD-felhasználó és a kapcsolódó felhasználó a Splunk Enterprise és Splunk felhő közötti kapcsolat kapcsolatot kell létrehozni.

Ez a hivatkozás kapcsolat létesíti értéket rendeli az **felhasználónév** értékeként Azure AD-ben a **felhasználónév** Splunk vállalati és Splunk felhő.

Az Azure AD az egyszeri bejelentkezés Splunk vállalati és Splunk felhő tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Splunk vállalati és Splunk felhő tesztfelhasználó létrehozása](#creating-a-splunk-enterprise-and-splunk-cloud-test-user)**  - kell rendelkeznie a partner Britta Simon Splunk vállalati és Splunk felhő, amely csatolva van rá, hogy az Azure AD ábrázolása.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri Bejelentkezést a klasszikus portálon, és egyszeri bejelentkezés konfigurálása az Splunk vállalati és Splunk felhőalapú alkalmazásokhoz.


**Konfigurálja az Azure AD az egyszeri bejelentkezés Splunk vállalati és Splunk felhő, hajtsa végre az alábbi lépéseket:**

1. A klasszikus portálon a a **Splunk vállalati és Splunk felhő** alkalmazás integráció lapján, kattintson a **konfigurálása egyszeri bejelentkezéshez** megnyitásához a **konfigurálása egyszeri bejelentkezéshez** párbeszédpanel.
     
    ![Egyszeri bejelentkezés konfigurálása][6] 

2. Az a **hová felhasználók bejelentkezhetnek a Splunk vállalati és Splunk felhő** lapon jelölje be **az Azure AD az egyszeri bejelentkezés**, és kattintson a **tovább**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_03.png) 

3. Az a **Alkalmazásbeállítások konfigurálása** párbeszédpanel lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_04.png) 
  1. Az a **URL-cím bejelentkezési** szövegmező, írja be az URL-címet használják-e a felhasználók bejelentkezés az Splunk vállalati és Splunk felhő alkalmazáshoz, a következő minta használatával:`https://<splunkserverUrl>/en-US/app/launcher/home`
  2. Az a **azonosító** szövegmezőhöz a Splunk kiszolgáló URL-címét.
  3. Az a **válasz URL-CÍMEN** szövegmező, írja be a következő mintával az URL-cím:`https://<splunkserver>/saml/acs`
  4. Kattintson a **Tovább** gombra.
 
4. Az a **Splunk vállalati és Splunk felhő egyszeri bejelentkezés konfigurálásáról** lapon, a következő lépésekkel:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_05.png)
  1. Kattintson a **metaadatok letöltése**, majd mentse a fájlt a számítógépen.
  2. Kattintson a **Tovább** gombra.

5. Ahhoz, hogy az alkalmazáshoz konfigurált SSO, forduljon a Splunk vállalati és Splunk felhő támogatási csoport, és adja meg azokat a következő:

    * A letöltött **federaton metaadatok**
6. A klasszikus portálon, válassza ki az egyszeri bejelentkezés konfigurációs megerősítő, és kattintson **következő**.
    
    ![Az Azure AD-egyszeri bejelentkezés][10]

7. Az a **az egyszeri bejelentkezés megerősítő** kattintson **Complete**.  
 
    ![Az Azure AD-egyszeri bejelentkezés][11]

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ebben a szakaszban Britta Simon neve a klasszikus portálon tesztfelhasználó létrehozása.

![Az Azure AD-felhasználó létrehozása][20]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **a klasszikus Azure portálon**, a bal oldali navigációs ablaktábláján kattintson **Active Directory**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_09.png) 

2. Az a **Directory** listára, válassza ki a könyvtárat, amelyhez a címtár-integrációs engedélyezni szeretné.

3. A felhasználók listájának megjelenítéséhez a felső menüben, kattintson a **felhasználók**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_03.png) 

4. Lehetőségre a **felhasználó hozzáadása** párbeszédpanel alján, az eszköztárban kattintson **felhasználó hozzáadása**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_04.png) 

5. Az a **adja meg azt a felhasználó** párbeszédpanel lapon, a következő lépésekkel:

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_05.png) 
  1. A felhasználó típusát válassza ki az új felhasználót a szervezetében.
  2. A felhasználó nevében **szövegmező**, típus **BrittaSimon**.
  3. Kattintson a **Tovább** gombra.

6.  Az a **felhasználói profil** párbeszédpanel lapon, a következő lépésekkel:
  
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_06.png) 
  1. Az a **Utónév** szövegmezőhöz típus **Britta**.  
  2. Az a **Vezetéknév** szövegmezőhöz típusa, **Simon**.
  3. Az a **megjelenített név** szövegmezőhöz típus **Britta Simon**.
  4. Az a **szerepkör** listáról válassza ki **felhasználói**.
  5. Kattintson a **Tovább** gombra.

7. Az a **ideiglenes jelszó beszerzése** párbeszédpanel lap, kattintson a **létrehozása**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_07.png) 

8. Az a **ideiglenes jelszó beszerzése** párbeszédpanel lapon, a következő lépésekkel:

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/create_aaduser_08.png) 
  1. Jegyezze fel az értéket a **új jelszó**.
  2. Kattintson a **Befejezés** gombra.   

### <a name="create-a-splunk-enterprise-and-splunk-cloud-test-user"></a>Splunk vállalati és Splunk felhő tesztfelhasználó létrehozása

Ebben a szakaszban Britta Simon Splunk vállalati és Splunk felhő nevű felhasználó létrehozása. Splunk vállalati és Splunk felhő támogatási csoport hozzáadása a felhasználók a vállalati Splunk és Splunk felhő platform működik.


### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban Azure SSOy saját hozzáférés biztosítása az Splunk vállalati és a felhő Splunk használandó Britta Simon engedélyezi.

![Felhasználó hozzárendelése][200] 

**Britta Simon Splunk vállalati és Splunk felhő hozzárendelése, hajtsa végre az alábbi lépéseket:**

1. A klasszikus portál megnyitásához az alkalmazások megtekintése a könyvtár nézetben kattintson **alkalmazások** a felső menüben.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Splunk vállalati és Splunk felhő**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_splunk_50.png) 

3. Kattintson a felső menüben **felhasználók**.

    ![Felhasználó hozzárendelése][203]

4. A felhasználók listában válassza ki a **Britta Simon**.

5. Kattintson az alsó eszköztár **hozzárendelése**.

    ![Felhasználó hozzárendelése][205]

### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD SSOonfiguration a hozzáférési panelen tesztelése.

A Splunk vállalati kattint, és a hozzáférési Panel Splunk felhő csempére, amikor meg kell beolvasása automatikusan bejelentkezett az Splunk vállalati és Splunk felhő alkalmazására.


## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)


<!--Image references-->

[1]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_04.png

[6]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_05.png
[10]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_06.png
[11]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_07.png
[20]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_201.png
[203]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_203.png
[204]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_204.png
[205]: ./media/active-directory-saas-splunk-enterprise-and-splunk-cloud-tutorial/tutorial_general_205.png
