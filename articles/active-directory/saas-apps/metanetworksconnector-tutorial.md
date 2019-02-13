---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Meta hálózatok összekötővel |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Meta hálózatok összekötő és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: 4ae5f30d-113b-4261-b474-47ffbac08bf7
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3c4d702a33742da5501be208154cbdd593fa0524
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56199711"
---
# <a name="tutorial-azure-active-directory-integration-with-meta-networks-connector"></a>Oktatóanyag: Meta hálózatok összekötő az Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan Meta hálózatok összekötő integrálása az Azure Active Directory (Azure AD).

Meta hálózatok összekötő integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá Meta hálózatok összekötő Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett Meta hálózatok összekötőhöz (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása Meta hálózatok összekötővel, a következő elemek szükségesek:

- Azure AD-előfizetés
- Egy Meta hálózatok összekötő egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. Meta hálózatok összekötő hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-meta-networks-connector-from-the-gallery"></a>Meta hálózatok összekötő hozzáadása a katalógusból
Meta hálózatok összekötő integrálása az Azure AD beállításához szüksége Meta hálózatok összekötő hozzáadása a felügyelt SaaS-alkalmazások listájában a katalógusból.

**Meta hálózatok összekötő hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 
    
    ![Az Azure Active Directory gomb][1]
    
1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.
    
    ![A vállalati alkalmazások panelen][2]
    
1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.
    
    ![Az új alkalmazás gomb][3]
    
1. A Keresés mezőbe írja be a **Meta hálózatok összekötő**, jelölje be **Meta hálózatok összekötő** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.
    
    ![Meta hálózatok összekötő a találatok listájában](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés Meta hálózatok összekötő "Britta Simon" nevű tesztfelhasználó alapján.

Egyszeri bejelentkezés működjön, az Azure ad-ben tudnia kell, a partner felhasználó Meta hálózatok összekötőben mi egy felhasználó számára az Azure ad-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó Meta hálózatok összekötőben hivatkozás kapcsolata kell létrehozni.

Az Azure AD egyszeri bejelentkezés Meta hálózatok összekötő tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy Meta hálózatok összekötő tesztfelhasználót](#create-a-meta-networks-connector-test-user)**  – egy megfelelője a Britta Simon Meta hálózatok összekötő, amely a felhasználó Azure ad-ben ábrázolása van csatolva van.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és az Adattisztítás hálózatokhoz csatlakozó alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálja az Azure AD egyszeri bejelentkezés Meta hálózatok összekötővel, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **Meta hálózatok összekötő** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.
 
    ![Egyszeri bejelentkezési párbeszédpanel](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_samlbase.png)

1. Az a **Meta hálózatok összekötő tartomány és URL-címek** területén kövesse az alábbi lépéseket, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód:

    ![Meta hálózatok összekötő tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url.png)

    1. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-címe: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/saml/metadata`
    
    1. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-címe: `https://login.nsof.io/v1/<ORGANIZATION-SHORT-NAME>/sso/saml`
    
1. Ellenőrizze **speciális URL-beállítások megjelenítése** , és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![Meta hálózatok összekötő tartomány és URL-címeket egyetlen bejelentkezési adatait](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_url1.png)

    1. Az a **bejelentkezési URL-** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/login`
    
    1. Az a **továbbítási állapot** szövegmezőbe írja be a következő minta használatával URL-címe: `https://<ORGANIZATION-SHORT-NAME>.metanetworks.com/#/`
    
    > [!NOTE]
    > Ezek a értékei nem valódi. Frissítheti ezeket az értékeket a tényleges azonosítóval, válasz URL-cím, és a bejelentkezési URL-magyarázatát olvashatja az oktatóanyag későbbi részében.
    
1. Meta hálózatok összekötő alkalmazást a SAML helyességi feltételek vár egy megadott formátumban. Konfigurálja a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a **felhasználói attribútumok** szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_attribute.png)
    
1. Az a **felhasználói attribútumok** szakaszában a **egyszeri bejelentkezési** párbeszédpanelen konfigurálja a SAML-jogkivonat attribútum, a fenti képen látható módon, és hajtsa végre az alábbi lépéseket:
    
    | Attribútum neve | Attribútum értéke | NÉVTÉR|
    | ---------------| --------------- | -------- |
    | Keresztnév | user.givenname | |
    | Vezetéknév | user.surname | |
    | e-mail cím| user.mail| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | név | user.userprincipalname| `http://schemas.xmlsoap.org/ws/2005/05/identity/claims` |
    | telefon | user.telephonenumber | |

    1. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

        ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/tutorial_attribute_04.png)
    
        ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/tutorial_attribute_05.png)   
    
    1. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.
    
    1. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.
    
    1. Az a **Namespace** szövegmezőbe írja be a névtér értéke a sorhoz látható.
    
    1. Kattintson a **Ok**
    
1. Az a **SAML-aláíró tanúsítvány** területén kattintson **Certificate(Base64)** , és mentse a tanúsítványfájlt, a számítógépen.
    
    ![A tanúsítvány letöltési hivatkozás](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_certificate.png)
    
1. Az a **Meta hálózatok összekötő-konfiguráció** területén kattintson **Meta hálózatok Connector konfigurálása** megnyitásához **bejelentkezés konfigurálása** ablak. Másolás a **SAML Entitásazonosító és SAML egyszeri bejelentkezési szolgáltatás URL-cím** származó a **gyors útmutató szakaszban.**
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_configure.png)
    
1. Kattintson a **mentése** gombra.
    
    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/metanetworksconnector-tutorial/tutorial_general_400.png)
    
