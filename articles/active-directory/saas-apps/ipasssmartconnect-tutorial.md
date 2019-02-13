---
title: 'Oktatóanyag: Az Azure Active Directory-integrációval rendelkező iPass SmartConnect |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és iPass SmartConnect között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: femila
ms.reviewer: joflore
ms.assetid: dee6d039-f9bb-49a2-a408-5ed40ef17d9f
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/20/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 91a68a208496904fcc8bfe13a227c61bf313214f
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56198167"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Oktatóanyag: Az Azure Active Directory-integrációval rendelkező iPass SmartConnect

Ebben az oktatóanyagban elsajátíthatja, hogyan iPass SmartConnect integrálása az Azure Active Directory (Azure AD).

IPass SmartConnect integrálása az Azure ad-ben nyújt a következő előnyökkel jár:

- Szabályozhatja, ki férhet hozzá iPass SmartConnect Azure AD-ben.
- Engedélyezheti a felhasználóknak, hogy automatikusan első bejelentkezett a iPass SmartConnect (egyszeri bejelentkezés) az Azure AD-fiókjukat.
- A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD-integráció konfigurálása iPass SmartConnect, a következőkre van szükség:

- Azure AD-előfizetés
- Egy iPass SmartConnect egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ebben az oktatóanyagban a lépéseket teszteléséhez nem ajánlott éles környezetben használja.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, csak szükség esetén.
- Ha nem rendelkezik egy Azure ad-ben a próbakörnyezet, [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelni az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben. Az ebben az oktatóanyagban ismertetett forgatókönyvben két fő építőelemeket áll:

1. IPass SmartConnect hozzáadása a katalógusból
1. Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>IPass SmartConnect hozzáadása a katalógusból
Az Azure AD integrálása a iPass SmartConnect konfigurálásához hozzá kell iPass SmartConnect a katalógusból a felügyelt SaaS-alkalmazások listájára.

**IPass SmartConnect hozzáadása a katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

1. Navigáljon a **vállalati alkalmazások**. Ezután lépjen a **minden alkalmazás**.

    ![A vállalati alkalmazások panelen][2]

1. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb][3]

