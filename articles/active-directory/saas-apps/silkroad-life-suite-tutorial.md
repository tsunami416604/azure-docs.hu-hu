---
title: 'Oktatóanyag: Azure Active Directory-integráció SilkRoad élettartama Suite |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és SilkRoad élettartama Suite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 3cd92319-7964-41eb-8712-444f5c8b4d15
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: jeedes
ms.openlocfilehash: 58cfd858641263eb0c678203f191451345a0ded9
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35926192"
---
# <a name="tutorial-azure-active-directory-integration-with-silkroad-life-suite"></a>Oktatóanyag: Azure Active Directory-integráció SilkRoad élettartama csomaggal

Ebben az oktatóanyagban elsajátíthatja SilkRoad élettartama Suite integrálása az Azure Active Directory (Azure AD).

SilkRoad élettartama Suite integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér SilkRoad élettartama Suite szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni aláírt a SilkRoad élettartama Suite (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása SilkRoad élettartama csomaggal, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy SilkRoad élettartama Suite egyszeri bejelentkezés engedélyezve van az előfizetésben

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből SilkRoad élettartama csomag hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-silkroad-life-suite-from-the-gallery"></a>A gyűjteményből SilkRoad élettartama csomag hozzáadása
Az Azure AD integrálása a SilkRoad élettartama Suite konfigurálásához kell hozzáadnia SilkRoad élettartama Suite a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből SilkRoad élettartama Suite hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **SilkRoad élettartama Suite**, jelölje be **SilkRoad élettartama Suite** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az eredménylistában SilkRoad élettartama csomag](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés SilkRoad élettartama Suite "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a párjukhoz felhasználó SilkRoad élettartama csomagban található a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó SilkRoad élettartama csomagban található hivatkozás kapcsolatának kell létrehozni.

SilkRoad élettartama Suite, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD az egyszeri bejelentkezés SilkRoad élettartama Suite tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[SilkRoad élettartama Suite tesztfelhasználó létrehozása](#create-a-silkroad-life-suite-test-user)**  - való egy megfelelője a Britta Simon SilkRoad élettartama csomag, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és konfigurálása egyszeri bejelentkezéshez az SilkRoad élettartama Suite alkalmazásban.

**Konfigurálja az Azure AD az egyszeri bejelentkezés SilkRoad élettartama csomaggal, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **SilkRoad élettartama Suite** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_samlbase.png)

3. Az a **SilkRoad élettartama Suite tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk SilkRoad élettartama Suite tartomány és az URL-címek](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_url1.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<subdomain>.silkroad-eng.com/Authentication/`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/SP` |
    | `https://<subdomain>.silkroad.com/Authentication/SP` |

    c. Az a **válasz URL-CÍMEN** szövegmező, adja meg a következő minta használatával URL-címe: 
    | |
    |--|
    | `https://<subdomain>.silkroad-eng.com/Authentication/` |
    | `https://<subdomain>.silkroad.com/Authentication/` |
     
    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges azonosítója, válasz URL-CÍMEN és bejelentkezési URL-cím. Ügyfél [SilkRoad élettartama Suite ügyfél-támogatási csoport](https://www.silkroad.com/locations/) beolvasni ezeket az értékeket. 

4. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_certificate.png) 

5. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/silkroad-life-suite-tutorial/tutorial_general_400.png)
    
6. Az a **SilkRoad élettartama Suite konfigurációs** területen kattintson **SilkRoad élettartama Suite konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **Sign-Out URL-címet, a SAML entitás azonosítója és a SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![SilkRoad élettartama teljes telepítés konfigurálása](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_configure.png) 

7. Bejelentkezés a SilkRoad vállalati webhely rendszergazdaként. 
 
    >[!NOTE] 
    > A Microsoft Azure AD-összevonás konfigurálása az SilkRoad élettartama Suite hitelesítési alkalmazáshoz való hozzáférési, forduljon SilkRoad támogatási vagy az Ön SilkRoad szolgáltatások képviselőjével.

8. Ugrás a **szolgáltató**, és kattintson a **összevonási részletek**. 
   
    ![Az Azure AD-egyszeri bejelentkezés][10]

