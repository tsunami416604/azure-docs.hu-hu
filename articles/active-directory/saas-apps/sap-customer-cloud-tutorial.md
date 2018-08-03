---
title: 'Oktatóanyag: Azure Active Directory-integráció az SAP-felhő ügyfél |} A Microsoft Docs'
description: Ismerje meg az egyszeri bejelentkezés az Azure Active Directory és az SAP-felhő között az ügyfél konfigurálása.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.assetid: 90154dab-eba2-4563-bcf0-f2acc797ea97
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/14/2017
ms.author: jeedes
ms.openlocfilehash: 59bbcbf9aaef17394151e7db1471b63b87a46288
ms.sourcegitcommit: 1d850f6cae47261eacdb7604a9f17edc6626ae4b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/02/2018
ms.locfileid: "39445717"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-cloud-for-customer"></a>Oktatóanyag: Azure Active Directory-integráció az SAP-felhő ügyfél

Ebben az oktatóanyagban megismerheti, hogyan integrálható az SAP Cloud az Azure Active Directoryval (Azure AD-) ügyfél.

SAP-felhő integrálása az Azure AD-ügyfél nyújt a következő előnyökkel jár:

- Szabályozhatja, aki hozzáféréssel rendelkezik az SAP-felhő ügyfél Azure AD-ben
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az SAP-felhő ügyfél (egyszeri bejelentkezés) az Azure AD-fiókjukat
- Kezelheti a fiókokat, egyetlen központi helyen – az Azure Portalon

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása az SAP-felhővel ügyfél, a következő elemek van szükség:

- Az Azure AD-előfizetéshez
- Egy ügyfél egyszeri bejelentkezést az SAP-felhő előfizetés engedélyezve van.

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, beszerezheti a ide-egy havi próbalehetőség: [próba](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Vegye fel az SAP-felhő-ügyfél a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-sap-cloud-for-customer-from-the-gallery"></a>Vegye fel az SAP-felhő-ügyfél a katalógusból
Ügyfél az Azure AD-be a SAP felhőalapú integráció konfigurálásához, hozzá kell az SAP Cloud ügyfél a galériából a felügyelt SaaS-alkalmazások listájára.

**Adja hozzá az SAP Cloud ügyfél számára a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a  **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Active Directory][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![Alkalmazások][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Alkalmazások][3]

1. A Keresés mezőbe írja be a **SAP-felhő ügyfél**.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_search.png)

1. Az eredmények panelen válassza ki a **SAP-felhő ügyfél**, és kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_addfromgallery.png)

##  <a name="configuring-and-testing-azure-ad-single-sign-on"></a>Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés
Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SAP-felhővel a teszt "Britta Simon" nevű felhasználó ügyfél számára.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó az ügyfél az SAP Cloud mi egy felhasználó számára az Azure ad-ben. Más szóval Azure AD-felhasználót és a kapcsolódó felhasználó a SAP felhőben ügyfél közötti kapcsolat kapcsolatot kell létrehozni.

SAP-felhő ügyfél, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az SAP-felhővel ügyfél tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configuring-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Az Azure ad-ben tesztfelhasználó létrehozása](#creating-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Ügyfél tesztfelhasználó számára egy SAP-felhő létrehozása](#creating-a-sap-cloud-for-customer-test-user)**  – egy megfelelője a Britta Simon rendelkeznie a SAP felhőben ügyfél, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Az Azure ad-ben tesztfelhasználó hozzárendelése](#assigning-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#testing-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configuring-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az SAP-felhőbeli felhasználói alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés az SAP-felhővel ügyfél, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon a a **SAP-felhő ügyfél** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezés konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_samlbase.png)

1. Az a **SAP-felhő a felhasználói tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<server name>.crm.ondemand.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<server name>.crm.ondemand.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [ügyfél ügyfél-támogatási csapatával a SAP Cloud](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) beolvasni ezeket az értékeket. 

1. Az a **felhasználói attribútumok** szakaszban, hajtsa végre az alábbi lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_attribute.png)

    a. A **felhasználóazonosító** listáról válassza ki a **ExtractMailPrefix()** függvény.

    b. Az a **Mail** listájához, válassza ki a példányhoz használni kívánt felhasználói attribútum.
    Például ha az attribútum értékét a ExtensionAttribute2 tárolt az EmployeeID használandó egyedi felhasználói azonosítóként, majd válassza ki user.extensionattribute2.  

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![Egyszeri bejelentkezés konfigurálása](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés konfigurálása](./media/sap-customer-cloud-tutorial/tutorial_general_400.png)

1. Az a **SAP-felhő a felhasználói konfigurálása** területén kattintson **SAP-felhő konfigurálása ügyfél** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![Egyszeri bejelentkezés konfigurálása](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_configure.png) 

