---
title: 'Oktatóanyag: Azure Active Directory-integráció Symantec webes biztonsági szolgáltatás (VSS) |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Symantec a webes biztonsági szolgáltatás (VSS) között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: joflore
ms.assetid: d6e4d893-1f14-4522-ac20-0c73b18c72a5
ms.service: active-directory
ms.component: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/27/2017
ms.author: jeedes
ms.openlocfilehash: b933bc5f5ecb39c3462e4e9bd300f1e07fd718c0
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52838771"
---
# <a name="tutorial-azure-active-directory-integration-with-symantec-web-security-service-wss"></a>Oktatóanyag: Azure Active Directory-integráció Symantec webes biztonsági szolgáltatás (VSS)

Ebben az oktatóanyagban, megtudhatja, hogyan integrálható a Symantec a webes biztonsági szolgáltatás (VSS) fiók az Azure Active Directory (Azure AD-) fiókkal, hogy a WSS hitelesítheti a végfelhasználó az SAML-hitelesítés használata az Azure AD-ben üzembe helyezett és kényszerítése a felhasználói vagy csoport-szintű szabályzat előírásainak.

A Symantec a webes biztonsági szolgáltatás (VSS) integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Kezelheti a végfelhasználók és a csoportok az Azure AD portálon WSS fiókját használják. 

- Lehetővé teszi a végfelhasználók számára, hogy az Azure AD hitelesítő adataikkal WSS hitelesítik magukat.

- Annak érdekében, a felhasználó engedélyezése, és csoportosíthatja a WSS-fiókban megadott szintű szabályzat előírásainak.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása a Symantec webes biztonsági szolgáltatás (VSS), a következőkre van szükség:

- Azure AD-előfizetés
- A Symantec a webes biztonsági szolgáltatás (VSS) fiók

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott a WSS fiókkal, amely a termelési célra használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja a WSS-fiók, amely jelenleg használatban van ebben a tesztben a termelési célra nem szükséges.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban az Azure AD egyszeri bejelentkezés WSS használatával az az Azure AD-fiókot a megadott felhasználói hitelesítő adatokkal való engedélyezéséhez konfigurálja.
Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. A Symantec a webes biztonsági szolgáltatás (VSS) alkalmazás hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-symantec-web-security-service-wss-from-the-gallery"></a>A Symantec a webes biztonsági szolgáltatás (VSS) hozzáadása a katalógusból
Az Azure AD-be a Symantec webes biztonsági szolgáltatás (VSS) integráció konfigurálásához, hozzá kell a Symantec a webes biztonsági szolgáltatás (VSS) a katalógusból a felügyelt SaaS-alkalmazások listájára.

**A Symantec a webes biztonsági szolgáltatás (VSS) hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **Symantec webes biztonsági szolgáltatás (VSS)**, jelölje be **Symantec webes biztonsági szolgáltatás (VSS)** eredmény panelen kattintson a **Hozzáadás** gombra kattintva adhat hozzá a az alkalmazás.

    ![Symantec webes biztonsági szolgáltatás (VSS) a találatok listájában](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Symantec webes biztonsági szolgáltatás (WSS) nevű, "Britta Simon" tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a Symantec webhelyén biztonsági szolgáltatás (WSS) tartozó felhasználó mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó a Symantec webhelyén biztonsági szolgáltatás (WSS) hivatkozás kapcsolata kell létrehozni.

Symantec webes biztonsági szolgáltatás (VSS) a hozzárendelése értékét a **felhasználónév** értékeként az Azure AD-ben a **felhasználónév** a hivatkozás kapcsolat létrehozására.

Az Azure AD egyszeri bejelentkezés Symantec webes biztonsági szolgáltatás (VSS) tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy a Symantec a webes biztonsági szolgáltatás (VSS) tesztfelhasználót](#create-a-symantec-web-security-service-wss-test-user)**  – egy megfelelője a Britta Simon kell a Symantec webes biztonsági szolgáltatás (WSS), amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a Symantec a webes biztonsági szolgáltatás (VSS) alkalmazás egyszeri bejelentkezés konfigurálása.

**Az Azure AD egyszeri bejelentkezés beállítása a Symantec webes biztonsági szolgáltatás (VSS), hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Symantec webes biztonsági szolgáltatás (VSS)** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_samlbase.png)

