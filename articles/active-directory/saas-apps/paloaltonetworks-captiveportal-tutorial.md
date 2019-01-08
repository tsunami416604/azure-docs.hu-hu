---
title: 'Oktatóanyag: A Palo Alto Networks - szükséglet Portal Azure Active Directory-integráció |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Palo Alto Networks - szükséglet portál között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: Azure-Active-Directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.openlocfilehash: eff08cc17f475e2b6ad6406e463de27371bbe5b1
ms.sourcegitcommit: 3ab534773c4decd755c1e433b89a15f7634e088a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/07/2019
ms.locfileid: "54064729"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks---captive-portal"></a>Oktatóanyag: A Palo Alto Networks - szükséglet Portal Azure Active Directory-integráció

Ebben az oktatóanyagban elsajátíthatja, hogyan integrálható a Palo Alto Networks - szükséglet Portal Azure Active Directoryval (Azure AD).
Futtató Palo Alto Networks - szükséglet portál az Azure AD integrálása nyújt a következő előnyökkel jár:

* Szabályozhatja, ki férhet hozzá a Palo Alto Networks - szükséglet Portal Azure AD-ben.
* Engedélyezheti a felhasználóknak, hogy lehet automatikusan bejelentkezett a Palo Alto Networks - szükséglet Portal (egyszeri bejelentkezés) az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

Ha meg szeretné ismerni a SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további részletekért, lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).
Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) a feladatok megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

A Palo Alto Networks - szükséglet Portal, az Azure AD-integráció konfigurálása a következőkre van szükség:

