---
title: 'Oktatóanyag: Azure Active Directory-integráció a SAP felhőalapú ügyfél |} Microsoft Docs'
description: 'Útmutató: az egyszeri bejelentkezés Azure Active Directory és az SAP-felhők közötti ügyfél konfigurálása.'
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: 18172fb634c369fb4add5fe7c55e5fec08df7670
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/20/2018
ms.locfileid: "34352299"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Oktatóanyag: Azure Active Directory-integráció a SAP felhőalapú ügyfél

Ebben az oktatóanyagban elsajátíthatja SAP felhő integrálása az Azure Active Directoryval (Azure AD-) ügyfél.

SAP felhő ügyfél és az Azure AD integrálása lehetővé teszi a következő előnyöket biztosítja:

- Szabályozhatja az Azure AD, aki hozzáféréssel rendelkezik az SAP felhőbe ügyfél
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett SAP felhőbe (egyszeri bejelentkezés) ügyfél a saját Azure AD-fiókok
- Kezelheti a fiókokat, egy központi helyen – az Azure-portálon

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálhatja az Azure AD-integrációs SAP felhő a felhasználói, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Az ügyfél egyszeri bejelentkezés SAP felhő előfizetés engedélyezve

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, egy hónapos próbaverzió Itt kaphat: [próbaverzió ajánlat](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. SAP felhő hozzáadása az ügyfél a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>SAP felhő hozzáadása az ügyfél a gyűjteményből
SAP felhő integrálása az Azure AD-be ügyfél konfigurálásához kell hozzáadnia SAP felhő ügyfél a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá a SAP felhő ügyfél a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Active Directory][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![Alkalmazások][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Alkalmazások][3]

4. Írja be a keresőmezőbe, **SAP felhő ügyfél**.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_search.png)

5. Az eredmények panelen válassza ki a **SAP felhő ügyfél**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezéshez a SAP felhőalapú ügyfél "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, a partner felhasználó SAP felhőben ügyfél Újdonságok egy felhasználó számára az Azure ad-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó SAP felhőben ügyfél közötti kapcsolat kapcsolatot kell létrehozni.

Ügyfél felhőben SAP, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezéshez a SAP felhőalapú ügyfél tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Az Azure AD tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Az ügyfél tesztfelhasználó SAP felhők létrehozásával](#creating-a-sap-cloud-for-customer-test-user)**  - ügyfél, amely csatolva van a felhasználó az Azure AD-ábrázolását rendelkezik egy megfelelője a Britta Simon SAP felhőben.
4. **[Az Azure AD-teszt felhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és az SAP-felhőben felhasználói alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezést a SAP felhőalapú ügyfél, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **SAP felhő ügyfél** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_samlbase.png)

3. Az a **SAP felhő a felhasználói tartomány és az URL-címek** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<server name>.crm.ondemand.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<server name>.crm.ondemand.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [SAP felhő ügyfél ügyfél-támogatási csoport](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) beolvasni ezeket az értékeket. 

4. Az a **felhasználói attribútumok** területen tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_attribute.png)

    a. A **felhasználói azonosító** listáról válassza ki a **ExtractMailPrefix()** függvény.

    b. Az a **Mail** listára, válassza ki a megvalósítás a használni kívánt felhasználói attribútum.
    Például ha az egyedi felhasználói azonosítóval az EmployeeID használni kívánt, és az attribútum értékét a ExtensionAttribute2 tárolt, majd válassza ki user.extensionattribute2.  

5. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_400.png)

7. A a **SAP felhő a felhasználói konfigurálása** kattintson **SAP felhő konfigurálása a felhasználói** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_configure.png) 

