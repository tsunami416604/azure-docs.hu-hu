---
title: 'Oktatóanyag: Azure Active Directoryval integrált SAP Business ByDesign |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és az SAP Business ByDesign között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: 4bc3998be4cdaa8ba419ad02037b45a1bd3952d2
ms.sourcegitcommit: c851842d113a7078c378d78d94fea8ff5948c337
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2018
ms.locfileid: "35925515"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Oktatóanyag: Azure Active Directory-integráció az SAP Business ByDesign megoldással

Ebben az oktatóanyagban elsajátíthatja SAP Business ByDesign integrálása az Azure Active Directory (Azure AD).

SAP Business ByDesign integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér az SAP Business ByDesign szabályozhatja.
- Az Azure AD-fiókok a engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett az SAP Business ByDesign (egyszeri bejelentkezés).
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az SAP Business ByDesign, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Az SAP Business ByDesign egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. SAP Business ByDesign hozzáadása a gyűjteményből
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>SAP Business ByDesign hozzáadása a gyűjteményből
Az Azure AD integrálása a SAP Business ByDesign konfigurálásához kell hozzáadnia SAP Business ByDesign a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá az SAP Business ByDesign a gyűjteményből, hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]
    
3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **SAP Business ByDesign**, jelölje be **SAP Business ByDesign** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![SAP Business ByDesign az eredménylistában](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD az egyszeri bejelentkezés SAP Business ByDesign "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD meg kell tudja, hogy mi a SAP Business ByDesign tartozó felhasználót a felhasználó Azure AD-ben. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a SAP Business ByDesign közötti kapcsolat kapcsolatot kell létrehozni.

Az SAP Business ByDesign, rendelje az értékét a **felhasználónév** értékeként Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezést az SAP Business ByDesign tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy SAP Business ByDesign tesztfelhasználó](#create-an-sap-business-bydesign-test-user)**  - való egy megfelelője a Britta Simon SAP Business ByDesign, amely csatolva van a felhasználó az Azure AD-ábrázolását.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és az SAP Business ByDesign alkalmazásban egyszeri bejelentkezés konfigurálása.

**Az SAP Business ByDesign konfigurálása az Azure AD egyszeri bejelentkezést, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **SAP Business ByDesign** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.
 
    ![Egyszeri bejelentkezés párbeszédpanel](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_samlbase.png)

3. Az a **SAP Business ByDesign tartomány és az URL-címek** területen tegye a következőket:

    ![Az egyszeri bejelentkezés információk SAP Business ByDesign tartomány és az URL-címek](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_url.png)

    a. Az a **bejelentkezési URL-cím** szövegmező, adja meg a következő minta használatával URL-címe: `https://<servername>.sapbydesign.com`

    b. Az a **azonosító** szövegmező, adja meg a következő minta használatával URL-címe: `https://<servername>.sapbydesign.com`

    > [!NOTE] 
    > Ezek az értékek nincsenek valós. Frissítheti ezeket az értékeket a tényleges bejelentkezési URL-cím és azonosítója. Ügyfél [SAP Business ByDesign ügyfél-támogatási csoport](https://www.sap.com/products/cloud-analytics.support.html) beolvasni ezeket az értékeket.

4. Az a **felhasználói attribútumok** területen tegye a következőket:

    ![SAP Business ByDesign attribútum szakasz](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_attribute.png)
    
    a. A **felhasználói azonosító** listáról válassza ki a **ExtractMailPrefix()** függvény.
    
    b. Az a **Mail** listára, válassza ki a megvalósítás a használni kívánt felhasználói attribútum. Például ha az egyedi felhasználói azonosítóval az EmployeeID használni kívánt, és az attribútum értékét a ExtensionAttribute2 tárolt, majd válassza ki user.extensionattribute2.     

5. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_certificate.png) 

6. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_general_400.png)