* Az Azure AD-előfizetés. Ha nem rendelkezik egy Azure AD-környezetet, beszerezheti a egy havi próbalehetőség [Itt](https://azure.microsoft.com/pricing/free-trial/)
* Futtató Palo Alto Networks - szükséglet portál egyszeri bejelentkezés engedélyezve van az előfizetés

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

* Palo Alto Networks - szükséglet portál támogatja **Identitásszolgáltató** által kezdeményezett egyszeri bejelentkezés

* Palo Alto Networks - szükséglet portál támogatja **igény szerinti** felhasználók átadása

## <a name="adding-palo-alto-networks---captive-portal-from-the-gallery"></a>Palo Alto Networks - szükséglet portálról, a katalógus hozzáadása

A Palo Alto Networks - szükséglet Portal Azure ad-ben,-integráció konfigurálása kell hozzáadnia a Palo Alto Networks - függő portált a galériából a felügyelt SaaS-alkalmazások listájában.

**Adja hozzá a Palo Alto Networks - szükséglet portál katalógusból, hajtsa végre az alábbi lépéseket:**

1. Az a **[az Azure portal](https://portal.azure.com)**, kattintson a bal oldali navigációs panelen, **Azure Active Directory** ikonra.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Navigáljon a **vállalati alkalmazások** majd válassza ki a **minden alkalmazás** lehetőséget.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

3. Új alkalmazás hozzáadásához kattintson **új alkalmazás** gombra a párbeszédpanel tetején.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Palo Alto Networks - szükséglet portál**, jelölje be **Palo Alto Networks - szükséglet portál** eredmény panelen kattintson a **hozzáadása** az alkalmazás hozzáadására szolgáló gomb .

     ![Futtató Palo Alto Networks - szükséglet portál a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Ebben a szakaszban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés a Palo Alto Networks - szükséglet portál nevű tesztfelhasználó alapján **Britta Simon**.
Egyszeri bejelentkezésre való munka, egy Azure AD-felhasználót és a kapcsolódó felhasználó a Palo Alto Networks - hivatkozás kapcsolatának függő portálon kell létrehozni.

Konfigurálás és tesztelés az Azure AD egyszeri bejelentkezés a Palo Alto Networks - szükséglet portálon kell hajtsa végre a következő építőelemeket:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**  – ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Függő portál egyszeri bejelentkezés konfigurálása a Palo Alto Networks -](#configure-palo-alto-networks---captive-portal-single-sign-on)**  – az alkalmazás oldalán az egyszeri bejelentkezés beállításainak konfigurálása.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  – az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  – Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre futtató Palo Alto Networks - szükséglet portál tesztfelhasználó](#create-palo-alto-networks---captive-portal-test-user)**  – van egy Britta Simon megfelelője a Palo Alto Networks - szükséglet portál, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**  – győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Ebben a szakaszban engedélyeznie kell az Azure AD egyszeri bejelentkezés az Azure Portalon.

A Palo Alto Networks - szükséglet Portal, az Azure AD egyszeri bejelentkezés konfigurálásához hajtsa végre az alábbi lépéseket:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Palo Alto Networks - szükséglet portál** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** párbeszédpanelen válassza **SAML/WS-Fed** módot az egyszeri bejelentkezés engedélyezése.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** kattintson **szerkesztése** ikonra kattintva nyissa meg a **alapszintű SAML-konfigurációja** párbeszédpanel.

    ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Futtató Palo Alto Networks - szükséglet portál tartomány és URL-címeket egyetlen bejelentkezési adatait](common/idp-intiated.png)

    a. Az a **azonosító** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<Customer Firewall Hostname>/SAML20/SP`

    b. Az a **válasz URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<Customer Firewall Hostname>/SAML20/SP/ACS`

    > [!NOTE]
    > Ezek a értékei nem valódi. Ezek az értékek frissítse a tényleges azonosítóját és a válasz URL-cím. Kapcsolattartó [Palo Alto Networks - szükséglet portál ügyfél-támogatási csapatával](https://support.paloaltonetworks.com/support) beolvasni ezeket az értékeket. Emellett olvassa el a minták látható a **alapszintű SAML-konfigurációja** szakaszban az Azure Portalon.

5. Az a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén kattintson **letöltése** letöltéséhez a **összevonási metaadatainak XML**  a megadott lehetőségek közül a követelmény alapján, majd mentse el a számítógépen.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

### <a name="configure-palo-alto-networks---captive-portal-single-sign-on"></a>Palo Alto Networks - függő portál egyszeri bejelentkezés konfigurálása

1. Nyissa meg a Palo Alto egy másik böngészőablakban rendszergazdaként.

2. Kattintson a **eszköz**.

    ![Rendszert futtató Palo Alto egyszeri bejelentkezés konfigurálása](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Válassza ki **identitásszolgáltató SAML** a bal oldali navigációs sávot, majd kattintson a "Importálása" a metaadatait tartalmazó fájl importálása.

    ![Rendszert futtató Palo Alto egyszeri bejelentkezés konfigurálása](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Hajtsa végre a következő műveleteket az importálási ablakot a

    ![Rendszert futtató Palo Alto egyszeri bejelentkezés konfigurálása](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    a. Az a **profilnév** szövegmezőben adjon meg egy nevet például az Azure AD felügyeleti felhasználói Felületéhez.
    
    b. A **Identity Provider metaadatok**, kattintson a **Tallózás** , és válassza ki a metadata.xml fájlt, amely az Azure Portalról letöltött
    
    c. Kattintson az **OK** gombra

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ez a szakasz célja az Azure Portalon Britta Simon nevű hozzon létre egy tesztfelhasználót.

1. Az Azure Portalon, a bal oldali panelen válassza ki a **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó** a képernyő tetején.

    ![Új felhasználó gomb](common/new-user.png)

3. A felhasználó tulajdonságai között az alábbi lépések végrehajtásával.

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    a. Az a **neve** mezőbe írja be **BrittaSimon**.
  
    b. Az a **felhasználónév** mezőtípus **brittasimon@yourcompanydomain.extension**  
    Például: BrittaSimon@contoso.com

    c. Válassza ki **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel az értékkel, a jelszó mező jelenik meg.

    d. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezze Britta Simon által biztosított hozzáférés Palo Alto Networks - szükséglet portál Azure egyszeri bejelentkezés használatára.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, jelölje be **minden alkalmazás**, majd **Palo Alto Networks - szükséglet portál**.

    ![Vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Az alkalmazások listáját, írja be, és válassza ki **Palo Alto Networks - szükséglet portál**.

    ![A futtató Palo Alto Networks - szükséglet portál hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. A bal oldali menüben válassza **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Kattintson a **felhasználó hozzáadása** gombra, majd válassza **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** párbeszédpanelen válassza **Britta Simon** a felhasználók listában, majd kattintson a **kiválasztása** gombra a képernyő alján.

6. Ha minden szerepkör értéket várt a a SAML helyességi feltétel, majd a a **Szerepkörválasztás** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó a listából, majd kattintson a **kiválasztása** gombra a képernyő alján.

7. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-palo-alto-networks---captive-portal-test-user"></a>Futtató Palo Alto Networks - szükséglet portál tesztfelhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználó a Palo Alto Networks - szükséglet portálon jön létre. Palo Alto Networks - szükséglet portál támogatja **just-in-time-felhasználók létrehozásának**, amely alapértelmezés szerint engedélyezve van. Nincs meg ebben a szakaszban a művelet elem. Ha a felhasználó még nem létezik a Palo Alto Networks - szükséglet Portal, a hitelesítés után egy új jön létre.

> [!NOTE]
> Hozzon létre egy felhasználót manuálisan kell, ha kapcsolódni kell a [Palo Alto Networks - szükséglet portál ügyfél-támogatási csapatával](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Függő portál Windows virtuális gépen a tűzfal mögött van konfigurálva. Egyszeri bejelentkezés a függő portálon, a Windows virtuális gép RDP-vel való bejelentkezés teszteléséhez. A belül az RDP-munkamenetet, nyissa meg egy böngészőt, és bármely webhelyen, azt automatikusan megnyitja az egyszeri bejelentkezési URL-cím és a hitelesítés kérése. Hitelesítéskonfiguráló befejeződése után meg kell tudni navgiate webhelyek.

## <a name="additional-resources"></a>További források

- [ SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

