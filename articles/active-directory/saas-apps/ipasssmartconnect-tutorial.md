---
title: 'Oktatóanyag: Azure Active Directoryval integrált iPass SmartConnect |} Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Azure Active Directory és iPass SmartConnect között.
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
ms.openlocfilehash: 5ae600142f7e90a7f6185c3a948a4174ce4c7797
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/22/2018
ms.locfileid: "36320526"
---
# <a name="tutorial-azure-active-directory-integration-with-ipass-smartconnect"></a>Oktatóanyag: Azure Active Directoryval integrált iPass SmartConnect

Ebben az oktatóanyagban elsajátíthatja iPass SmartConnect integrálása az Azure Active Directory (Azure AD).

IPass SmartConnect integrálása az Azure AD lehetővé teszi a következő előnyöket biztosítja:

- Az Azure AD, aki hozzáfér iPass SmartConnect szabályozhatja.
- Engedélyezheti a felhasználóknak, hogy automatikusan lekérni bejelentkezett iPass SmartConnect (egyszeri bejelentkezés) a saját Azure AD-fiókok számára.
- A fiók egyetlen központi helyen – az Azure-portálon kezelheti.

Ha meg szeretné ismerni az Azure AD SaaS integrálásáról további adatait, tekintse meg [alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](../manage-apps/what-is-single-sign-on.md).

## <a name="prerequisites"></a>Előfeltételek

Konfigurálása az Azure AD-integrációs iPass SmartConnect, a következőkre van szükség:

- Az Azure AD szolgáltatásra
- Egy iPass SmartConnect egyszeri bejelentkezés engedélyezve van az előfizetés

> [!NOTE]
> Ez az oktatóanyag lépéseit teszteléséhez nem ajánlott használata termelési környezetben.

Ebben az oktatóanyagban a lépéseket teszteléséhez kövesse ezeket a javaslatokat:

