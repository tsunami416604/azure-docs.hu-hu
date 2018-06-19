---
title: 'Oktatóanyag: Azure Active Directory-integráció a Mozy vállalati |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Mozy vállalati között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 489b5e62-85c2-45c9-8766-326632d48114
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: jeedes
ms.openlocfilehash: d4da17c5e2a9da0f18d08da56f7bd6ea6b95a0ef
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35926179"
---
# <a name="tutorial-azure-active-directory-integration-with-mozy-enterprise"></a>Oktatóanyag: Azure Active Directory-integráció a Mozy vállalati

Ebben az oktatóanyagban elsajátíthatja Mozy vállalati integrálása az Azure Active Directory (Azure AD).

Mozy vállalati integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Mozy vállalati hozzáféréssel rendelkező Azure AD-ben
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Mozy vállalati (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Mozy vállalati, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Mozy vállalati egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Mozy vállalati hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-mozy-enterprise-from-the-gallery"></a>A gyűjteményből Mozy vállalati hozzáadása
Az Azure AD integrálása a Mozy Enterprise konfigurálásához kell hozzáadnia Mozy vállalati a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Mozy vállalati hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Mozy vállalati**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_search.png)

5. Az eredmények panelen válassza ki a **Mozy vállalati**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálása és tesztelése az Azure AD egyszeri bejelentkezést a Mozy vállalati "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Mozy vállalati a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó Mozy vállalat közötti kapcsolat kapcsolatot kell létrehozni.

Mozy vállalat, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a vállalati Mozy tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Mozy vállalati tesztfelhasználó létrehozása](#creating-a-mozy-enterprise-test-user)**  - kell rendelkeznie a megfelelője a Britta Simon Mozy vállalat, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Mozy vállalati alkalmazásban.

**Az Azure AD egyszeri bejelentkezést a Mozy vállalati megadásához hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Mozy vállalati** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_samlbase.png)

3. Az a **Mozy vállalati tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_url.png)

    Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<tenantname>.Mozyenterprise.com`

    > [!NOTE] 
    > Ez az érték nincs valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címet. Ügyfél [Mozy vállalati ügyfél-támogatási csoport](http://support.mozy.com/) lekérni ezt az értéket.

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/mozy-enterprise-tutorial/tutorial_general_400.png)

6. A a **Mozy vállalati konfiguráció** kattintson **konfigurálása Mozy vállalati** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a vállalati Mozy vállalati webhely rendszergazdaként.

8. Az a **konfigurációs** kattintson **hitelesítési házirend**.
   
   ![Hitelesítési házirend](./media/mozy-enterprise-tutorial/ic777314.png "hitelesítési házirend")

9. Az a **hitelesítési házirend** területen tegye a következőket:
   
   ![Hitelesítési házirend](./media/mozy-enterprise-tutorial/ic777315.png "hitelesítési házirend")
   
   a. Válassza ki **címtárszolgáltatás** , **szolgáltató**.
   
   b. Válassza ki **LDAP leküldéses használja**.
   
   c. Kattintson a **SAML-alapú hitelesítés** fülre.
   
   d. Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe**, amely az Azure-portálról másolta a **hitelesítés URL-címe** szövegmező.
   
   e. Beillesztés **SAML Entitásazonosító**, amely az Azure-portálról másolta a **SAML-végpont** szövegmező.
   
   f. Nyissa meg a letöltött base-64 kódolású tanúsítvány a Jegyzettömbben, annak tartalmának másolása a vágólapra és illessze be a teljes tanúsítványt **SAML tanúsítvány** szövegmező.
   
   g. Válassza ki **egyszeri bejelentkezés engedélyezése a rendszergazdák számára, hogy jelentkezzen be a hálózati hitelesítő adataik**.
   
   h. Kattintson a **módosítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/mozy-enterprise-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/mozy-enterprise-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/mozy-enterprise-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/mozy-enterprise-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-mozy-enterprise-test-user"></a>Mozy vállalati tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Mozy vállalati bejelentkezni, akkor ki kell építenie Mozy vállalat. Mozy vállalati, ha egy kézi tevékenység.

>[!NOTE]
>Bármely más Mozy vállalati felhasználói fiók létrehozása eszközök, vagy rendelkezés AAD felhasználói fiókokhoz Mozy vállalati által nyújtott API-k.

**A felhasználói fiókok létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **Mozy vállalati** bérlő.

2. Kattintson a **felhasználók**, és kattintson a **új felhasználó hozzáadása**.
   
   ![Felhasználók](./media/mozy-enterprise-tutorial/ic777317.png "felhasználók")
   
   >[!NOTE]
   >A **új felhasználó hozzáadása** a beállítás csak akkor látható, ha csak **Mozy** van jelölve, a szolgáltató **hitelesítési házirend**. Ha SAML-alapú hitelesítés van konfigurálva, majd a felhasználót adnak hozzá automatikusan az egyszeri bejelentkezési keresztül az első bejelentkezés a.
    
3. Az új felhasználói párbeszédpanelen hajtsa végre a következő lépéseket:
   
   ![Felhasználók hozzáadása az](./media/mozy-enterprise-tutorial/ic777318.png "felhasználók hozzáadása")
   
   a. Az a **válasszon ki egy csoportot** listában, válasszon ki egy csoportot.
   
   b. Az a **felhasználó milyen típusú** listára, válassza ki a típus.
   
   c. Az a **felhasználónév** szövegmező, írja be az Azure AD-felhasználó nevét.
   
   d. Az a **E-mail** szövegmező, írja be az Azure AD-felhasználó e-mail címét.
   
   e. Válassza ki **felhasználói utasítás e-mailek küldése**.
   
   f. Kattintson a **felhasználó(k) hozzáadása**.

     >[!NOTE]
     > Miután létrehozta a felhasználó, egy e-mailt kapnak az Azure AD-felhasználó, mielőtt aktívvá válik, győződjön meg arról, hogy a fiók mutató hivatkozást tartalmazó.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Mozy vállalati Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Mozy vállalati, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Mozy vállalati**.

    ![Egyszeri bejelentkezés konfigurálása](./media/mozy-enterprise-tutorial/tutorial_mozyenterprise_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Mozy vállalati csempére kattint, Mozy vállalati alkalmazás bejelentkezési oldalán szerezheti be.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/mozy-enterprise-tutorial/tutorial_general_01.png
[2]: ./media/mozy-enterprise-tutorial/tutorial_general_02.png
[3]: ./media/mozy-enterprise-tutorial/tutorial_general_03.png
[4]: ./media/mozy-enterprise-tutorial/tutorial_general_04.png

[100]: ./media/mozy-enterprise-tutorial/tutorial_general_100.png

[200]: ./media/mozy-enterprise-tutorial/tutorial_general_200.png
[201]: ./media/mozy-enterprise-tutorial/tutorial_general_201.png
[202]: ./media/mozy-enterprise-tutorial/tutorial_general_202.png
[203]: ./media/mozy-enterprise-tutorial/tutorial_general_203.png

