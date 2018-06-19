---
title: 'Oktatóanyag: Azure Active Directoryval integrált Freshservice |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Freshservice között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 3dd22b1f-445d-45c6-8eda-30207eb9a1a8
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/16/2017
ms.author: jeedes
ms.openlocfilehash: 7669f9e49d62f4fb5a57c9e15b7f6196f21d815b
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35931446"
---
# <a name="tutorial-azure-active-directory-integration-with-freshservice"></a>Oktatóanyag: Azure Active Directoryval integrált Freshservice

Ebben az oktatóanyagban elsajátíthatja Freshservice integrálása az Azure Active Directory (Azure AD).

Freshservice integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Freshservice hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Freshservice (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Freshservice, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Freshservice egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Freshservice hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-freshservice-from-the-gallery"></a>A gyűjteményből Freshservice hozzáadása
Az Azure AD integrálása a Freshservice konfigurálásához kell hozzáadnia Freshservice a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Freshservice hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Freshservice**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/freshservice-tutorial/tutorial_freshservice_search.png)

5. Az eredmények panelen válassza ki a **Freshservice**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/freshservice-tutorial/tutorial_freshservice_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Freshservice.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Freshservice a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Freshservice közötti kapcsolat kapcsolatot kell létrehozni.

Freshservice, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Freshservice tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Freshservice tesztfelhasználó létrehozása](#creating-a-freshservice-test-user)**  - való Britta Simon valami Freshservice, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Freshservice alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Freshservice, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Freshservice** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/freshservice-tutorial/tutorial_freshservice_samlbase.png)

3. Az a **Freshservice tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/freshservice-tutorial/tutorial_freshservice_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<democompany>.freshservice.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<democompany>.freshservice.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [Freshservice ügyfél-támogatási csoport](https://support.freshservice.com/) beolvasni ezeket az értékeket. 
 
4. A a **SAML-aláíró tanúsítványa** területen másolása **UJJLENYOMAT** tanúsítvány értékét.

    ![Egyszeri bejelentkezés konfigurálása](./media/freshservice-tutorial/tutorial_freshservice_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/freshservice-tutorial/tutorial_general_400.png)

6. A a **Freshservice konfigurációs** kattintson **konfigurálása Freshservice** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/freshservice-tutorial/tutorial_freshservice_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a Freshservice vállalati webhely rendszergazdaként.

8. Kattintson a felső menüben **Admin**.
   
    ![Felügyeleti](./media/freshservice-tutorial/ic790814.png "rendszergazda")

9. Az a **Ügyfélportálra**, kattintson a **biztonsági**.
   
    ![Biztonsági](./media/freshservice-tutorial/ic790815.png "biztonsági")

10. Az a **biztonsági** területen tegye a következőket:
   
    ![Egyszeri bejelentkezés](./media/freshservice-tutorial/ic790816.png "egyszeri bejelentkezés")
   
    a. Kapcsoló **egyszeri bejelentkezés**.

    b. Válassza ki **SAML SSO**.

    c. Az a **SAML bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.

    d. Az a **kijelentkezési URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím** ami Azure-portálon másolta.

    e. A **biztonsági tanúsítvány-ujjlenyomat** szövegmező, illessze be a **UJJLENYOMAT** érték tanúsítvány, amely az Azure-portálon másolta.

    f. Kattintson a **Mentés** gombra.
   
> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/freshservice-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/freshservice-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/freshservice-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/freshservice-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-freshservice-test-user"></a>Freshservice tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók FreshService bejelentkezni, akkor ki kell építenie a FreshService. FreshService, ha egy kézi tevékenység.

**Felhasználói fiók létrehozásához hajtsa végre az alábbi lépéseket:**

1. Jelentkezzen be a **FreshService** vállalati hely rendszergazdaként.

2. Kattintson a felső menüben **Admin**.
   
    ![Felügyeleti](./media/freshservice-tutorial/ic790814.png "rendszergazda")

3. Az a **felhasználókezelés** kattintson **engedélyezését**.
   
    ![Engedélyezését](./media/freshservice-tutorial/ic790818.png "engedélyezését")

4. Kattintson a **új kérelmező**.
   
    ![Új engedélyezését](./media/freshservice-tutorial/ic790819.png "új engedélyezését")

5. Az a **új kérelmező** területen tegye a következőket:
   
    ![Új kérelmező](./media/freshservice-tutorial/ic790820.png "új kérelmező")   

    a. Adja meg a **Utónév** és **E-mail** egy érvényes Azure Active Directory-fiókot szeretné azokat a kapcsolódó szövegmezők rendelkezés attribútumait.

    b. Kattintson a **Save** (Mentés) gombra.
   
    >[!NOTE]
    >Az Azure Active Directory fióktulajdonos kap egy e-mailt, mielőtt aktívvá válik, győződjön meg arról, hogy a fiók hivatkozással
    >  

>[!NOTE]
>Bármely más FreshService felhasználói fiók létrehozása eszközök vagy rendelkezés AAD felhasználói fiókokhoz FreshService által nyújtott API-k.
>  

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Freshservice, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Freshservice**.

    ![Egyszeri bejelentkezés konfigurálása](./media/freshservice-tutorial/tutorial_freshservice_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja tesztelése az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen Freshservice csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Freshservice alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/freshservice-tutorial/tutorial_general_01.png
[2]: ./media/freshservice-tutorial/tutorial_general_02.png
[3]: ./media/freshservice-tutorial/tutorial_general_03.png
[4]: ./media/freshservice-tutorial/tutorial_general_04.png

[100]: ./media/freshservice-tutorial/tutorial_general_100.png

[200]: ./media/freshservice-tutorial/tutorial_general_200.png
[201]: ./media/freshservice-tutorial/tutorial_general_201.png
[202]: ./media/freshservice-tutorial/tutorial_general_202.png
[203]: ./media/freshservice-tutorial/tutorial_general_203.png

