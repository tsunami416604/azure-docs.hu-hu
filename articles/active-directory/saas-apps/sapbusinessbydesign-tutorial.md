---
title: 'Oktatóanyag: Az Azure Active Directory-integráció az SAP Business Bydesignnal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és az SAP Business bydesign megoldással között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: joflore
ms.assetid: 82938920-33ba-47cb-b141-511b46d19e66
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/25/2017
ms.author: jeedes
ms.openlocfilehash: 62a448c5a0994dc549d06808c6ece520eabefc3e
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55193318"
---
# <a name="tutorial-azure-active-directory-integration-with-sap-business-bydesign"></a>Oktatóanyag: Az Azure Active Directory-integráció az SAP Business Bydesignnal

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható az SAP Business bydesign megoldással az Azure Active Directoryval (Azure AD).

SAP Business bydesign megoldással integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá az SAP Business bydesign megoldással az Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett az SAP Business bydesign megoldással (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció az SAP Business Bydesignnal konfigurálásához lesz szüksége a következő elemek:

- Azure AD-előfizetés
- Az SAP Business bydesign megoldással egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. SAP Business bydesign megoldással hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-sap-business-bydesign-from-the-gallery"></a>SAP Business bydesign megoldással hozzáadása a katalógusból
Konfigurálja az integráció az SAP Business bydesign megoldással az Azure AD-be, szüksége a katalógus az SAP Business bydesign megoldással hozzáadása a felügyelt SaaS-alkalmazások listájában.

**SAP Business bydesign megoldással hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **SAP Business bydesign megoldással**válassza **SAP Business bydesign megoldással** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![SAP Business bydesign megoldással a találatok listájában](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az SAP Business Bydesignnal a teszt "Britta Simon" nevű felhasználó.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó, az SAP Business bydesign megoldással mi egy felhasználó számára az Azure ad-ben. Más szóval Azure AD-felhasználót és az SAP Business bydesign megoldással a kapcsolódó felhasználó közötti kapcsolat kapcsolatot kell létrehozni.

SAP Business bydesign megoldással, rendelje hozzá az értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés az SAP Business Bydesignnal tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy SAP Business bydesign megoldással tesztfelhasználót](#create-an-sap-business-bydesign-test-user)**  - a-megfelelője a Britta Simon rendelkezik az SAP Business bydesign megoldással, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és egyszeri bejelentkezést az SAP Business bydesign megoldással alkalmazásban konfigurálja.

**Konfigurálja az Azure AD egyszeri bejelentkezés az SAP Business Bydesignnal, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **SAP Business bydesign megoldással** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_samlbase.png)

1. Az a **SAP Business bydesign megoldással tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SAP Business bydesign megoldással tartomány és URL-címek egyszeri bejelentkezési adatait](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_url.png)

    a. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<servername>.sapbydesign.com`

    b. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<servername>.sapbydesign.com`

    > [!NOTE] 
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges bejelentkezési URL- és azonosító. Kapcsolattartó [SAP Business bydesign megoldással ügyfél-támogatási csapatának](https://www.sap.com/products/cloud-analytics.support.html) beolvasni ezeket az értékeket.

1. Az a **felhasználói attribútumok** szakaszban, hajtsa végre az alábbi lépéseket:

    ![SAP Business bydesign megoldással attribútum szakasz](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_attribute.png)
    
    a. A **felhasználóazonosító** listáról válassza ki a **ExtractMailPrefix()** függvény.
    
    b. Az a **Mail** listájához, válassza ki a példányhoz használni kívánt felhasználói attribútum. Például ha az attribútum értékét a ExtensionAttribute2 tárolt az EmployeeID használandó egyedi felhasználói azonosítóként, majd válassza ki user.extensionattribute2.     

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_general_400.png)

1. Az a **SAP Business bydesign megoldással konfigurációs** területén kattintson **konfigurálja az SAP Business bydesign megoldással** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**

    ![SAP Business bydesign megoldással konfiguráció](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_configure.png) 

