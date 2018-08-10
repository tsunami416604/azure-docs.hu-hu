---
title: 'Oktatóanyag: Azure Active Directory-integráció az Cisco Webex |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Cisco Webex között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/08/2018
ms.author: jeedes
ms.openlocfilehash: 73e20afdcacec76482f8ebf01bf2cef2105912a6
ms.sourcegitcommit: d0ea925701e72755d0b62a903d4334a3980f2149
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2018
ms.locfileid: "40005519"
---
# <a name="tutorial-azure-active-directory-integration-with-cisco-webex"></a>Oktatóanyag: Cisco Webex-Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Cisco Webex integrálása az Azure Active Directory (Azure AD).

Cisco Webex integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá a Cisco Webex Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Cisco Webex az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen--az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Cisco Webex, a következőkre van szükség:

- Azure AD-előfizetés
- Egyetlen Cisco Webex sign-a-kompatibilis előfizetéshez

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket tesztelése éles környezetben használata nem ajánlott.

Ebben az oktatóanyagban a lépéseket teszteléséhez hajtsa végre ezeket a javaslatokat:

- Ne használja éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos ingyenes próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Cisco Webex hozzáadása a katalógusból
2. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="add-cisco-webex-from-the-gallery"></a>Cisco Webex hozzáadása a katalógusból
Az Azure AD integrálása a Cisco Webex konfigurálásához hozzá kell Cisco Webex a galériából a felügyelt SaaS-alkalmazások listájára.

**Cisco Webex hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali panelen válassza ki a **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Lépjen a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
3. Új alkalmazás hozzáadásához válassza a **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

4. A Keresés mezőbe írja be a **Cisco Webex**. 

5. Válassza ki **Cisco Webex** az eredmények panelen. Válassza ki a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![Cisco Webex a találatok listájában](./media/cisco-webex-tutorial/tutorial_ciscowebex_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Cisco Webex egy "Britta Simon." nevű tesztelési felhasználó alapján

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, akik a Cisco Webex tartozó felhasználót, hogy egy felhasználó Azure AD-ben. Más szóval kell létesítenie egy Azure AD-felhasználót és a kapcsolódó felhasználó közötti kapcsolat, a Cisco Webex.

Cisco Webex, adjon az érték **felhasználónév** azonos értéket **felhasználónév** az Azure ad-ben. Most hozott létre a kapcsolatot a két olyan felhasználó között. 

Az Azure AD egyszeri bejelentkezés a Cisco Webex tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. [Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on) ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. [Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
3. [Hozzon létre egy Cisco Webex tesztfelhasználót](#create-a-cisco-webex-test-user) van egy megfelelője a Britta Simon Cisco Webex, amely kapcsolódik az Azure AD felhasználói ábrázolása.
4. [Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user) Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. [Egyszeri bejelentkezés tesztelése](#test-single-sign-on) , hogy működik-e a konfiguráció ellenőrzéséhez.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Cisco Webex alkalmazásban egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés Cisco Webex, tegye a következőket:**

1. Az Azure Portalon az a **Cisco Webex** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

2. Egyszeri bejelentkezéssel, engedélyeznie a **egyszeri bejelentkezési** párbeszédpanel a **mód** legördülő listában válassza **SAML-alapú bejelentkezés**.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/cisco-webex-tutorial/tutorial_ciscowebex_samlbase.png)

3. Egy másik böngészőablakban jelentkezzen be a Cisco Webex vállalati hely rendszergazdaként.

4. Kattintson a **beállítások** bal oldali menü.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial_cisco_webex_10.png)

5. A beállítások lapon görgessen lefelé a a **hitelesítési** területén kattintson **módosítás**.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial_cisco_webex_14.png)

6. Válassza ki **integrálása egy 3. fél identitásszolgáltatót. (Speciális) ** , és folytassa a következő képernyőn.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial_cisco_webex_15.png)

7. Az a **exportálása könyvtár metaadatai** kattintson **metaadatait tartalmazó fájl letöltése** a metaadat-fájl letöltéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial_cisco_webex_16.png)

8. Az Azure Portalon alatt a **Cisco Webex tartomány és URL-címek** szakaszt, és töltse fel a letöltött **szolgáltató metaadatait tartalmazó fájl** és az alkalmazás konfigurálása a következő lépések végrehajtásával:

    a. Kattintson a **metaadatfájl feltöltése**.

    ![Cisco Webex tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/cisco-webex-tutorial/tutorial_ciscowebex_upload.png)

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    ![Cisco Webex tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/cisco-webex-tutorial/tutorial_ciscowebex_uploadconfig.png)

    c. Feltöltés sikeres befejezése után **szolgáltató metaadatait tartalmazó fájl** a **azonosító** és **válasz URL-cím** értékeket automatikusan az első **Cisco Webex Tartomány és URL-címek** szövegmező szakasz alább látható módon:

    ![Cisco Webex tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/cisco-webex-tutorial/tutorial_ciscowebex_url.png)

    d. Az a **bejelentkezési URL-** mezőbe írja be egy URL-CÍMÉT a következő mintának: `https://<SUBDOMAIN>.webex.com/`
     
    > [!NOTE] 
    > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-cím ezeket az értékeket. Kapcsolattartó [Cisco Webex ügyfél-támogatási csapatának](https://www.webex.co.in/support/support-overview.html) beolvasni ezeket az értékeket.

9. Cisco Webex-alkalmazásban a SAML helyességi feltételek tartalmazza az adott attribútumok vár. Konfigurálja a következő attribútumok ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial_ciscowebex_07.png) 

10. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    |  Attribútum neve  | Attribútum értéke |
    | --------------- | -------------------- |    
    |   Keresztnév    | User.givenName |
    |   Vezetéknév    | User.surname |
    |   egyedi azonosítója    | user.mail |

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial_attribute_05.png)
    
    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    d. Kattintson az **OK** gombra.

11. A a **SAML-aláíró tanúsítvány** szakaszban válassza **metaadatainak XML**, majd mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/cisco-webex-tutorial/tutorial_ciscowebex_certificate.png) 

12. Kattintson a **Mentés** gombra.

    ![Az egyszeri bejelentkezés mentési gomb konfigurálása](./media/cisco-webex-tutorial/tutorial_general_400.png)
    
13. A Cisco Webex vállalati hely rendszergazda lapon a fájl böngésző lehetőséget használva keresse meg és az Azure AD-metaadatfájl feltöltésére. Ezután válassza ki **szükséges metaadatokat (biztonságosabb) a hitelesítésszolgáltató által aláírt tanúsítvány** , és folytassa a következő képernyőn. 

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial_cisco_webex_11.png)

14. Válassza ki **SSO-kapcsolat tesztelése**, és a egy új böngészőlapon nyílik meg, amikor hitelesítéshez az Azure AD-bejelentkezés.

15. Lépjen vissza a **Cisco együttműködési Felhőfelügyelet** böngészőlapon. Ha a teszt sikeres volt, válassza ki a **a teszt sikeres volt. Egyszeri bejelentkezés a beállítás engedélyezése** kattintson **mentése**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/cisco-webex-tutorial/create_aaduser_01.png)

2. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/cisco-webex-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanelen jelölje ki **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/cisco-webex-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanel mezőbe az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/cisco-webex-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Létrehozás** gombra.
 
### <a name="create-a-cisco-webex-test-user"></a>Cisco Webex tesztfelhasználó létrehozása

Ez a szakasz célja a Cisco Webex Britta Simon nevű felhasználó létrehozásához. Cisco Webex támogatja a just-in-time kiépítését és a felhasználók automatikus átadása, amely alapértelmezetten engedélyezve van. További részleteket talál [Itt](https://docs.microsoft.com/azure/active-directory/saas-apps/cisco-webex-provisioning-tutorial) konfigurálásának a felhasználók automatikus átadása.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze a felhasználó által a Cisco Webex hozzáférést adna Azure egyszeri bejelentkezés használatára Britta Simon.

![A felhasználói szerepkör hozzárendelése][200] 

**Cisco Webex Britta Simon rendel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése. Következő, nyissa meg a könyvtár nézetre, majd a **vállalati alkalmazások**.  

2. Válassza ki **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

3. Az alkalmazások listájában jelölje ki a **Cisco Webex**.

    ![Az alkalmazások listáját a Cisco Webex hivatkozás](./media/cisco-webex-tutorial/tutorial_ciscowebex_app.png)  

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Válassza ki a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.

    ![A hozzárendelés hozzáadása panel][203]

5. Az a **felhasználók és csoportok** párbeszédpanelen jelölje ki **Britta Simon** a a **felhasználók** listája.

6. Az a **felhasználók és csoportok** párbeszédpanelen kattintson a **kiválasztása** gombra.

7. Válassza ki a **hozzárendelése** gombra a **hozzárendelés hozzáadása** párbeszédpanel bezárásához.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panel segítségével tesztelheti.

A Cisco Webex csempe kiválasztásakor a hozzáférési panelen, automatikusan első bejelentkezett a Cisco Webex-alkalmazásban.

A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési panel használatába](../user-help/active-directory-saas-access-panel-introduction.md). 

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/cisco-webex-tutorial/tutorial_general_01.png
[2]: ./media/cisco-webex-tutorial/tutorial_general_02.png
[3]: ./media/cisco-webex-tutorial/tutorial_general_03.png
[4]: ./media/cisco-webex-tutorial/tutorial_general_04.png

[100]: ./media/cisco-webex-tutorial/tutorial_general_100.png

[200]: ./media/cisco-webex-tutorial/tutorial_general_200.png
[201]: ./media/cisco-webex-tutorial/tutorial_general_201.png
[202]: ./media/cisco-webex-tutorial/tutorial_general_202.png
[203]: ./media/cisco-webex-tutorial/tutorial_general_203.png

