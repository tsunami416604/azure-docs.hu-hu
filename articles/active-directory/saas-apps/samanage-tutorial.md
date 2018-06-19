---
title: 'Oktatóanyag: Azure Active Directoryval integrált Samanage |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Samanage között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: f0db4fb0-7eec-48c2-9c7a-beab1ab49bc2
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/05/2017
ms.author: jeedes
ms.openlocfilehash: 2d5bdac5319cd5964ce018819c979d24ab28211a
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35927561"
---
# <a name="tutorial-azure-active-directory-integration-with-samanage"></a>Oktatóanyag: Azure Active Directoryval integrált Samanage

Ebben az oktatóanyagban elsajátíthatja Samanage integrálása az Azure Active Directory (Azure AD).

Samanage integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Samanage hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Samanage (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Samanage, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Samanage egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Samanage hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-samanage-from-the-gallery"></a>A gyűjteményből Samanage hozzáadása
Az Azure AD integrálása a Samanage konfigurálásához kell hozzáadnia Samanage a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Samanage hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Samanage**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/samanage-tutorial/tutorial_samanage_search.png)

5. Az eredmények panelen válassza ki a **Samanage**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/samanage-tutorial/tutorial_samanage_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Samanage.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Samanage a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Samanage közötti kapcsolat kapcsolatot kell létrehozni.

Samanage, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Samanage tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Samanage tesztfelhasználó létrehozása](#creating-a-samanage-test-user)**  - való Britta Simon valami Samanage, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Samanage alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Samanage, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Samanage** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/samanage-tutorial/tutorial_samanage_samlbase.png)

3. Az a **Samanage tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/samanage-tutorial/tutorial_samanage_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<Company Name>.samanage.com/saml_login/<Company Name>`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<Company Name>.samanage.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket és a tényleges bejelentkezési URL-cím és -azonosítót, amelynek az ismertetése, az oktatóanyag későbbi részében. További részletekért forduljon [Samanage ügyfél-támogatási csoport](https://www.samanage.com/support).    
 
4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/samanage-tutorial/tutorial_samanage_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/samanage-tutorial/tutorial_general_400.png)

6. A a **Samanage konfigurációs** kattintson **konfigurálása Samanage** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, és a SAML Entitásazonosító** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/samanage-tutorial/tutorial_samanage_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a Samanage vállalati webhely rendszergazdaként.

8. Kattintson a **irányítópult** válassza **telepítő** bal oldali navigációs ablaktáblán.
   
    ![Irányítópult](./media/samanage-tutorial/tutorial_samanage_001.png "irányítópult")

9. Kattintson a **egyszeri bejelentkezés**.
   
    ![Egyszeri bejelentkezés](./media/samanage-tutorial/tutorial_samanage_002.png "egyszeri bejelentkezés")

10. Navigáljon a **SAML használatával bejelentkezési** területen tegye a következőket:
   
    ![Bejelentkezés SAML](./media/samanage-tutorial/tutorial_samanage_003.png "bejelentkezési SAML használatával")
 
    a. Kattintson a **engedélyezése egyszeri bejelentkezéshez a SAML**.  
 
    b. Az a **identitási szolgáltató URL-cím** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** ami Azure-portálon másolta.    
 
    c. Erősítse meg a **bejelentkezési URL-cím** megegyezik a **URL-cím bejelentkezési** a **Samanage tartomány és az URL-címek** szakaszban az Azure portálon.
 
    d. Az a **kijelentkezési URL-cím** szövegmezőhöz értékének megadása **Sign-Out URL-cím** ami Azure-portálon másolta.
 
    e. Az a **SAML kibocsátó** szövegmező, írja be az alkalmazásazonosító URI a az identitásszolgáltató beállítása.
 
    f. Nyissa meg a base-64 kódolású tanúsítvány a Jegyzettömbben az Azure portálról letöltött, annak tartalmának másolása a vágólapra és illessze be azt a **illessze be az identitásszolgáltató x.509 tanúsítvány az alábbi** szövegmező.
 
    g. Kattintson a **felhasználók létrehozása, ha azok nem léteznek Samanage**.
 
    h. Kattintson a **frissítés**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
 
### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/samanage-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/samanage-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/samanage-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/samanage-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-samanage-test-user"></a>Samanage tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Samanage bejelentkezni, akkor ki kell építenie a Samanage.  
Samanage, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a Samanage vállalati webhely rendszergazdaként.

2. Kattintson a **irányítópult** válassza **telepítő** a bal oldali navigációs ablakban.
   
    ![A telepítő](./media/samanage-tutorial/tutorial_samanage_001.png "beállítása")

3. Kattintson a **felhasználók** lap
   
    ![Felhasználók](./media/samanage-tutorial/tutorial_samanage_006.png "felhasználók")

4. Kattintson a **új felhasználó**.
   
    ![Új felhasználó](./media/samanage-tutorial/tutorial_samanage_007.png "új felhasználó")

5. Típus a **neve** és a **E-mail cím** egy Azure Active Directory-fiók ellátásához, majd kattintson a kívánt **a felhasználó létrehozása**.
   
    ![Hozzon létre felhasználói](./media/samanage-tutorial/tutorial_samanage_008.png "felhasználó létrehozása")
   
   >[!NOTE]
   >Az Azure Active Directory fióktulajdonos fog egy e-maileket és hivatkozásra a fiók megerősítéséhez, mielőtt aktívvá válik. Bármely más Samanage felhasználói fiók létrehozása eszközök vagy API-k által biztosított Samanage kiépítését Azure Active Directory felhasználói fiókokat.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Samanage Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Samanage, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Samanage**.

    ![Egyszeri bejelentkezés konfigurálása](./media/samanage-tutorial/tutorial_samanage_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Samanage csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Samanage alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/samanage-tutorial/tutorial_general_01.png
[2]: ./media/samanage-tutorial/tutorial_general_02.png
[3]: ./media/samanage-tutorial/tutorial_general_03.png
[4]: ./media/samanage-tutorial/tutorial_general_04.png

[100]: ./media/samanage-tutorial/tutorial_general_100.png

[200]: ./media/samanage-tutorial/tutorial_general_200.png
[201]: ./media/samanage-tutorial/tutorial_general_201.png
[202]: ./media/samanage-tutorial/tutorial_general_202.png
[203]: ./media/samanage-tutorial/tutorial_general_203.png