1. Egyszeri bejelentkezés konfigurálva az alkalmazás eléréséhez hajtsa végre az alábbi lépéseket:
   
    a. Jelentkezzen be rendszergazdai jogosultságokkal az SAP Business bydesign megoldással portálon.
   
    b. Navigáljon a **alkalmazás- és felhasználókezelési gyakori feladatot** , és kattintson a **identitásszolgáltató** fülre.
   
    c. Kattintson a **új identitásszolgáltató** , és válassza ki az Azure Portalról letöltött metaadatainak XML-fájl. Importálja a metaadatokat, a rendszer automatikusan feltölti a szükséges aláírási tanúsítvány és a titkosítási tanúsítványt.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_54.png)
   
    d. Tartalmazza a **helyességi feltétel fogyasztói URL-címe** válassza ki azokat az SAML-kérelmet **helyességi feltétel fogyasztói szolgáltatás URL-címek**.
   
    e. Kattintson a **egyszeri bejelentkezés aktiválása**.
   
    f. Mentse a módosításokat.
   
    g. Kattintson a **saját rendszer** fülre.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_52.png)
   
    h. Beillesztés **SAML egyszeri bejelentkezési szolgáltatás URL-cím**, amely másolta az Azure Portalról be azt a **Azure AD bejelentkezési URL-cím** szövegmezőbe.
   
    ![Egyszeri bejelentkezés konfigurálása](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_53.png)
   
    i. Adja meg, hogy az alkalmazott manuálisan választhatnak, hogy naplózás a felhasználói azonosító és jelszó vagy az SSO kiválasztásával **manuális Identity Provider kijelölés**.
   
    j. Az a **egyszeri bejelentkezési URL-cím** szakaszban adja meg az alkalmazott ahhoz, hogy a rendszer által használt URL-CÍMÉT. 
    Az az URL-cím küldött alkalmazott legördülő listából választhat a következő beállításokat:
   
    **Nem egyszeri bejelentkezési URL-címe**
   
    A rendszer csak a normál rendszer URL-CÍMÉT az alkalmazott küld. Az alkalmazott nem jelentkezik be egyszeri Bejelentkezést, és kell jelszó használata vagy a tanúsítvány helyett.
   
    **EGYSZERI BEJELENTKEZÉSI URL-CÍME** 
   
    A rendszer elküldi az alkalmazott csak az egyszeri bejelentkezési URL-címet. Az alkalmazott bejelentkezhet az egyszeri bejelentkezés használatával. Hitelesítési kérelem az identitásszolgáltató keresztül lesz átirányítva.
   
    **Automatikus kiválasztása**
   
    Ha egyszeri bejelentkezés nem aktív, a rendszer az alkalmazott küld a rendszer normál URL-CÍMÉT. Ha egyszeri bejelentkezés aktív, a rendszer ellenőrzi, hogy az alkalmazott rendelkezik-e a jelszó. A jelszó nem érhető el, ha egyszeri bejelentkezési URL-cím és a nem-egyszeri bejelentkezési URL-címet kapnak az alkalmazott. Azonban ha az alkalmazott nem tartozik jelszó, csak az egyszeri bejelentkezési URL-cím küld az alkalmazottnak.
   
    k. Mentse a módosításokat.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Az Azure AD embedded dokumentációja]( https://go.microsoft.com/fwlink/?linkid=845985)
> 

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/sapbusinessbydesign-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/sapbusinessbydesign-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/sapbusinessbydesign-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/sapbusinessbydesign-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-an-sap-business-bydesign-test-user"></a>Az SAP Business bydesign megoldással tesztfelhasználó létrehozása

Ebben a szakaszban egy SAP Business bydesign megoldással Britta Simon nevű felhasználói hoz létre. Együttműködve [SAP Business bydesign megoldással ügyfél-támogatási csapatának](https://www.sap.com/products/cloud-analytics.support.html) a felhasználók hozzáadása az SAP Business bydesign megoldással platformon. 

> [!NOTE]
> Győződjön meg arról, hogy NameID értékének egyeznie kell a felhasználónév mező az SAP Business bydesign megoldással platformon.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezést az SAP Business bydesign megoldással való hozzáférés biztosítása használja.

![A felhasználói szerepkör hozzárendelése][200] 

**Britta Simon rendel az SAP Business bydesign megoldással, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **SAP Business bydesign megoldással**.

    ![Az alkalmazások listáját az SAP Business bydesign megoldással hivatkozásra](./media/sapbusinessbydesign-tutorial/tutorial_sapbusinessbydesign_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen az SAP Business bydesign megoldással csempére kattint, akkor kell lekérése automatikusan bejelentkezett az SAP Business bydesign megoldással alkalmazáshoz.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

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

