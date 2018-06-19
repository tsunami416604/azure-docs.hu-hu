---
title: 'Oktatóanyag: Azure Active Directoryval integrált Gigya |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Gigya között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2c7d200b-9242-44a5-ac8a-ab3214a78e41
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/18/2017
ms.author: jeedes
ms.openlocfilehash: 4acc2e8993095ab339b5644f695fb4a55b294152
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35923639"
---
# <a name="tutorial-azure-active-directory-integration-with-gigya"></a>Oktatóanyag: Azure Active Directoryval integrált Gigya

Ebben az oktatóanyagban elsajátíthatja Gigya integrálása az Azure Active Directory (Azure AD).

Gigya integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Megadhatja a Gigya hozzáféréssel rendelkező Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan beolvasása bejelentkezett Gigya (egyszeri bejelentkezés) számára a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs Gigya, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Gigya egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Gigya hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-gigya-from-the-gallery"></a>A gyűjteményből Gigya hozzáadása
Az Azure AD integrálása a Gigya konfigurálásához kell hozzáadnia Gigya a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Gigya hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Gigya**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/gigya-tutorial/tutorial_gigya_search.png)

5. Az eredmények panelen válassza ki a **Gigya**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/gigya-tutorial/tutorial_gigya_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Gigya.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Gigya a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a Gigya közötti kapcsolat kapcsolatot kell létrehozni.

Gigya, rendelje hozzá a értékének a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Gigya tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Gigya tesztfelhasználó létrehozása](#creating-a-gigya-test-user)**  - való Britta Simon valami Gigya, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Gigya alkalmazásban.

**Konfigurálása az Azure AD az egyszeri bejelentkezés Gigya, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Gigya** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/gigya-tutorial/tutorial_gigya_samlbase.png)

3. Az a **Gigya tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/gigya-tutorial/tutorial_gigya_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `http://<companyname>.gigya.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://fidm.gigya.com/saml/v2.0/<companyname>`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [Gigya ügyfél-támogatási csoport](https://www.gigya.com/support-policy/) beolvasni ezeket az értékeket. 
 
4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/gigya-tutorial/tutorial_gigya_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/gigya-tutorial/tutorial_general_400.png)

6. A a **Gigya konfigurációs** kattintson **konfigurálása Gigya** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/gigya-tutorial/tutorial_gigya_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a Gigya vállalati webhely rendszergazdaként.

8. Lépjen **beállítások \> SAML bejelentkezési**, majd kattintson a **Hozzáadás** gomb.
   
    ![SAML bejelentkezési](./media/gigya-tutorial/ic789532.png "SAML-bejelentkezés")

9. Az a **SAML bejelentkezési** területen tegye a következőket:
   
    ![SAML-alapú konfigurációs](./media/gigya-tutorial/ic789533.png "SAML-konfigurációja")
   
    a. Az a **neve** szövegmező, adja meg a konfiguráció nevét.
   
    b. A **kibocsátó** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** , amely az Azure portálról másolta. 
   
    c. A **egyszeri bejelentkezési URL-címe** szövegmezőhöz illessze be az értékét **egyszeri bejelentkezési URL-címe** , amely az Azure portálról másolta.
   
    d. A **név Azonosítóformátumának** szövegmezőhöz illessze be az értékét **azonosító formátuma** , amely az Azure portálról másolta.
   
    e. A base-64 kódolású tanúsítvány megnyitása a Jegyzettömbben az Azure portálról letöltött, a tartalmának másolása a vágólapra és illessze be azt a **X.509 tanúsítvány** szövegmező.
   
    f. Kattintson a **beállítások mentése**.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/gigya-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/gigya-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/gigya-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/gigya-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-gigya-test-user"></a>Gigya tesztfelhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók Gigya bejelentkezni, akkor ki kell építenie Gigya be.  
Gigya, ha egy kézi tevékenység.

### <a name="to-provision-a-user-accounts-perform-the-following-steps"></a>A felhasználói fiókok létrehozásához hajtsa végre az alábbi lépéseket:

1. Jelentkezzen be a **Gigya** vállalati hely rendszergazdaként.

2. Nyissa meg a **Admin \> felhasználók kezelése**, és kattintson a **meghívása felhasználók**.
   
    ![Felhasználók kezelése](./media/gigya-tutorial/ic789535.png "felhasználók kezelése")

3. A felhasználók meghívása párbeszédpanelen hajtsa végre az alábbi lépéseket:
   
    ![Meghívott felhasználóknak](./media/gigya-tutorial/ic789536.png "meghívott felhasználóknak")
   
    a. Az a **E-mail** szövegmező, írja be egy érvényes Azure Active Directory-fiókot rendelkezés kívánt e-mail aliasát.
    
    b. Kattintson a **felhasználó meghívása**.
      
    > [!NOTE]
    > Az Azure Active Directory fióktulajdonos kap egy e-mailt, amely tartalmaz egy hivatkozást a fiók megerősítéséhez, mielőtt aktívvá válik.
    > 
    

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Gigya Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Gigya, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Gigya**.

    ![Egyszeri bejelentkezés konfigurálása](./media/gigya-tutorial/tutorial_gigya_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ez a szakasz célja a hozzáférési panelen az Azure AD SSO-konfigurációjának tesztelése.

Ha a hozzáférési panelen Gigya csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Gigya alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/gigya-tutorial/tutorial_general_01.png
[2]: ./media/gigya-tutorial/tutorial_general_02.png
[3]: ./media/gigya-tutorial/tutorial_general_03.png
[4]: ./media/gigya-tutorial/tutorial_general_04.png

[100]: ./media/gigya-tutorial/tutorial_general_100.png

[200]: ./media/gigya-tutorial/tutorial_general_200.png
[201]: ./media/gigya-tutorial/tutorial_general_201.png
[202]: ./media/gigya-tutorial/tutorial_general_202.png
[203]: ./media/gigya-tutorial/tutorial_general_203.png

