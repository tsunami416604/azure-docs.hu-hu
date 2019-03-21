---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Palo Alto hálózatok függő portállal |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés az Azure Active Directory és a Palo Alto hálózatok függő portál között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0a62aa573d49ccdd237e692b63a6fda0dd83d52a
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57888297"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Oktatóanyag: A Palo Alto hálózatok függő Portal Azure Active Directory-integráció

Ebben az oktatóanyagban megismerheti, hogyan integrálható a Palo Alto hálózatok függő Portal Azure Active Directoryval (Azure AD).

Palo Alto hálózatok függő portál integrálása az Azure ad-vel a következőket a következő előnyöket kínálja:

* Az Azure AD-ben szabályozhatja, ki férhet hozzá a Palo Alto hálózatok függő portálon.
* Automatikusan bejelentkezhet a Palo Alto hálózatok függő portál (egyszeri bejelentkezés) felhasználók Azure AD-felhasználói fiókok használatával.
* A fiókok egyetlen központi helyen, az Azure Portalon kezelheti.

További információ a szoftverek az Azure ad-vel szoftverként (saas biztosított) alkalmazás integrációja, lásd: [egyszeri bejelentkezés alkalmazásokhoz az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

Azure AD integrálása a Palo Alto hálózatok függő Portal, a következőkre van szükség:

* Az Azure Active Directory-előfizetés. Ha nem rendelkezik Azure ad-ben, egy [egy havi próbalehetőség](https://azure.microsoft.com/pricing/free-trial/).
* A Palo Alto hálózatok függő portál egyszeri bejelentkezés (SSO)-előfizetés engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD egyszeri bejelentkezés egy tesztkörnyezetben.

Palo Alto hálózatok függő portál ezeket a forgatókönyveket támogatja:

* **Identitásszolgáltató által kezdeményezett egyszeri bejelentkezés**
* **Felhasználókiépítés igény**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Palo Alto hálózatok függő portál hozzáadása a katalógusból

Első lépésként a katalógusban, Palo Alto hálózatok függő portál hozzáadása a felügyelt SaaS-alkalmazások listája:

1. Az a [az Azure portal](https://portal.azure.com), a bal oldali menüben válassza ki a **Azure Active Directory**.

    ![Az Azure Active Directory gomb](common/select-azuread.png)

2. Válassza ki **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások lehetőséget a menüben](common/enterprise-applications.png)

3. Válassza az **Új alkalmazás** lehetőséget.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A Keresés mezőbe írja be a **Palo Alto hálózatok függő portál**. A keresési eredmények között, válassza ki a **Palo Alto Networks - szükséglet portál**, majd válassza ki **Hozzáadás**.

     ![Futtató Palo Alto Networks - szükséglet portál a találatok listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

A Palo Alto hálózatok függő portál nevű tesztfelhasználó alapján az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása, *Britta Simon*. Az egyszeri bejelentkezés működjön a Palo Alto hálózatok függő portál Azure AD-felhasználót, és ugyanaz a felhasználó közötti kapcsolatot kell létesítenie. 

Az Azure AD egyszeri bejelentkezés Palo Alto hálózatok függő portállal tesztelése és konfigurálása, hajtsa végre a következő feladatokat:

1. **[Az Azure AD egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**: Engedélyezze a felhasználó a funkció használatához.
2. **[Palo Alto hálózatok függő portál egyszeri bejelentkezés konfigurálása](#configure-palo-alto-networks-captive-portal-single-sign-on)**: Az egyszeri bejelentkezési beállításainak konfigurálása az alkalmazásban.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**: Az Azure AD egyszeri bejelentkezés a felhasználóval tesztelje *Britta Simon*.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**: Britta Simon beállítása az Azure AD egyszeri bejelentkezés használatára.
5. **Hozzon létre egy Palo Alto hálózatok függő portál tesztfelhasználót**: Hozzon létre egy partner felhasználót *Britta Simon* a Palo Alto hálózatok függő portál, amely kapcsolódik az Azure AD-felhasználót.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**: Győződjön meg arról, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Első lépésként engedélyezze az Azure AD egyszeri bejelentkezés az Azure Portalon:

1. Az a [az Azure portal](https://portal.azure.com/), a a **Palo Alto Networks - szükséglet portál** alkalmazás integráció lapon jelölje be **egyszeri bejelentkezési**.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az a **egyszeri bejelentkezési módszer** ablaktáblán válassza ki az **SAML**.

    ![Egyszeri bejelentkezés kijelölési mód bekapcsolása](common/select-saml-option.png)

3. Az a **állítsa be egyszeri bejelentkezést az SAML** panelen válassza a Ceruza **szerkesztése** ikonra.

    ![A penciled Szerkesztés ikon](common/edit-urls.png)

4. Az a **alapszintű SAML-konfigurációja** panelen a következő lépéseket:

    ![Palo Alto hálózatok függő portál alapszintű SAML-konfigurációja panel](common/idp-intiated.png)

   1. A **azonosító**, adja meg a következő mintának `https://<customer_firewall_host_name>/SAML20/SP`.

   2. A **válasz URL-cím**, adja meg a következő mintának `https://<customer_firewall_host_name>/SAML20/SP/ACS`.

      > [!NOTE]
      > Frissítse a helyőrző értékeket az ebben a lépésben a tényleges azonosítóval, és a válasz URL. A tényleges értékek beszerzéséhez forduljon [Palo Alto hálózatok függő portál ügyfél-támogatási csapatának](https://support.paloaltonetworks.com/support).

5. Az a **SAML-aláíró tanúsítvány** részben, a **összevonási metaadatainak XML**válassza **letöltése**. Mentse a letöltött fájlt a számítógépen.

    ![Az összevonási metaadatainak XML-fájl letöltési hivatkozás](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Palo Alto hálózatok függő portál egyszeri bejelentkezés konfigurálása

Következő lépésként állítsa be egyszeri bejelentkezést a Palo Alto hálózatok függő portálon:

1. Egy másik böngészőablakban jelentkezzen be a Palo Alto Networks-webhely rendszergazdaként.

2. Válassza ki a **eszköz** fülre.

    ![A Palo Alto Networks-webhely eszköz lap](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. Válassza a menüben **identitásszolgáltató SAML**, majd válassza ki **importálás**.

    ![Az Importálás gombra](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Az a **SAML Identity Provider kiszolgáló profilok importálása** párbeszédpanelen töltse ki az alábbi lépéseket:

    ![Palo Alto Networks egyszeri bejelentkezés konfigurálása](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. A **profilnév**, adjon meg egy nevet, például **Azure ad-CaptivePortal**.
    
    2. A **Identity Provider metaadatok**, jelölje be **Tallózás**. Az Azure Portalon válassza ki a letöltött metadata.xml fájlt.
    
    3. Kattintson az **OK** gombra.

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára 

Ezután hozzon létre egy tesztfelhasználót nevű *Britta Simon* az Azure Portalon:

1. Az Azure Portalon válassza ki a **Azure Active Directory** > **felhasználók** > **minden felhasználó**.

    ![A "felhasználók és csoportok" és "Minden felhasználó" hivatkozások](common/users.png)

2. Válassza ki **új felhasználó**.

    ![Az új felhasználó gomb](common/new-user.png)

3. Az a **felhasználói** panelen a következő lépéseket:

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    1. A **neve**, adja meg **BrittaSimon**.
  
    2. A **felhasználónév**, adja meg **BrittaSimon\@\<your_company_domain\>**. Ha például **BrittaSimon\@contoso.com**.

    3. A **jelszó**, adjon meg egy jelszót. Azt javasoljuk, hogy Ön tárolja a megadott jelszó. Kiválaszthatja a **jelszó megjelenítése** melletti jelölőnégyzetet, hogy a jelszó megjelenítéséhez.

    4. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ezután hozzáférést Palo Alto hálózatok függő portálra így Britta Simon Azure egyszeri bejelentkezéshez használható:

1. Az Azure Portalon válassza ki a **vállalati alkalmazások** > **minden alkalmazás**.

    ![A vállalati alkalmazások panelen](common/enterprise-applications.png)

2. Adja meg az alkalmazások listáját **Palo Alto Networks - szükséglet portál**, és válassza ki az alkalmazást.

    ![A futtató Palo Alto Networks - szükséglet portál hivatkozásra az alkalmazások listáját](common/all-applications.png)

3. Válassza a menüben **felhasználók és csoportok**.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

4. Válassza ki **felhasználó hozzáadása**. Ezt követően a a **-hozzárendelés hozzáadása** ablaktáblán válassza előbb **felhasználók és csoportok**.

    ![A Hozzáadás hozzárendelési ablaktáblán](common/add-assign-user.png)

5. Az a **felhasználók és csoportok** ablaktáblán, a a **felhasználók** listáról válassza ki **Britta Simon**. Válassza ki **kiválasztása**.

6. Az SAML-előfeltétel a szerepkör érték hozzáadása a **szerepkör kiválasztása** ablaktáblán válassza ki a megfelelő szerepkört a felhasználóhoz. Válassza ki **kiválasztása**.

7. Az a **-hozzárendelés hozzáadása** ablaktáblán válassza előbb **hozzárendelése**.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Palo Alto hálózatok függő portál tesztfelhasználó létrehozása

Ezután hozzon létre egy nevű felhasználó *Britta Simon* Palo Alto hálózatok függő portálon. Palo Alto hálózatok függő portál támogatja just-in-time-felhasználók létrehozásának, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban feladatok befejezéséhez nem kell. Ha a felhasználó még nem létezik a Palo Alto hálózatok függő portál, a hitelesítés után egy új jön létre.

> [!NOTE]
> Ha szeretné manuálisan hozzon létre egy felhasználót, lépjen kapcsolatba a [Palo Alto hálózatok függő portál ügyfél-támogatási csapatának](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Tűzfal mögött lévő Windows virtuális gép telepítve van a Palo Alto hálózatok függő portál. Egyszeri bejelentkezés tesztelése a Palo Alto hálózatok függő portál, jelentkezzen be a Windows virtuális gép távoli asztali protokoll (RDP) használatával. Az RDP-munkamenetet nyisson meg egy böngészőt, és bármely webhelyen. Az egyszeri bejelentkezési URL-címet nyitja meg, és hitelesítést kér. Ha hitelesítési befejeződött, a websites érheti el.

## <a name="additional-resources"></a>További források

További információkért tanulmányozza a következő cikkeket:

- [SaaS-alkalmazások integrálása az Azure Active Directoryval kapcsolatos oktatóanyagok](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Egyszeri bejelentkezés az Azure Active Directory-alkalmazások](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Az Azure Active Directory feltételes hozzáférés](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

