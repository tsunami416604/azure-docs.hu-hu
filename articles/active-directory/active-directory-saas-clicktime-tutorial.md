---
title: "Oktatóanyag: Azure Active Directoryval integrált ClickTime |} Microsoft Docs"
description: "Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és ClickTime között."
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: d437b5ab-4d71-4c13-96d0-79018cebbbd4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/10/2017
ms.author: jeedes
ms.openlocfilehash: 0e0123a40d52dfd7a2e29c29cb2239e979089ca9
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2017
---
# <a name="tutorial-azure-active-directory-integration-with-clicktime"></a>Oktatóanyag: Azure Active Directoryval integrált ClickTime

Ebben az oktatóanyagban elsajátíthatja ClickTime integrálása az Azure Active Directory (Azure AD).

ClickTime integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a ClickTime hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett ClickTime (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](active-directory-appssoaccess-whatis.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs ClickTime, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy ClickTime egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből ClickTime hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-clicktime-from-the-gallery"></a>A gyűjteményből ClickTime hozzáadása
Az Azure AD integrálása a ClickTime konfigurálásához kell hozzáadnia ClickTime a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből ClickTime hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **ClickTime**, jelölje be **ClickTime** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában ClickTime](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján ClickTime.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó ClickTime a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a ClickTime közötti kapcsolat kapcsolatot kell létrehozni.

ClickTime, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a ClickTime tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[ClickTime tesztfelhasználó létrehozása](#create-a-clicktime-test-user)**  - való Britta Simon valami ClickTime, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az ClickTime alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés ClickTime, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **ClickTime** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_samlbase.png)

3. Az a **ClickTime tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk ClickTime tartomány és az URL-címek](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_url.png)

    a. Az a **azonosító** szövegmező, adja meg az URL-címet:`https://app.clicktime.com/sp/`
    
    b. Az a **válasz URL-CÍMEN** szövegmezőhöz URL-címet a következő minták használatával írja be: 

    | |
    |--|
    | `https://app.clicktime.com/Login/` |
    | `https://app.clicktime.com/App/Login/Consume.aspx` |

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/active-directory-saas-clicktime-tutorial/tutorial_general_400.png)

6. A a **ClickTime konfigurációs** kattintson **konfigurálása ClickTime** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![ClickTime konfiguráció](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a ClickTime vállalati webhely rendszergazdaként.

8. A felső eszköztáron kattintson **beállítások**, és kattintson a **biztonsági beállítások**.

9. Az a **egyszeri bejelentkezési beállítások** konfigurációs szakaszban, hajtsa végre a következő lépéseket:
   
    ![Biztonsági beállítások](./media/active-directory-saas-clicktime-tutorial/tic777280.png "biztonsági beállítások")
   
    a.  Válassza ki **engedélyezése** jelentkezzen be egyszeri bejelentkezés (SSO) használatával **az Azure AD**.
   
    b. Az a **identitás szolgáltatói végpont** szövegmezőhöz Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.
   
    c.  Nyissa meg a **base-64 kódolású tanúsítvány** az Azure portálról letöltött **Jegyzettömb**, és másolja a tartalmat, majd illessze be azt a **X.509 tanúsítvány** szövegmező.
   
    d.  Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/active-directory-saas-clicktime-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/active-directory-saas-clicktime-tutorial/create_aaduser_02.png) 

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.
 
    ![A Hozzáadás gombra.](./media/active-directory-saas-clicktime-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:
 
    ![A felhasználó párbeszédpanel](./media/active-directory-saas-clicktime-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-clicktime-test-user"></a>ClickTime tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók ClickTime bejelentkezni, akkor ki kell építenie ClickTime be.  
ClickTime, ha egy kézi tevékenység.

> [!NOTE]
> Bármely más ClickTime felhasználói fiók létrehozása eszközök vagy ClickTime kiépíteni az Azure AD-felhasználói fiókok által nyújtott API-k.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**
1. Jelentkezzen be a **ClickTime** bérlő.
2. A felső eszköztáron kattintson **vállalati**, és kattintson a **személyek**.
   
    ![Személyek](./media/active-directory-saas-clicktime-tutorial/tic777282.png "személyek")
3. Kattintson a **személy hozzáadása**.
   
    ![Adja hozzá a személy](./media/active-directory-saas-clicktime-tutorial/tic777283.png "személy hozzáadása")
4. Új személy csoportjában hajtsa végre az alábbi lépéseket:
   
    ![Személyek](./media/active-directory-saas-clicktime-tutorial/tic777284.png "személyek")
   
    a.  Az a **teljes név** szövegmező, például a felhasználó teljes név típusa **Britta Simon**. 
  
    b.  Az a **e-mail cím** szövegmezőben, az e-mailt a felhasználó típusát, például  **brittasimon@contoso.com** .
       
    > [!NOTE]
    > Ha szeretné, beállíthatja az új személy objektum további tulajdonságokat.
   
    c.  Kattintson a **Save** (Mentés) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés ClickTime Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése ClickTime, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **ClickTime**.

    ![Az alkalmazások listáját a ClickTimne hivatkozás](./media/active-directory-saas-clicktime-tutorial/tutorial_clicktime_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen ClickTime csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az ClickTime alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](active-directory-appssoaccess-whatis.md)



<!--Image references-->

[1]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-clicktime-tutorial/tutorial_general_203.png