9. Kattintson a **töltse le az összevonási metaadatok**, majd mentse a metaadatok fájlt a számítógépen.
   
    ![Az Azure AD-egyszeri bejelentkezés][11] 

10. Az a **SilkRoad** alkalmazás, kattintson a **hitelesítési források**.
   
    ![Az Azure AD-egyszeri bejelentkezés][12] 

11. Kattintson a **hitelesítési forrás hozzáadása**. 
   
    ![Az Azure AD-egyszeri bejelentkezés][13] 

12. Az a **hitelesítési forrás hozzáadása** területen tegye a következőket: 
   
    ![Az Azure AD-egyszeri bejelentkezés][14]
  
    a. A **beállítás 2 - metaadatfájl**, kattintson a **Tallózás** feltöltése az Azure-portálról letöltött metaadatfájl.
  
    b. Kattintson a **fájladatok használatával hozzon létre identitásszolgáltató**.

13. Az a **hitelesítési források** kattintson **szerkesztése**. 
    
     ![Az Azure AD-egyszeri bejelentkezés][15] 

14. Az a **szerkesztése hitelesítési forrás** párbeszédpanelen hajtsa végre a következő lépéseket: 
    
     ![Az Azure AD-egyszeri bejelentkezés][16] 

    a. Mint **engedélyezve**, jelölje be **Igen**.

    b. Az a **entityid beállítást** szövegmezőhöz illessze be az értékét **SAML Entitásazonosító** ami Azure-portálon másolta.
   
    c. Az a **IdP leírás** szövegmező, adja meg a konfiguráció leírása (például: *Azure AD SSO*).

    d. Az a **metaadatfájl** szövegmezőhöz feltöltése a **metaadatok** Azure portálról letöltött fájl.
  
    e. Az a **IdP neve** szövegmező, írja be a konfigurációs adott nevét (például: *Azure SP*).
  
    f. Az a **kijelentkezési URL-címe** szövegmezőhöz illessze be az értékét **Sign-Out URL-cím** ami Azure-portálon másolta.

    g. Az a **sign-on URL-címe** szövegmezőhöz illessze be az értékét **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.

    h. Kattintson a **Save** (Mentés) gombra.

15. Tiltsa le a többi hitelesítési forrást. 
    
     ![Az Azure AD-egyszeri bejelentkezés][17]

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/silkroad-life-suite-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/silkroad-life-suite-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/silkroad-life-suite-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/silkroad-life-suite-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-silkroad-life-suite-test-user"></a>SilkRoad élettartama Suite tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon SilkRoad élettartama Suite nevű felhasználót hoz létre. Együttműködve [SilkRoad élettartama Suite ügyfél-támogatási csoport](https://www.silkroad.com/locations/) a felhasználók hozzáadása a SilkRoad élettartama Suite platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt. 

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SilkRoad élettartama Suite Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon hozzárendelése SilkRoad élettartama Suite, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **SilkRoad élettartama Suite**.

    ![Az alkalmazások listáját a SilkRoad élettartama Suite hivatkozás](./media/silkroad-life-suite-tutorial/tutorial_silkroadlifesuite_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen SilkRoad élettartama Suite csempére kattint, meg kell beolvasása automatikusan bejelentkezett az SilkRoad élettartama Suite alkalmazására.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](../active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/silkroad-life-suite-tutorial/tutorial_general_01.png
[2]: ./media/silkroad-life-suite-tutorial/tutorial_general_02.png
[3]: ./media/silkroad-life-suite-tutorial/tutorial_general_03.png
[4]: ./media/silkroad-life-suite-tutorial/tutorial_general_04.png

[100]: ./media/silkroad-life-suite-tutorial/tutorial_general_100.png

[200]: ./media/silkroad-life-suite-tutorial/tutorial_general_200.png
[201]: ./media/silkroad-life-suite-tutorial/tutorial_general_201.png
[202]: ./media/silkroad-life-suite-tutorial/tutorial_general_202.png
[203]: ./media/silkroad-life-suite-tutorial/tutorial_general_203.png
[10]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_06.png
[11]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_07.png
[12]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_08.png
[13]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_09.png
[14]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_10.png
[15]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_11.png
[16]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_12.png
[17]: ./media/silkroad-life-suite-tutorial/tutorial_silkroad_13.png