1. Nyisson meg egy új lapot a böngészőben, és jelentkezzen be a Meta hálózatok összekötő rendszergazdai fiókjával.
    
    > [!NOTE]
    > Meta hálózatok összekötő egy olyan biztonságos rendszer. Ezért a portál elérése előtt le szeretné kérni a nyilvános IP-cím engedélyezési listán az oldalon. A nyilvános IP-cím lekéréséhez kövesse az alábbi hivatkozással megadott [Itt](https://whatismyipaddress.com/). Az IP-cím küldése az [Meta hálózatokhoz csatlakozó ügyfél-támogatási csapatának](mailto:support@metanetworks.com) beolvasni az IP-cím engedélyezési listán.
    
1. Lépjen a **rendszergazda** válassza **beállítások**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure3.png)
    
1. Győződjön meg arról, hogy **Log internetes forgalom** és **kényszerített VPN MFA** vannak beállítva, hogy ki.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure1.png)
    
1. Lépjen a **rendszergazda** válassza **SAML**.
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure4.png)
    
1. Hajtsa végre a következő lépéseket a **részletek** oldalon:
    
    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure2.png)
    
    1. Másolás **egyszeri bejelentkezési URL-cím** értékét, és illessze be azt a **bejelentkezési URL** szövegmezőjébe a **Meta hálózatok összekötő tartomány és URL-címek** szakaszban.
    
    1. Másolás **címzett URL-cím** értékét, és illessze be azt a **válasz URL-cím** szövegmezőjébe a **Meta hálózatok összekötő tartomány és URL-címek** szakaszban.
    
    1. Másolás **célközönség-URI (SP entitás azonosítója)** értékét, és illessze be azt a **azonosító (entityid)** szövegmezőjébe a **Meta hálózatok összekötő tartomány és URL-címek** szakaszban.
    
    1. Az SAML engedélyezése
    
1. Az a **általános** lapon tegye a következőket:

    ![Egyszeri bejelentkezés konfigurálása](./media/metanetworksconnector-tutorial/configure5.png)

    1. Az a **identitás szolgáltató egyszeri bejelentkezési URL-cím**, illessze be a **SAML egyszeri bejelentkezési szolgáltatás URL-cím** az Azure Portalról másolt érték.

    1. Az a **identitásszolgáltató kibocsátója**, illessze be a **SAML Entitásazonosító** az Azure Portalról másolt érték.

    1. Nyissa meg a letöltött tanúsítvány az Azure Portalról a Jegyzettömbben, illessze be azt a **X.509-tanúsítvány** szövegmezőbe.

    1. Engedélyezze a **Just-in-Time-kiépítés**.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.
    
![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]
    
**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.
    
    ![Az Azure Active Directory gomb](./media/metanetworksconnector-tutorial/create_aaduser_01.png)
    
1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.
    
    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/metanetworksconnector-tutorial/create_aaduser_02.png)
    
1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.
    
    ![A Hozzáadás gombra.](./media/metanetworksconnector-tutorial/create_aaduser_03.png)
    
1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/metanetworksconnector-tutorial/create_aaduser_04.png)
    
    1. Az a **neve** mezőbe írja be **BrittaSimon**.

    1. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.
    
    1. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
    
    1. Kattintson a **Create** (Létrehozás) gombra.
    
### <a name="create-a-meta-networks-connector-test-user"></a>Meta hálózatok összekötő tesztfelhasználó létrehozása

Ez a szakasz célja egy Meta hálózatok összekötőben Britta Simon nevű felhasználó létrehozásához. Meta hálózatok összekötő támogatja az just-in-time-kiépítés, amely alapértelmezés szerint van engedélyezve. Nincs meg ebben a szakaszban a művelet elem. Új felhasználó jön létre az Meta hálózatok összekötő elérésére, ha még nem létezik tett kísérlet során.

>[!Note]
>Ha manuálisan hozzon létre egy felhasználót van szüksége, forduljon a [Meta hálózatokhoz csatlakozó ügyfél-támogatási csapatának](mailto:support@metanetworks.com).

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Meta hálózatok összekötő Azure egyszeri bejelentkezés használatára.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon rendel Meta hálózatok összekötő, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.
    
    ![Felhasználó hozzárendelése][201]
    
1. Az alkalmazások listájában jelölje ki a **Meta hálózatok összekötő**.
    
    ![Az alkalmazások listáját a Meta hálózatok összekötő hivatkozás](./media/metanetworksconnector-tutorial/tutorial_metanetworksconnector_app.png)  
    
1. A bal oldali menüben kattintson **felhasználók és csoportok**.
    
    ![A "Felhasználók és csoportok" hivatkozásra][202]
    
1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.
    
    ![A hozzárendelés hozzáadása panel][203]
    
1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.
    
1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.
    
1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.
    
### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Meta hálózatok összekötő csempére kattint, meg kell lekérése automatikusan bejelentkezett az Adattisztítás hálózatok Connector alkalmazáshoz.
A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](../active-directory-saas-access-panel-introduction.md).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/metanetworksconnector-tutorial/tutorial_general_01.png
[2]: ./media/metanetworksconnector-tutorial/tutorial_general_02.png
[3]: ./media/metanetworksconnector-tutorial/tutorial_general_03.png
[4]: ./media/metanetworksconnector-tutorial/tutorial_general_04.png

[100]: ./media/metanetworksconnector-tutorial/tutorial_general_100.png

[200]: ./media/metanetworksconnector-tutorial/tutorial_general_200.png
[201]: ./media/metanetworksconnector-tutorial/tutorial_general_201.png
[202]: ./media/metanetworksconnector-tutorial/tutorial_general_202.png
[203]: ./media/metanetworksconnector-tutorial/tutorial_general_203.png