1. Az a **Symantec webhelyén biztonsági szolgáltatás (VSS) tartomány és URL-címek** szakaszban, hajtsa végre az alábbi lépéseket:

    ![A Symantec a webes biztonsági szolgáltatás (VSS) tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_url.png)

    a. Az a **azonosító** szövegmezőbe írja be az URL-cím: `https://saml.threatpulse.net:8443/saml/saml_realm`

    b. Az a **válasz URL-cím** szövegmezőbe írja be az URL-cím: `https://saml.threatpulse.net:8443/saml/saml_realm/bcsamlpost`

    > [!NOTE]
    > Vegye fel a kapcsolatot a [Symantec webes biztonsági szolgáltatás (VSS) ügyfél-támogatási csapatával](https://www.symantec.com/contact-us) Ha értékeit a **azonosító** és **válasz URL-cím** valamilyen okból nem működnek.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_certificate.png) 

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/symantec-tutorial/tutorial_general_400.png)
    
1. Egyszeri bejelentkezés beállítása a Symantec a webes biztonsági szolgáltatás (VSS) oldalon, tekintse meg a WSS online dokumentációt. A letöltött **metaadatainak XML** fájlt a WSS-portálon importálni kell. Forduljon a [Symantec webes biztonsági szolgáltatás (VSS) támogatási csoportjának](https://www.symantec.com/contact-us) kaphat segítséget a konfigurációval a WSS-portálon.

> [!TIP]
> Ezek az utasítások belül tömör verziója elolvashatja a [az Azure portal](https://portal.azure.com), míg a állítja be az alkalmazás!  Ez az alkalmazás hozzáadása után a **Active Directory > Vállalati alkalmazások** egyszerűen kattintson a **egyszeri bejelentkezés** lapra, és a beágyazott dokumentáció eléréséhez a  **Konfigurációs** alul található szakaszában. Tudjon meg többet a beágyazott dokumentáció szolgáltatásról ide: [Azure ad-ben embedded – dokumentáció]( https://go.microsoft.com/fwlink/?linkid=845985)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/symantec-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/symantec-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/symantec-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/symantec-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.
 
### <a name="create-a-symantec-web-security-service-wss-test-user"></a>A Symantec a webes biztonsági szolgáltatás (VSS) tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon a Symantec webhelyén biztonsági szolgáltatás (WSS) nevű felhasználó létrehozásához. A megfelelő teljes felhasználónevet manuálisan létrehozott a WSS-portálon, vagy várja meg, néhány perc (~ 15 perc) után szinkronizálni kell a WSS-portálon az Azure AD-ben üzembe helyezett felhasználók/csoportok. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva. A végfelhasználói gép, webhelyek, tallózással használandó nyilvános IP-címét is szükség lesz a Symantec a webes biztonsági szolgáltatás (VSS) portálon.

> [!NOTE]
> Adjon [ide](https://www.bing.com/search?q=my+ip+address&qs=AS&pq=my+ip+a&sc=8-7&cvid=29A720C95C78488CA3F9A6BA0B3F98C5&FORM=QBLH&sp=1) a gépet a nyilvános IP-cím.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezés hozzáférést biztosít a Symantec webes biztonsági szolgáltatás (VSS) használja.

![A felhasználói szerepkör hozzárendelése][200] 

**Rendelje hozzá a Britta Simon Symantec webes biztonsági szolgáltatás (VSS), hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201] 

1. Az alkalmazások listájában jelölje ki a **Symantec webes biztonsági szolgáltatás (VSS)**.

    ![A Symantec a webes biztonsági szolgáltatás (VSS) hivatkozásra az alkalmazások listáját](./media/symantec-tutorial/tutorial_symantecwebsecurityservicewss_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni fogja az egyszeri bejelentkezés funkció most, hogy konfigurálta a WSS fiókkal az Azure AD a SAML-hitelesítés.

Miután konfigurálta a proxy-forgalom WSS, a böngészőben nyissa meg a webböngészőjét, és tallózással keresse meg a helyet próbál majd átirányítjuk az Azure bejelentkezési oldala. Adja meg a felhasználó hitelesítő adatait, a tesztelési célból, amelyet az Azure ad (azaz BrittaSimon) és a társított jelszót. A hitelesítést követően lesz a választott webhelyre navigálhat. Érdemes olyan szabályt hoz létre szabályzat BrittaSimon megakadályozza a böngészés egy adott helyre, majd lapnak kell megjelennie a WSS letiltása, tallózással keresse meg a helyet BrittaSimon felhasználóként megkísérlésekor a WSS oldalon.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/symantec-tutorial/tutorial_general_01.png
[2]: ./media/symantec-tutorial/tutorial_general_02.png
[3]: ./media/symantec-tutorial/tutorial_general_03.png
[4]: ./media/symantec-tutorial/tutorial_general_04.png

[100]: ./media/symantec-tutorial/tutorial_general_100.png

[200]: ./media/symantec-tutorial/tutorial_general_200.png
[201]: ./media/symantec-tutorial/tutorial_general_201.png
[202]: ./media/symantec-tutorial/tutorial_general_202.png
[203]: ./media/symantec-tutorial/tutorial_general_203.png