7. A a **SAP Business ByDesign konfigurációs** kattintson **SAP Business ByDesign konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML-alapú egyszeri bejelentkezési URL-címe** a a **rövid összefoglaló szakasz.**

    ![SAP Business ByDesign konfiguráció](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_configure.png) 

8. Ahhoz, hogy az alkalmazáshoz konfigurált SSO, hajtsa végre az alábbi lépéseket:
   
    a. Jelentkezzen be a SAP Business ByDesign portál, rendszergazdai jogosultságokkal.
   
    b. Navigáljon a **alkalmazás- és felhasználói gyakori feladatot** , és kattintson a **identitásszolgáltató** fülre.
   
    c. Kattintson a **új identitásszolgáltató** válassza ki az Azure-portálról letöltött metaadatok XML-fájl. Importálja a metaadatokat, a rendszer automatikusan feltölti a szükséges aláírási tanúsítvány és titkosítási tanúsítványt.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)
   
    d. Tartalmazza a **helyességi feltétel ügyfél szolgáltatás URL-címe** válassza ki azokat a SAML-kérelmet **helyességi feltétel fogyasztói szolgáltatás URL-címek**.
   
    e. Kattintson a **egyszeri bejelentkezés aktiválása**.
   
    f. Mentse a módosításokat.
   
    g. Kattintson a **a rendszer** fülre.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)
   
    h. Beillesztés **SAML-alapú egyszeri bejelentkezési URL-címe**, amely másolta az Azure portálról be azt a **Azure AD bejelentkezési URL-címen** szövegmező.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)
   
    i. Adja meg, hogy az alkalmazott manuálisan választhat naplózás a felhasználói Azonosítót és jelszót vagy az SSO kiválasztásával **manuális Identity Provider kijelölés**.
   
    j. Az a **egyszeri bejelentkezési URL-cím** szakaszában, az URL-cím, az alkalmazott ahhoz, hogy a rendszer által használandó. 
    Az URL-cím küldött alkalmazott legördülő listából, a választhat a következő beállításokat:
   
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

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/sapbusinessbydesign-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/sapbusinessbydesign-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/sapbusinessbydesign-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/sapbusinessbydesign-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-sap-business-bydesign-test-user"></a>Az SAP Business ByDesign tesztfelhasználó létrehozása

Ebben a szakaszban egy SAP Business ByDesign Britta Simon nevű felhasználót hoz létre. Adjon együttműködve [SAP Business ByDesign ügyfél-támogatási csoport](https://www.sap.com/products/cloud-analytics.support.html) a felhasználók hozzáadása az SAP Business ByDesign platform. 

> [!NOTE]
> Győződjön meg arról, hogy NameID értéke az a felhasználónév mező az SAP Business ByDesign platform egyezniük kell.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés SAP Business ByDesign Azure egyszeri bejelentkezéshez használandó.

![A felhasználói szerepkör hozzárendelése][200] 

**Az SAP Business ByDesign Britta Simon rendeléséhez hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

2. Az alkalmazások listában válassza ki a **SAP Business ByDesign**.

    ![Az alkalmazások listáját az SAP Business ByDesign hivatkozás](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

Ha a hozzáférési panelen SAP Business ByDesign csempére kattint, akkor kell beolvasása automatikusan bejelentkezett az SAP Business ByDesign alkalmazására.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/sapbusinessbydesign-tutorial/tutorial_general_01.png
[2]: ./media/sapbusinessbydesign-tutorial/tutorial_general_02.png
[3]: ./media/sapbusinessbydesign-tutorial/tutorial_general_03.png
[4]: ./media/sapbusinessbydesign-tutorial/tutorial_general_04.png

[100]: ./media/sapbusinessbydesign-tutorial/tutorial_general_100.png

[200]: ./media/sapbusinessbydesign-tutorial/tutorial_general_200.png
[201]: ./media/sapbusinessbydesign-tutorial/tutorial_general_201.png
[202]: ./media/sapbusinessbydesign-tutorial/tutorial_general_202.png
[203]: ./media/sapbusinessbydesign-tutorial/tutorial_general_203.png