1. A Keresés mezőbe írja be a **iPass SmartConnect**, jelölje be **iPass SmartConnect** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![az eredmények listájában SmartConnect iPass](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés az iPass SmartConnect egy "Britta Simon" nevű tesztelési felhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD tudnia kell, milyen a partner felhasználó iPass SmartConnect, hogy egy felhasználó Azure AD-ben. Más szóval egy Azure AD-felhasználót és a kapcsolódó felhasználó iPass hivatkozás kapcsolata SmartConnect kell létrehozni.

Az Azure AD egyszeri bejelentkezés az iPass SmartConnect tesztelése és konfigurálása, hogy hajtsa végre a következő építőelemeit kell:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
1. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
1. **[Hozzon létre egy iPass SmartConnect tesztfelhasználót](#create-an-ipass-smartconnect-test-user)**  – egy megfelelője a Britta Simon iPass SmartConnect, amely kapcsolódik az Azure AD felhasználói ábrázolása a rendelkeznie.
1. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
1. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon, és a iPass SmartConnect alkalmazás egyszeri bejelentkezés konfigurálása.

**Szeretné konfigurálni az Azure AD egyszeri bejelentkezés iPass SmartConnect, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon az a **iPass SmartConnect** alkalmazás integrációs oldalán kattintson a **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása][4]

1. Az a **egyszeri bejelentkezési** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezéséhez.

    ![Egyszeri bejelentkezési párbeszédpanel](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_samlbase.png)

1. Az a **iPass SmartConnect tartomány és URL-címek** szakaszra, ha az alkalmazás a konfigurálni kívánt **Identitásszolgáltató** kezdeményezett mód, végrehajthatja a lépéseket nem szükséges.

    ![iPass SmartConnect tartomány és URL-címek egyszeri bejelentkezési adatait](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url1.png)

1. Ellenőrizze a speciális URL-beállítások megjelenítése, és hajtsa végre a következő lépést, ha az alkalmazás a konfigurálni kívánt **SP** kezdeményezett mód:

    ![iPass SmartConnect tartomány és URL-címek egyszeri bejelentkezési adatait](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url2.png)

    A bejelentkezési URL-címe szövegmezőbe írja be egy URL-címe: `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

1. iPass SmartConnect alkalmazás a SAML helyességi feltételek vár egy megadott formátumban. Állítsa be a következő jogcímek ehhez az alkalmazáshoz. Ezek az attribútumok értékeinek kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható erre egy példa látható.

    ![Egyszeri bejelentkezés konfigurálása](./media/ipasssmartconnect-tutorial/attribute.png)

1. Kattintson a **megtekintése és egyéb felhasználói attribútumok szerkesztése** a jelölőnégyzetet a **felhasználói attribútumok** szakaszban bontsa ki az attribútumokat. Hajtsa végre az alábbi lépéseket az egyes a megjelenített attribútumok-

    | Attribútum neve | Attribútum értéke | Namespace érték|
    | ---------------| --------------- |----------------|
    | Keresztnév | user.givenname |   |
    | Vezetéknév | user.surname | |
    | e-mail | user.userprincipalname | |
    | felhasználónév | user.userprincipalname | |

    a. Kattintson a **attribútum hozzáadása** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/ipasssmartconnect-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/ipasssmartconnect-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmezőbe írja be azon attribútum nevét, a sorhoz látható.

    c. Az a **érték** list, írja be az adott sorhoz feltüntetett attribútumot értéket.

    d. Hagyja üresen a mezőt a sorhoz névtér értéke.

    e. Kattintson az **OK** gombra.

1. Az a **SAML-aláíró tanúsítvány** területén kattintson **metaadatainak XML** , és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_certificate.png)

1. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gomb konfigurálása](./media/ipasssmartconnect-tutorial/tutorial_general_400.png)

1. Az egyszeri bejelentkezés konfigurálása **iPass SmartConnect** oldalon kell küldenie a letöltött **metaadatainak XML** és a **tartománynév** való [iPass SmartConnect támogatási csoport](mailto:help@ipass.com). Akkor állítsa ezt a beállítást, hogy a SAML SSO-kapcsolat megfelelően állítsa be mindkét oldalon.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

   ![Hozzon létre egy Azure ad-ben tesztfelhasználó számára][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon, a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/ipasssmartconnect-tutorial/create_aaduser_01.png)

1. A felhasználók listájának megjelenítéséhez, lépjen a **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/ipasssmartconnect-tutorial/create_aaduser_02.png)

1. Megnyitásához a **felhasználói** párbeszédpanelen kattintson a **Hozzáadás** felső részén a **minden felhasználó** párbeszédpanel bezárásához.

    ![A Hozzáadás gombra.](./media/ipasssmartconnect-tutorial/create_aaduser_03.png)

1. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/ipasssmartconnect-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó Britta Simon e-mail-címét.

    c. Válassza ki a **jelszó megjelenítése** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-an-ipass-smartconnect-test-user"></a>Hozzon létre egy iPass SmartConnect tesztfelhasználó számára

Ebben a szakaszban egy felhasználói Britta Simon nevű iPass SmartConnect hoz létre. Együttműködve [iPass SmartConnect támogatási csoportjának](mailto:help@ipass.com) hozzáadása a felhasználók és a tartományt, amely a iPass SmartConnect platform az engedélyezéshez van szükség. Ha a tartomány a csapata által hozzáadott, a iPass SmartConnect platform automatikusan kiépítve felhasználók. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon használja az Azure egyszeri bejelentkezés iPass SmartConnect való hozzáférést.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon rendel iPass SmartConnect, hajtsa végre az alábbi lépéseket:**

1. Az Azure Portalon nyissa meg az alkalmazások megtekintése, és a könyvtár nézetben keresse meg és nyissa meg **vállalati alkalmazások** kattintson **minden alkalmazás**.

    ![Felhasználó hozzárendelése][201]

1. Az alkalmazások listájában jelölje ki a **iPass SmartConnect**.

    ![A iPass SmartConnect hivatkozásra az alkalmazások listáját](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_app.png)  

1. A bal oldali menüben kattintson **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

1. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel][203]

1. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

1. Kattintson a **kiválasztása** gombot **felhasználók és csoportok** párbeszédpanel.

1. Kattintson a **hozzárendelése** gombot **hozzárendelés hozzáadása** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

**Az SP által kezdeményezett flow-ban az alkalmazás teszteléséhez hajtsa végre az alábbi lépéseket:**

a. Töltse le a windows iPass SmartConnect ügyfél [Itt](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![A iPass SmartConnect hivatkozásra az alkalmazások listáját](./media/ipasssmartconnect-tutorial/testing3.png)

b. Az ügyfél és az indítási telepítése.

c. Kattintson a **Ismerkedés**.

![A iPass SmartConnect hivatkozásra az alkalmazások listáját](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Adja meg a tartományt az Azure-beli felhasználónevével. Kattintson a **továbbra is**. Ez az Azure bejelentkezési oldalára irányítja

![A iPass SmartConnect hivatkozásra az alkalmazások listáját](./media/ipasssmartconnect-tutorial/testing2.png) 

e. Sikeres hitelesítés után az ügyfél aktiválási fog elindulni. Ügyfél fog aktiválni.

**Az identitásszolgáltató által kezdeményezett flow-ban az alkalmazás teszteléséhez hajtsa végre az alábbi lépéseket:**

a. Jelentkezzen be [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

b. Kattintson a iPass SmartConnect alkalmazást.

c. Elindul SSA lap, kattintson a **Windows letöltése alkalmazás** iPass SmartConnect ügyfél telepítéséhez.

![A iPass SmartConnect hivatkozásra az alkalmazások listáját](./media/ipasssmartconnect-tutorial/testing4.png)

d. Után az alkalmazás első indításakor az ügyfél a telepítés automatikusan elindul az aktiválási feltételek és kikötések elfogadása után.

e. Ha az aktiválás nem indul el, kattintson a SSA lapon elindíthatja az aktiválás gombra.

f. Ügyfél fog aktiválni.

## <a name="additional-resources"></a>További források

* [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

<!--Image references-->

[1]: ./media/ipasssmartconnect-tutorial/tutorial_general_01.png
[2]: ./media/ipasssmartconnect-tutorial/tutorial_general_02.png
[3]: ./media/ipasssmartconnect-tutorial/tutorial_general_03.png
[4]: ./media/ipasssmartconnect-tutorial/tutorial_general_04.png

[100]: ./media/ipasssmartconnect-tutorial/tutorial_general_100.png

[200]: ./media/ipasssmartconnect-tutorial/tutorial_general_200.png
[201]: ./media/ipasssmartconnect-tutorial/tutorial_general_201.png
[202]: ./media/ipasssmartconnect-tutorial/tutorial_general_202.png
[203]: ./media/ipasssmartconnect-tutorial/tutorial_general_203.png