1. Egyszeri bejelentkezés konfigurálva eléréséhez hajtsa végre az alábbi lépéseket:
   
    a. Jelentkezzen be SAP-felhő a felhasználói portál rendszergazda jogosultsággal rendelkező.
   
    b. Keresse meg a **alkalmazás- és felhasználókezelési gyakori feladatot** , és kattintson a **identitásszolgáltató** fülre.
   
    c. Kattintson a **új identitásszolgáltató** , és válassza ki az Azure Portalról letöltött metaadatainak XML-fájl. Importálja a metaadatokat, a rendszer automatikusan feltölti a szükséges aláírási tanúsítvány és a titkosítási tanúsítványt.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_54.png)
   
    d. Az Azure Active Directory gyermekelemet a helyességi feltétel fogyasztói URL-címe az az SAML-kérelmet, ezért válassza a **helyességi feltétel fogyasztói szolgáltatás URL-címek** jelölőnégyzetet.
   
    e. Kattintson a **egyszeri bejelentkezés aktiválása**.
   
    f. Mentse a módosításokat.
   
    g. Kattintson a **saját rendszer** fülre.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_52.png)
   
    h. A **Azure AD bejelentkezési URL-cím** szövegmezőjébe illessze be **SAML egyszeri bejelentkezési szolgáltatás URL-cím** Azure Portalról másolt.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_53.png)
   
    i. Adja meg, hogy az alkalmazott manuálisan választhatnak, hogy naplózás a felhasználói azonosító és jelszó vagy az SSO kiválasztásával a **manuális Identity Provider kijelölés**.
   
    j. Az a **egyszeri bejelentkezési URL-cím** szakaszban adja meg, jelentkezzen be a rendszer az alkalmazottak által használandó URL-CÍMÉT. 
    Az a **alkalmazott küldött URL-cím** listájában, a következő lehetőségek közül választhat:
   
    **Nem egyszeri bejelentkezési URL-címe**
   
    A rendszer csak a normál rendszer URL-CÍMÉT az alkalmazott küld. Az alkalmazott nem jelentkezik be egyszeri Bejelentkezést, és kell jelszó használata vagy a tanúsítvány helyett.
   
    **EGYSZERI BEJELENTKEZÉSI URL-CÍME** 
   
    A rendszer elküldi az alkalmazott csak az egyszeri bejelentkezési URL-címet. Az alkalmazott bejelentkezhet az egyszeri bejelentkezés használatával. Hitelesítési kérelem az identitásszolgáltató keresztül lesz átirányítva.
   
    **Automatikus kiválasztása**
   
    Ha egyszeri bejelentkezés nem aktív, a rendszer az alkalmazott küld a rendszer normál URL-CÍMÉT. Ha egyszeri bejelentkezés aktív, a rendszer ellenőrzi, hogy az alkalmazott rendelkezik-e a jelszó. A jelszó nem érhető el, ha egyszeri bejelentkezési URL-cím és a nem-egyszeri bejelentkezési URL-címet kapnak az alkalmazott. Azonban ha az alkalmazott nem tartozik jelszó, csak az egyszeri bejelentkezési URL-cím küld az alkalmazottnak.
   
    k. Mentse a módosításokat.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="creating-an-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó létrehozása
Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

![Az Azure AD-felhasználó létrehozása][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az a **az Azure portal**, a bal oldali navigációs panelén kattintson **Azure Active Directory** ikonra.

    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sap-customer-cloud-tutorial/create_aaduser_01.png) 

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok** kattintson **minden felhasználó**.
    
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sap-customer-cloud-tutorial/create_aaduser_02.png) 

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** a párbeszédpanel tetején.
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sap-customer-cloud-tutorial/create_aaduser_03.png) 

1. Az a **felhasználói** párbeszédpanel lapon, a következő lépésekkel:
 
    ![Az Azure ad-ben tesztfelhasználó létrehozása](./media/sap-customer-cloud-tutorial/create_aaduser_04.png) 

    a. Az a **neve** szövegmezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** szövegmezőbe írja be a **e-mail-cím** BrittaSimon az.

    c. Válassza ki **jelszó megjelenítése** és jegyezze fel az értékét a **jelszó**.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="creating-a-sap-cloud-for-customer-test-user"></a>Ügyfél tesztfelhasználó számára egy SAP-felhő létrehozása

Ebben a szakaszban egy felhasználói Britta Simon nevű SAP felhőben ügyfél hoz létre. Együttműködve [ügyfélszolgálati csoport az SAP-felhő](https://www.sap.com/about/agreements.sap-cloud-services-customers.html) a felhasználók hozzáadása a SAP felhőben ügyfél platform. 

> [!NOTE]
> Győződjön meg arról, hogy NameID értékének egyeznie kell a felhasználónév mező, a SAP felhőben ügyfél platform.

### <a name="assigning-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használandó Azure egyszeri bejelentkezést az SAP Cloud való hozzáférés biztosítása az ügyfél által.

![Felhasználó hozzárendelése][200] 

**Britta Simon rendel az SAP Cloud ügyfél, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **SAP-felhő ügyfél**.

    ![Egyszeri bejelentkezés konfigurálása](./media/sap-customer-cloud-tutorial/tutorial_sapcloudforcustomer_app.png) 

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![Felhasználó hozzárendelése][202] 

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![Felhasználó hozzárendelése][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="testing-single-sign-on"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha rákattint az SAP-felhő a felhasználói csempe a hozzáférési panelen, kell lekérése automatikusan bejelentkezett a SAP felhőben felhasználói alkalmazás.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../user-help/active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)



<!--Image references-->

[1]: ./media/sap-customer-cloud-tutorial/tutorial_general_01.png
[2]: ./media/sap-customer-cloud-tutorial/tutorial_general_02.png
[3]: ./media/sap-customer-cloud-tutorial/tutorial_general_03.png
[4]: ./media/sap-customer-cloud-tutorial/tutorial_general_04.png

[100]: ./media/sap-customer-cloud-tutorial/tutorial_general_100.png

[200]: ./media/sap-customer-cloud-tutorial/tutorial_general_200.png
[201]: ./media/sap-customer-cloud-tutorial/tutorial_general_201.png
[202]: ./media/sap-customer-cloud-tutorial/tutorial_general_202.png
[203]: ./media/sap-customer-cloud-tutorial/tutorial_general_203.png