8. Ahhoz, hogy egyszeri Bejelentkezéses konfigurálva, hajtsa végre az alábbi lépéseket:
   
    a. Bejelentkezés SAP felhő a felhasználói portál rendszergazda jogosultságokkal.
   
    b. Keresse meg a **alkalmazás- és felhasználói gyakori feladatot** , és kattintson a **identitásszolgáltató** fülre.
   
    c. Kattintson a **új identitásszolgáltató** válassza ki az Azure-portálról letöltött metaadatok XML-fájl. Importálja a metaadatokat, a rendszer automatikusan feltölti a szükséges aláírási tanúsítvány és titkosítási tanúsítványt.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
    d. Az Azure Active Directory szükséges az elem helyességi feltétel ügyfél szolgáltatás URL-címe a SAML-kérelmet, ezért az a **helyességi feltétel fogyasztói szolgáltatás URL-címek** jelölőnégyzetet.
   
    e. Kattintson a **egyszeri bejelentkezés aktiválása**.
   
    f. Mentse a módosításokat.
   
    g. Kattintson a **a rendszer** fülre.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
    h. A **Azure AD bejelentkezési URL-címen** szövegmezőhöz Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe** ami Azure-portálon másolta.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
    i. Adja meg, hogy az alkalmazott manuálisan választhat naplózás a felhasználói Azonosítót és jelszót vagy az SSO kiválasztásával a **manuális Identity Provider kijelölés**.
   
    j. Az a **egyszeri bejelentkezési URL-cím** szakaszban adja meg, jelentkezzen be a rendszer az alkalmazottak által használandó URL-CÍMÉT. 
    Az a **URL-cím küldött alkalmazott** listájában, a következő lehetőségek közül választhat:
   
    **Nem egyszeri bejelentkezési URL-címe**
   
    A rendszer küld a dolgozó csak a normál rendszer URL-cím. Az alkalmazott nem jelentkezik be egyszeri Bejelentkezést, és kell jelszó használata vagy a tanúsítvány helyette.
   
    **EGYSZERI BEJELENTKEZÉSI URL-CÍME** 
   
    A rendszer küld a dolgozó csak az egyszeri bejelentkezési URL-címet. Az alkalmazottak az SSO használatával jelentkezhetnek. Hitelesítési kérelmet a rendszer átirányítja az IdP keresztül.
   
    **Automatikus kiválasztásához.**
   
    Ha egyszeri Bejelentkezéssel nem aktív, a rendszer küld a normál rendszer URL-címet az alkalmazott. Ha egyszeri bejelentkezés aktív, a rendszer ellenőrzi, hogy az alkalmazottnak a jelszót. A jelszó nem érhető el, ha mind SSO és URL-címe nem-SSO kerülnek az alkalmazott. Azonban ha az alkalmazott nincs jelszava, csak az egyszeri bejelentkezési URL-címet kap az alkalmazott.
   
    k. Mentse a módosításokat.

> [!TIP]
> Ezek az utasítások belül tömör verziója most el tudja olvasni a [Azure-portálon](https://portal.azure.com), míg az alkalmazás beállításakor!  Ez az alkalmazás a hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentációja keresztül a **konfigurációs** szakasz alján. További Itt a embedded dokumentációjából szolgáltatásról: [az Azure AD beágyazott dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure AD tesztfelhasználó létrehozása
Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **Azure-portálon**, a bal oldali navigációs ablaktábláján kattintson **Azure Active Directory** ikonra.

    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_01.png) 

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_02.png) 

3. Lehetőségre a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** párbeszédpanel tetején.
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_03.png) 

4. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure AD tesztfelhasználó létrehozása](./media/active-directory-saas-sap-customer-cloud-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőhöz típus **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőhöz típusa a **e-mail cím** a BrittaSimon.

    c. Válassza ki **megjelenítése jelszó** írja le a értékének a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-sap-cloud-for-customer-test-user"></a>SAP felhő a felhasználói tesztfelhasználó létrehozása

Ebben a szakaszban egy SAP felhő Britta Simon nevű ügyfél felhasználó hoz létre. Adjon együttműködve [SAP felhő a felhasználói támogatási csoport](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) a felhasználók hozzáadása az SAP felhőben ügyfél platform. 

> [!NOTE]
> Győződjön meg arról, hogy NameID értéke az a felhasználónév mező a SAP felhőben ügyfél platform egyezniük kell.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SAP felhő ügyfél Azure egyszeri bejelentkezéshez használandó.

![Felhasználó hozzárendelése][200] 

**Britta Simon hozzárendelése SAP felhő ügyfél, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **SAP felhő ügyfél**.

    ![Egyszeri bejelentkezés konfigurálása](./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_app.png) 

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a felhasználói csempe a hozzáférési Panel az SAP felhő gombra kattint, meg kell beolvasása automatikusan bejelentkezett az SAP-felhőhöz felhasználói alkalmazás.
A hozzáférési Panel kapcsolatos további információkért lásd: [a hozzáférési Panel bemutatása](active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](active-directory-saas-tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_01.png
[2]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_02.png
[3]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_03.png
[4]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_04.png

[100]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_100.png

[200]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_200.png
[201]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_201.png
[202]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_202.png
[203]: ./media/active-directory-saas-sap-customer-cloud-tutorial/tutorial_general_203.png

