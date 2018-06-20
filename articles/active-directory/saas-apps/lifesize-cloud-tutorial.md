---
title: 'Oktatóanyag: Azure Active Directory-integráció a Lifesize felhőalapú |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és a Lifesize felhő között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 75fab335-fdcd-4066-b42c-cc738fcb6513
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: jeedes
ms.openlocfilehash: 287204afa7ace9e39507c9dc006549a731b3b992
ms.sourcegitcommit: 16ddc345abd6e10a7a3714f12780958f60d339b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/19/2018
ms.locfileid: "36230386"
---
# <a name="tutorial-azure-active-directory-integration-with-lifesize-cloud"></a>Oktatóanyag: Azure Active Directoryval integrált Lifesize felhő

Ebben az oktatóanyagban elsajátíthatja Lifesize felhő integrálása az Azure Active Directory (Azure AD).

Lifesize felhő integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja az Azure AD, aki hozzáféréssel rendelkezik Lifesize felhőbe
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett Lifesize felhőbe (egyszeri bejelentkezés)
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Lifesize felhőalapú, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy Lifesize felhő egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió kaphat [Itt](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből Lifesize felhő hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-lifesize-cloud-from-the-gallery"></a>A gyűjteményből Lifesize felhő hozzáadása
Az Azure AD integrálása a Lifesize felhő konfigurálásához kell hozzáadnia Lifesize felhőalapú a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből Lifesize felhő hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **Lifesize felhő**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_search.png)

5. Az eredmények panelen válassza ki a **Lifesize felhő**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban konfigurálhatja, és a Lifesize felhőalapú Azure AD az egyszeri bejelentkezés teszt "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó Lifesize felhőben Újdonságok egy felhasználó számára az Azure ad-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó Lifesize felhőben közötti kapcsolat kapcsolatot kell létrehozni.

Lifesize felhőben, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést a Lifesize felhőalapú tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Lifesize felhő tesztfelhasználó létrehozása](#creating-a-lifesize-cloud-test-user)**  – egy megfelelője a Britta Simon rendelkezik, amely csatolva van a felhasználó az Azure AD-ábrázolását Lifesize felhőben.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az Lifesize felhőalapú alkalmazásokhoz.

**Lifesize a felhő konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **Lifesize felhő** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_samlbase.png)

3. Az a **Lifesize felhőalapú tartományt és URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://login.lifesizecloud.com/ls/?acs`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://login.lifesizecloud.com/<companyname>`

     
4. Ellenőrizze **megjelenítése speciális URL-beállításainak**, hajtsa végre a következő lépést:    
   
    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_url1.png)

    Az a **állapot továbbítása** szövegmező, adja meg a következő minta használatával URL-címe: `https://webapp.lifesizecloud.com/?ent=<identifier>`
   
   > [!NOTE] 
   >Ne feledje, hogy ezek nincsenek a valódi értékek. akkor frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím, továbbítási állapotot és azonosítója. Ügyfél [Lifesize felhőalapú ügyfél-támogatási csoport](https://www.lifesize.com/support) bejelentkezési URL-címet, és azonosítóértékek, és lekérheti továbbítási állapotérték SSO-konfiguráció esetén, tekintse meg az oktatóanyag későbbi részében.

4. Az a **SAML-aláíró tanúsítványa** kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_general_400.png)

6. A a **Lifesize Felhőkonfiguráció** kattintson **Lifesize felhő konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_configure.png) 

7. Az alkalmazáshoz, a bejelentkezés a Lifesize felhőalapú alkalmazásnál rendszergazda jogosultságokkal a beállított SSO eléréséhez.

8. A jobb felső sarokban kattintson a nevére, és kattintson a a **speciális beállítások**.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_06.png)

9. A Speciális beállítások most kattintson a a **SSO konfigurációs** hivatkozásra. Az ekkor megnyílik az egyszeri bejelentkezés konfigurálása lapon a példány.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_07.png)

10. A következő értékeket konfigurálhatja a SSO konfigurációs felhasználói felületen.    
   
    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesizecloud_08.png)
    
    a. A **Identity Provider kibocsátó** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** ami Azure-portálon másolta.

    b.  A **bejelentkezési URL-cím** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.

    c. A base-64 kódolású tanúsítvány megnyitása a Jegyzettömbben az Azure portálról letöltött, a tartalmának másolása a vágólapra és illessze be azt a **X.509 tanúsítvány** szövegmező.
  
    d. A SAML attribútum-leképezésekhez Keresztnév szövegmező adja meg az értéket, mint **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname**
    
    e. A SAML attribútum társítását a a **Vezetéknév** szövegmezőbe írja be a értékével megegyező **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname**
    
    f. A SAML attribútum társítását a a **E-mail** szövegmezőbe írja be a értékével megegyező **http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress**

11. Ellenőrizze a konfigurációt, kattintson a **teszt** gombra.
   
    >[!NOTE]
    >A sikeres vizsgálat kell a konfiguráció varázsló az Azure ad-ben, és hozzáférést is biztosít a felhasználókhoz vagy csoportokhoz a tesztet hajthat végre.

12. Engedélyezze az egyszeri bejelentkezés ellenőrzése a következőn: a **SSO engedélyezése** gombra.

13. Most kattintson a a **frissítés** gombra, hogy a beállítások lesznek mentve. Ezzel a RelayState értéket hoz létre. Másolás a RelayState érték, amely a szövegmezőben jön létre, illessze be a **továbbítási állapotot** szövegmező alatt **Lifesize felhőalapú tartományt és URL-címek** szakasz. 

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/lifesize-cloud-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/lifesize-cloud-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/lifesize-cloud-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/lifesize-cloud-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-lifesize-cloud-test-user"></a>Lifesize felhő tesztfelhasználó létrehozása

Ebben a szakaszban egy felhasználó Britta Simon nevű Lifesize felhőben hoz létre. Lifesize felhő támogatja, a felhasználók automatikus átadása. Az Azure AD a sikeres hitelesítést követően a felhasználó automatikusan megkapják az alkalmazásban. 

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Lifesize felhőalapú Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése Lifesize felhő, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **Lifesize felhő**.

    ![Egyszeri bejelentkezés konfigurálása](./media/lifesize-cloud-tutorial/tutorial_lifesize-cloud_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen Lifesize felhő csempére kattint, bejelentkezési oldalán Lifesize felhőalapú alkalmazásnál szerezheti be.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/lifesize-cloud-tutorial/tutorial_general_01.png
[2]: ./media/lifesize-cloud-tutorial/tutorial_general_02.png
[3]: ./media/lifesize-cloud-tutorial/tutorial_general_03.png
[4]: ./media/lifesize-cloud-tutorial/tutorial_general_04.png

[100]: ./media/lifesize-cloud-tutorial/tutorial_general_100.png

[200]: ./media/lifesize-cloud-tutorial/tutorial_general_200.png
[201]: ./media/lifesize-cloud-tutorial/tutorial_general_201.png
[202]: ./media/lifesize-cloud-tutorial/tutorial_general_202.png
[203]: ./media/lifesize-cloud-tutorial/tutorial_general_203.png