- Ne használja az éles környezetben, nem szükséges.
- Ha még nem rendelkezik az Azure AD próbaverziójának környezetben, akkor [egy hónapos próbaverzió beszerzése](https://azure.microsoft.com/pricing/free-trial/).

## <a name="scenario-description"></a>Forgatókönyv leírása
Ebben az oktatóanyagban tesztelése az Azure AD egyszeri bejelentkezéshez egy tesztkörnyezetben. Ebben az oktatóanyagban leírt forgatókönyv két fő építőelemeket áll:

1. A gyűjteményből iPass SmartConnect hozzáadása
2. És tesztelés az Azure AD konfigurálása egyszeri bejelentkezés

## <a name="adding-ipass-smartconnect-from-the-gallery"></a>A gyűjteményből iPass SmartConnect hozzáadása
Az Azure AD integrálása a iPass SmartConnect konfigurálásához kell hozzáadnia iPass SmartConnect a gyűjteményből a felügyelt SaaS-alkalmazások listájára.

**A gyűjteményből iPass SmartConnect hozzáadásához hajtsa végre az alábbi lépéseket:**

1. Az a  **[Azure-portálon](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen **Azure Active Directory** ikonra. 

    ![Az Azure Active Directory gomb][1]

2. Navigáljon a **vállalati alkalmazások**. Ezután lépjen **összes alkalmazás**.

    ![A vállalati alkalmazások panel][2]

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** párbeszédpanel tetején gombra.

    ![Az új alkalmazás gomb][3]

4. Írja be a keresőmezőbe, **iPass SmartConnect**, jelölje be **iPass SmartConnect** eredmény panelen kattintson a **Hozzáadás** gombra kattintva vegye fel az alkalmazást.

    ![az eredménylistában SmartConnect iPass](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_addfromgallery.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezést a iPass SmartConnect "Britta Simon" nevű tesztfelhasználó alapján.

Az egyszeri bejelentkezés működéséhez az Azure AD az Azure AD-ben a felhasználó van SmartConnect iPass a párjukhoz felhasználó tudnia kell. Ez azt jelenti az Azure AD-felhasználó és a kapcsolódó felhasználó a iPass hivatkozás kapcsolatának SmartConnect kell létrehozni.

Az Azure AD egyszeri bejelentkezést a iPass SmartConnect tesztelése és konfigurálása, hogy végezze el a következő építőelemeket kell:

1. **[Az Azure AD az egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – lehetővé teszi a felhasználók a szolgáltatás használatához.
2. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezést a Britta Simon teszteléséhez.
3. **[Hozzon létre egy iPass SmartConnect tesztfelhasználó](#create-an-ipass-smartconnect-test-user)**  - Britta Simon egy partner, a felhasználó az Azure AD ábrázolását kapcsolódó SmartConnect iPass rendelkezik.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)**  - Britta Simon használata az Azure AD az egyszeri bejelentkezés engedélyezése.
5. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD az egyszeri bejelentkezés konfigurálása

Ebben a szakaszban az Azure AD egyszeri bejelentkezés engedélyezése az Azure portálon, és a iPass SmartConnect alkalmazás egyszeri bejelentkezés konfigurálása.

**Konfigurálása az Azure AD az egyszeri bejelentkezés iPass SmartConnect, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a a **iPass SmartConnect** alkalmazás integráció lapján, kattintson a **egyszeri bejelentkezés**.

    ![Egyszeri bejelentkezés kapcsolat konfigurálása][4]

2. Az a **egyszeri bejelentkezés** párbeszédablakban válassza **mód** , **SAML-alapú bejelentkezés** egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés párbeszédpanel](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_samlbase.png)

3. Az a **iPass SmartConnect tartomány és az URL-címek** szakaszban, ha szeretne beállítani az alkalmazás **IDP** kezdeményezett mód, hajtsa végre a lépéseket nem szükséges.

    ![az egyszeri bejelentkezés információk iPass SmartConnect tartomány és az URL-címek](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url1.png)

4. Ellenőrizze a megjelenítése speciális URL-cím beállításait, és hajtsa végre a következő lépést, ha konfigurálja az alkalmazást a **SP** kezdeményezett mód:

    ![az egyszeri bejelentkezés információk iPass SmartConnect tartomány és az URL-címek](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_url2.png)

    A bejelentkezési URL-cím mezőbe írja be egy URL-cím: `https://om-activation.ipass.com/ClientActivation/ssolanding.go`

5. iPass SmartConnect alkalmazás vár a SAML helyességi feltételek egy meghatározott formátumban. Állítsa be a következő jogcímeket ehhez az alkalmazáshoz. Ezek az attribútumok értékének kezelheti a "**felhasználói attribútumok**" szakasz alkalmazás integráció lapján. Az alábbi képernyőfelvételen látható egy példa a.

    ![Egyszeri bejelentkezés konfigurálása](./media/ipasssmartconnect-tutorial/attribute.png)

6. Kattintson a **nézet és egyéb felhasználói attribútumok szerkesztése** jelölőnégyzet a **felhasználói attribútumok** szakaszban bontsa ki az attribútumok. Hajtsa végre a következő lépéseket mindegyik a megjelenített attribútumok-

    | Attribútum neve | Attribútum értéke | Namespace érték|
    | ---------------| --------------- |----------------|
    | Utónév | User.givenName |   |
    | Vezetéknév | User.surname | |
    | e-mailben | User.userPrincipalName | |
    | felhasználónév | User.userPrincipalName | |

    a. Kattintson a **Hozzáadás attribútum** megnyitásához a **attribútum hozzáadása** párbeszédpanel.

    ![Egyszeri bejelentkezés konfigurálása](./media/ipasssmartconnect-tutorial/tutorial_attribute_04.png)

    ![Egyszeri bejelentkezés konfigurálása](./media/ipasssmartconnect-tutorial/tutorial_attribute_05.png)

    b. Az a **neve** szövegmező, írja be az adott sorhoz feltüntetett attribútumot nevét.

    c. Az a **érték** kilistázásához írja be a sorhoz látható attribútum értéke.

    d. Tartsa a sor üres névtér értéket.

    e. Kattintson az **OK** gombra.

7. Az a **SAML-aláíró tanúsítványa** területen kattintson **metaadatainak XML-kódja** és mentse a metaadat-fájlt a számítógépen.

    ![A tanúsítvány letöltési hivatkozását](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_certificate.png)

8. Kattintson a **mentése** gombra.

    ![Egyszeri bejelentkezés Mentés gombra konfigurálása](./media/ipasssmartconnect-tutorial/tutorial_general_400.png)

9. Egyszeri bejelentkezés konfigurálása **iPass SmartConnect** oldalon kell küldeniük a letöltött **metaadatainak XML-kódja** és a **tartománynév** való [iPass SmartConnect támogatási csoport](mailto:help@ipass.com). Akkor állítsa be ezt a beállítást, hogy a SAML SSO kapcsolat mindkét oldalán megfelelően beállítva.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure AD-teszt felhasználó

Ez a szakasz célja a tesztfelhasználó létrehozása az Azure portálon Britta Simon nevezik.

   ![Hozzon létre egy Azure AD-teszt felhasználó][100]

**Tesztfelhasználó létrehozása az Azure AD-ban, hajtsa végre az alábbi lépéseket:**

1. Az Azure portálon a bal oldali ablaktáblán kattintson a **Azure Active Directory** gombra.

    ![Az Azure Active Directory gomb](./media/ipasssmartconnect-tutorial/create_aaduser_01.png)

2. Azon felhasználók listájának megtekintéséhez keresse fel **felhasználók és csoportok**, és kattintson a **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](./media/ipasssmartconnect-tutorial/create_aaduser_02.png)

3. Megnyitásához a **felhasználói** párbeszédpanel, kattintson a **Hozzáadás** tetején a **minden felhasználó** párbeszédpanel megnyitásához.

    ![A Hozzáadás gombra.](./media/ipasssmartconnect-tutorial/create_aaduser_03.png)

4. Az a **felhasználói** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A felhasználó párbeszédpanel](./media/ipasssmartconnect-tutorial/create_aaduser_04.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.

    b. Az a **felhasználónév** mezőbe írja be a felhasználó e-mail címe az Britta Simon.

    c. Válassza ki a **megjelenítése jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="create-an-ipass-smartconnect-test-user"></a>Hozzon létre egy iPass SmartConnect tesztfelhasználó számára

Ebben a szakaszban egy iPass SmartConnect Britta Simon nevű felhasználót hoz létre. Együttműködve [iPass SmartConnect támogatási csoport](mailto:help@ipass.com) a felhasználókat vagy a tartománnyal, amely ahhoz szükséges, hogy a iPass SmartConnect platform szerepel az engedélyezési listán kell hozzáadni. Ha a tartomány a csapata által hozzáadott, felhasználók fogja lekérni automatikusan hozzáférést kapnak a iPass SmartConnect platform. Felhasználók kell létrehoznia és aktiválni az egyszeri bejelentkezés használata előtt.

### <a name="assign-the-azure-ad-test-user"></a>Rendelje hozzá az Azure AD-teszt felhasználó

Ebben a szakaszban engedélyezze Britta Simon Azure egyszeri bejelentkezéshez használandó iPass SmartConnect való hozzáférés biztosítása.

![A felhasználói szerepkör hozzárendelése][200]

**Britta Simon hozzárendelése iPass SmartConnect, hajtsa végre az alábbi lépéseket:**

1. Az Azure-portálon, nyissa meg az alkalmazások nézet, majd nyissa meg a könyvtár nézetet, és navigáljon **vállalati alkalmazások** kattintson **összes alkalmazás**.

    ![Felhasználó hozzárendelése][201]

2. Az alkalmazások listában válassza ki a **iPass SmartConnect**.

    ![A iPass SmartConnect hivatkozásra az alkalmazások listáját](./media/ipasssmartconnect-tutorial/tutorial_ipasssmartconnect_app.png)  

3. A bal oldali menüben kattintson a **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra][202]

4. Kattintson a **Hozzáadás** gombra. Válassza ki **felhasználók és csoportok** a **hozzáadása hozzárendelés** párbeszédpanel.

    ![A hozzárendelés hozzáadása panelen][203]

5. A **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listában.

6. Kattintson a **válasszon** gombra **felhasználók és csoportok** párbeszédpanel.

7. Kattintson a **hozzárendelése** gombra **hozzáadása hozzárendelés** párbeszédpanel.

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés vizsgálata

Ebben a szakaszban az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen tesztelése.

**Az alkalmazás a Szolgáltató kezdeményezett folyamatában, hajtsa végre az alábbi lépéseket:**

a. Töltse le a windows iPass SmartConnect ügyfél [Itt](https://om-activation.ipass.com/ClientActivation/ssolanding.go).

![A iPass SmartConnect hivatkozásra az alkalmazások listáját](./media/ipasssmartconnect-tutorial/testing3.png)

b. Telepítse az ügyfelet, és indítsa el.

c. Kattintson a **Ismerkedés**.

![A iPass SmartConnect hivatkozásra az alkalmazások listáját](./media/ipasssmartconnect-tutorial/testing1.png) 

d. Adja meg Azure felhasználói nevének tartománnyal. Kattintson a **továbbra is**. Ez a rendszer átirányítja az Azure bejelentkezési lapra

![A iPass SmartConnect hivatkozásra az alkalmazások listáját](./media/ipasssmartconnect-tutorial/testing2.png) 

e. Sikeres hitelesítés után az ügyfél aktiválási fog elindulni. Ügyfél aktiválva lesz beolvasása.

**Az alkalmazás az IdP kezdeményezett folyamatában, hajtsa végre az alábbi lépéseket:**

a. Jelentkezzen be [ https://myapps.microsoft.com ](https://myapps.microsoft.com).

b. Kattintson a iPass SmartConnect alkalmazást.

c. Elindítja a SSA lap, kattintson a **Windows letöltése alkalmazás** iPass SmartConnect ügyfél telepítéséhez.

![A iPass SmartConnect hivatkozásra az alkalmazások listáját](./media/ipasssmartconnect-tutorial/testing4.png)

d. Az első indítsa el az ügyfél a telepítés automatikusan az aktiválási feltételek és kikötések elfogadása után elindul.

e. Ha aktiválási nem indul el, kattintson a SSA lapon elindíthatja az aktiválás gombra.

f. Ügyfél aktiválva lesz beolvasása.

## <a name="additional-resources"></a>További források

* [Az Azure Active Directoryval SaaS-alkalmazások integrációjával kapcsolatos bemutatók felsorolása](tutorial-list.md)
* [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryban?](../manage-apps/what-is-single-sign-on.md)

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

