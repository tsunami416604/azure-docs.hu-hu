---
title: 'Oktatóanyag: Azure Active Directory-integráció Igloo szoftverrel |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és Igloo szoftverek között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 2eb625c1-d3fc-4ae1-a304-6a6733a10e6e
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: cea66f48d3ec1ab99b2b1e3dc40ab559e0ca55a5
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36229158"
---
# <a name="tutorial-azure-active-directory-integration-with-igloo-software"></a>Oktatóanyag: Azure Active Directory-integráció Igloo szoftverrel

Ebben az oktatóanyagban elsajátíthatja Igloo szoftver integrálása az Azure Active Directory (Azure AD).

Igloo szoftver integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja az Azure AD, aki hozzáfér Igloo szoftver
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Igloo szoftverre (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Igloo szoftverrel, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Igloo szoftver egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Igloo szoftver hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-igloo-software-from-the-gallery"></a>A gyűjteményből Igloo szoftver hozzáadása
Az Azure AD integrálása a Igloo szoftver konfigurálásához kell hozzáadnia Igloo szoftver a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Igloo hozzáadása a gyűjteményből, hajtsa végre a következő lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Igloo szoftver**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/igloo-software-tutorial/tutorial_igloosoftware_search.png)

5. Az eredmények panelen válassza ki a **Igloo szoftver**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/igloo-software-tutorial/tutorial_igloosoftware_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezéshez "Britta Simon" nevű tesztfelhasználó alapján Igloo szoftverrel.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó Igloo szoftver a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó Igloo szoftver közötti kapcsolat kapcsolatot kell létrehozni.

Igloo szoftver, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés Igloo szoftverrel tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Egy Igloo szoftver tesztfelhasználó létrehozása](#creating-an-igloo-software-test-user)**  - való egy megfelelője a Britta Simon Igloo szoftver, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a Igloo alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD az egyszeri bejelentkezés Igloo szoftverrel, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Igloo szoftver** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/igloo-software-tutorial/tutorial_igloosoftware_samlbase.png)

3. Az a **Igloo szoftver tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/igloo-software-tutorial/tutorial_igloosoftware_url.png)
    
    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<company name>.igloocommmunities.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<company name>.igloocommmunities.com/saml.digest`

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: `https://<company name>.igloocommmunities.com/saml.digest`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [Igloo szoftver ügyfél-támogatási csoport](https://www.igloosoftware.com/services/support) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/igloo-software-tutorial/tutorial_igloosoftware_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/igloo-software-tutorial/tutorial_general_400.png)
    
6. A a **Igloo szoftverkonfigurációt** kattintson **Igloo szoftver konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out és SAML-alapú egyszeri bejelentkezés szolgáltatás URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/igloo-software-tutorial/tutorial_igloosoftware_configure.png) 

7. Egy másik webes böngészőablakban jelentkezzen be a Igloo szoftver vállalati webhely rendszergazdaként.

8. Lépjen a **vezérlőpultot**.
   
     ![Vezérlőpult](./media/igloo-software-tutorial/ic799949.png "vezérlőpultot")

9. Az a **tagsági** lapra, majd **beállításaival bejelentkezési**.
   
    ![Jelentkezzen be a beállítások](./media/igloo-software-tutorial/ic783968.png "beállítások bejelentkezés")

10. A SAML-alapú konfigurációs szakaszban kattintson **SAML-alapú hitelesítés beállítása**.
   
    ![SAML-alapú konfigurációs](./media/igloo-software-tutorial/ic783969.png "SAML-konfigurációja")
   
11. Az a **általános konfigurációs** területen tegye a következőket:
   
    ![Általános konfiguráció](./media/igloo-software-tutorial/ic783970.png "általános konfiguráció")

    a. A a **kapcsolatnév** szövegmező, írjon be egy egyéni nevet a konfigurációhoz.
   
    b. Az a **IdP bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.
   
    c. Az a **IdP kijelentkezési URL-cím** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím** ami Azure-portálon másolta.
    
    d. Válassza ki **kijelentkezési válasz és kérelem HTTP típusú** , **POST**.
   
    e. Nyissa meg a **base-64** kódolt tanúsítvány a Jegyzettömbben az Azure portálról letöltött, annak tartalmának másolása a vágólapra és illessze be azt a **nyilvános tanúsítvány** szövegmező.
    
12. Az a **válasz és a hitelesítési beállításokat**, hajtsa végre a következő lépéseket:
    
    ![Válasz és a hitelesítési beállításokat](./media/igloo-software-tutorial/IC783971.png "válasz és hitelesítés konfigurációját.")
  
      a. Mint **identitásszolgáltató**, jelölje be **Microsoft ADFS**.
      
      b. Mint **azonosítótípusa**, jelölje be **E-mail cím**. 

      c. Az a **E-mail attribútum** szövegmezőhöz típus **emailaddress**.

      d. Az a **Keresztnév attribútum** szövegmezőhöz típus **givenname**.

      e. Az a **utolsó Name attribútum** szövegmezőhöz típus **vezetékneve**.

13. Hajtsa végre az alábbi lépéseket a konfigurálás befejezéséhez:
    
    ![Jelentkezzen be a felhasználó létrehozása](./media/igloo-software-tutorial/IC783972.png "jelentkezzen be a felhasználó létrehozása") 

     a. Mint **jelentkezzen be a felhasználó létrehozása**, jelölje be **a hely új felhasználó létrehozása, amikor bejelentkeznek a**.

     b. Mint **beállítások bejelentkezés**, jelölje be **használható SAML gomb a "Bejelentkezés" képernyőn**.

     c. Kattintson a **Save** (Mentés) gombra.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/igloo-software-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/igloo-software-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/igloo-software-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/igloo-software-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-an-igloo-software-test-user"></a>Egy Igloo szoftver tesztfelhasználó létrehozása

Nincs művelet elem ahhoz, hogy a felhasználók átadása Igloo szoftver konfigurálása.  

Ha egy hozzárendelt felhasználó megpróbál bejelentkezni a hozzáférési panelen Igloo szoftver, a Igloo szoftver ellenőrzi, hogy a felhasználó létezik-e.  Ha nincs felhasználói fiók elérhető még, automatikusan létrejön Igloo szoftvereket.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés által biztosított hozzáférés Igloo szoftver használatára.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Igloo szoftver, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Igloo szoftver**.

    ![Egyszeri bejelentkezés konfigurálása](./media/igloo-software-tutorial/tutorial_igloosoftware_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Igloo szoftver csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az Igloo szoftverfrissítések alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/igloo-software-tutorial/tutorial_general_01.png
[2]: ./media/igloo-software-tutorial/tutorial_general_02.png
[3]: ./media/igloo-software-tutorial/tutorial_general_03.png
[4]: ./media/igloo-software-tutorial/tutorial_general_04.png

[100]: ./media/igloo-software-tutorial/tutorial_general_100.png

[200]: ./media/igloo-software-tutorial/tutorial_general_200.png
[201]: ./media/igloo-software-tutorial/tutorial_general_201.png
[202]: ./media/igloo-software-tutorial/tutorial_general_202.png
[203]: ./media/igloo-software-tutorial/tutorial_general_203.png

