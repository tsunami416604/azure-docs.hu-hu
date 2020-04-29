---
title: 'Oktatóanyag: Azure Active Directory-integráció a Palo Alto Networks-szel kötött portálon | Microsoft Docs'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést Azure Active Directory és a Palo Alto Networks-portál között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: daveba
ms.reviewer: barbkess
ms.assetid: 67a0b476-2305-4157-8658-2ec3625850d5
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 12/25/2018
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: f926741bde3bdcc69cb4ea30f54daca79606047e
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "73160164"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Oktatóanyag: Azure Active Directory-integráció a Palo Alto Networks-szel kötött portálon

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Azure Active Directory (Azure AD) használatával a Palo Alto Networks-portált.

A következő előnyökkel jár a Palo Alto Networks-portál Azure AD-vel való integrálásakor:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Palo Alto Networks céges portálhoz.
* A felhasználó Azure AD-fiókok használatával automatikusan bejelentkezhet a Palo Alto Networks-beli céges portálon (egyszeri bejelentkezés).
* A fiókokat egy, központi helyen, a Azure Portal is kezelheti.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg az [egyszeri bejelentkezést a Azure Active Directory alkalmazásaihoz](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD és a Palo Alto Networks céges portál integrálásához a következő elemek szükségesek:

* Azure Active Directory előfizetés. Ha nem rendelkezik az Azure AD-vel, egy [hónapos próbaverziót](https://azure.microsoft.com/pricing/free-trial/)is beszerezhet.
* A Palo Alto-hálózatok kötött portál egyszeri bejelentkezés (SSO) használatára képes előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

A Palo Alto Networks-portál a következő forgatókönyveket támogatja:

* **IDENTITÁSSZOLGÁLTATÓ – kezdeményezett egyszeri bejelentkezés**
* **Igény szerinti felhasználó üzembe helyezése**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Palo Alto-hálózatokra vonatkozó helyi portál hozzáadása a katalógusból

Első lépésként a katalógusban adja hozzá a Palo Alto Networks portált a felügyelt SaaS-alkalmazások listájához:

1. A [Azure Portal](https://portal.azure.com)bal oldali menüjében válassza a **Azure Active Directory**lehetőséget.

    ![A Azure Active Directory gomb](common/select-azuread.png)

2. Válassza a **vállalati alkalmazások** > **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások lehetőség a menüben](common/enterprise-applications.png)

3. Válassza az **Új alkalmazás** lehetőséget.

    ![Az új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Palo Alto Networks céges portált**. A keresési eredmények között válassza a **Palo Alto Networks-kötött portál**elemet, majd válassza a **Hozzáadás**lehetőséget.

     ![Palo Alto Networks – kötött portál az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése

Az Azure AD egyszeri bejelentkezést a Palo Alto Networks céges portálon konfigurálhatja és tesztelheti a *Britta Simon*nevű teszt felhasználó alapján. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolatot egy Azure AD-felhasználó és ugyanaz a felhasználó között a Palo Alto Networks-beli céges portálon. 

Az Azure AD egyszeri bejelentkezés konfigurálásához és teszteléséhez a Palo Alto Networks céges portálon végezze el a következő feladatokat:

1. Az **[Azure ad egyszeri bejelentkezés konfigurálása](#configure-azure-ad-single-sign-on)**: engedélyezze a felhasználónak a funkció használatát.
2. A **[Palo Alto Networks – kötött portál egyszeri bejelentkezésének konfigurálása](#configure-palo-alto-networks-captive-portal-single-sign-on)**: az alkalmazás egyszeri bejelentkezési beállításainak konfigurálása.
3. **[Azure ad-tesztkörnyezet létrehozása](#create-an-azure-ad-test-user)**: Azure ad-alapú egyszeri bejelentkezés tesztelése a *Britta Simon*felhasználóval.
4. **[Az Azure ad-teszt felhasználójának kiosztása](#assign-the-azure-ad-test-user)**: állítsa be a Britta Simon-t az Azure ad egyszeri bejelentkezés használatára.
5. Az Azure AD-felhasználóhoz társított **Palo Alto Networks Portal**-felhasználó létrehozása: hozzon létre egy partneri felhasználót a *Britta Simon* -ben a Palo Alto Networks céges portálon.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**: Ellenőrizze, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés konfigurálása

Először engedélyezze az Azure AD egyszeri bejelentkezést a Azure Portalban:

1. A [Azure Portal](https://portal.azure.com/)a **Palo Alto Networks – kötött portál alkalmazás-** integráció lapon válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési hivatkozás konfigurálása](common/select-sso.png)

2. Az **egyszeri bejelentkezési módszer kiválasztása** ablaktáblán válassza az **SAML**lehetőséget.

    ![Egyszeri bejelentkezési mód kiválasztása](common/select-saml-option.png)

3. Az **egyszeri bejelentkezés SAML-** panelen való beállítása lapon válassza a ceruza **Szerkesztés** ikont.

    ![A ceruza szerkesztés ikon](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** ablaktáblán hajtsa végre a következő lépéseket:

    ![Palo Alto Networks – céges portál alapszintű SAML-konfigurációs panelje](common/idp-intiated.png)

   1. Az **azonosító**mezőben adja meg a mintát `https://<customer_firewall_host_name>/SAML20/SP`tartalmazó URL-címet.

   2. A **Válasz URL-cím**mezőben adjon meg egy URL- `https://<customer_firewall_host_name>/SAML20/SP/ACS`címet, amely a mintát tartalmazta.

      > [!NOTE]
      > Az ebben a lépésben szereplő helyőrző értékek frissítése a tényleges azonosító és a válasz URL-címekkel. A tényleges értékek beszerzéséhez vegye fel a kapcsolatot a [Palo Alto Networks céges portál ügyfél-támogatási csapatával](https://support.paloaltonetworks.com/support).

5. Az **SAML aláíró tanúsítvány** szakaszban, az **összevonási metaadatok XML**elem mellett válassza a **Letöltés**lehetőséget. Mentse a letöltött fájlt a számítógépre.

    ![Az összevonási metaadatok XML-letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>A Palo Alto Networks-alapú helyszíni portál egyszeri bejelentkezésének konfigurálása

Következő lépésként állítsa be az egyszeri bejelentkezést a Palo Alto-hálózatok helyi portálon:

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Palo Alto Networks webhelyre.

2. Válassza az **eszköz** fület.

    ![A Palo Alto Networks webhely eszköz lapja](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. A menüben válassza ki az **SAML-identitás szolgáltatója**elemet, majd válassza az **Importálás**lehetőséget.

    ![Az Importálás gomb](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. A **SAML-azonosító kiszolgáló profiljának importálása** párbeszédpanelen hajtsa végre a következő lépéseket:

    ![A Palo Alto Networks egyszeri bejelentkezés konfigurálása](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. A **profil neve**mezőben adjon meg egy nevet, például **AzureAD-CaptivePortal**.
    
    2. Az **Identitáskezelő metaadatainak**mellett válassza a **Tallózás**lehetőséget. Válassza ki a metaadatok. xml fájlt, amelyet a Azure Portal letöltött.
    
    3. Kattintson az **OK** gombra.

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása 

Ezután hozzon létre egy *Britta Simon* nevű teszt felhasználót a Azure Portalban:

1. A Azure Portal válassza a **Azure Active Directory** > **felhasználók** > **minden felhasználó**lehetőséget.

    ![A "felhasználók és csoportok" és a "minden felhasználó" hivatkozás](common/users.png)

2. Válassza az **új felhasználó**lehetőséget.

    ![Az új felhasználó gomb](common/new-user.png)

3. A **felhasználó** ablaktáblán hajtsa végre a következő lépéseket:

    ![A felhasználó párbeszédpanel](common/user-properties.png)

    1. A **név**mezőbe írja be a következőt: **BrittaSimon**.
  
    2. A **Felhasználónév**mezőbe írja be **a\@\<BrittaSimon\>your_company_domain**nevet. Például **BrittaSimon\@contoso.com**.

    3. A **Password (jelszó**) mezőbe írjon be egy jelszót. Javasoljuk, hogy tartsa meg a beírt jelszó rekordját. A jelszó **megjelenítése** jelölőnégyzet bejelölésével megjelenítheti a jelszót.

    4. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ezután engedélyezze a hozzáférést a Palo Alto Networks-portálhoz, hogy Britta Simon használhassa az Azure-beli egyszeri bejelentkezést:

1. A Azure Portal válassza a **vállalati alkalmazások** > **minden alkalmazás**lehetőséget.

    ![A vállalati alkalmazások panel](common/enterprise-applications.png)

2. Az alkalmazások listában adja meg a **Palo Alto Networks-kötött portál**elemet, majd válassza ki az alkalmazást.

    ![A Palo Alto Networks – kötött portál hivatkozása az alkalmazások listájában](common/all-applications.png)

3. A menüben válassza a **felhasználók és csoportok**lehetőséget.

    ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Válassza a **felhasználó hozzáadása**elemet. Ezután a **hozzárendelés hozzáadása** panelen válassza a **felhasználók és csoportok**lehetőséget.

    ![A hozzárendelés hozzáadása panel](common/add-assign-user.png)

5. A **felhasználók és csoportok** ablaktábla **felhasználók** listájában válassza a **Britta Simon**elemet. Válassza a **Kiválasztás** lehetőséget.

6. Ha szerepkör-értéket szeretne hozzáadni az SAML-állításhoz, a **szerepkör kiválasztása** panelen válassza ki a megfelelő szerepkört a felhasználó számára. Válassza a **Kiválasztás** lehetőséget.

7. A **hozzárendelés hozzáadása** panelen válassza a **hozzárendelés**lehetőséget.

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Palo Alto Networks – kötött portál tesztelési felhasználó létrehozása

Ezután hozzon létre egy *Britta Simon* nevű felhasználót a Palo Alto Networks-portálon. A Palo Alto Networks-portál támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nem kell végrehajtania a feladatokat. Ha egy felhasználó még nem létezik a Palo Alto Networks-beli céges portálon, akkor a hitelesítés után létrejön egy újat.

> [!NOTE]
> Ha manuálisan szeretne felhasználót létrehozni, forduljon a [Palo Alto Networks céges portál ügyfél-támogatási csapatához](https://support.paloaltonetworks.com/support).

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

A Palo Alto Networks-portál a tűzfal mögött van telepítve egy Windows rendszerű virtuális gépen. Ha az egyszeri bejelentkezést a Palo Alto Networks-portálon szeretné tesztelni, jelentkezzen be a Windows rendszerű virtuális gépre RDP protokoll (RDP) használatával. Az RDP-munkamenetben nyisson meg egy böngészőt, és lépjen a webhelyre. Megnyílik az egyszeri bejelentkezési URL-cím, és a rendszer felszólítja a hitelesítésre. A hitelesítés befejezésekor hozzáférhet a webhelyekhez.

## <a name="additional-resources"></a>További háttéranyagok

További információt a következő cikkekben talál:

- [Oktatóanyagok az SaaS-alkalmazások integrálásához Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Egyszeri bejelentkezés a Azure Active Directory alkalmazásaiba](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Feltételes hozzáférés Azure Active Directory](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

