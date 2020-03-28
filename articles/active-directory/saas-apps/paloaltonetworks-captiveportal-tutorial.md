---
title: 'Oktatóanyag: Az Azure Active Directory integrációja a Palo Alto Networks Captive Portal portállal | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Palo Alto Networks Captive Portal között.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "73160164"
---
# <a name="tutorial-azure-active-directory-integration-with-palo-alto-networks-captive-portal"></a>Oktatóanyag: Az Azure Active Directory integrációja a Palo Alto Networks Captive Portal portállal

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Palo Alto Networks Captive Portal-t az Azure Active Directoryval (Azure AD).

A Palo Alto Networks Captive Portal azure AD-vel való integrálása a következő előnyöket élvezi:

* Az Azure AD-ben szabályozhatja, hogy ki férhet hozzá a Palo Alto Networks Captive Portal hoz.
* A Palo Alto Networks Captive Portal (egyszeri bejelentkezés) felhasználói Azure-Fiókhasználatával automatikusan bejelentkezhet a felhasználókba.
* A fiókokat egy központi helyen, az Azure Portalon kezelheti.

Ha többet szeretne megtudni a szoftverszolgáltatásként (SaaS) alkalmazásintegrációról az Azure AD-vel, olvassa [el az Egyszeri bejelentkezés az alkalmazásokra az Azure Active Directoryban című témakört.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

Ha nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/)

## <a name="prerequisites"></a>Előfeltételek

Az Azure AD integrálása a Palo Alto Networks Captive Portal szolgáltatással a következő elemekre van szüksége:

* Egy Azure Active Directory-előfizetés. Ha nem rendelkezik az Azure AD,kaphat egy [hónapos próbaverzió.](https://azure.microsoft.com/pricing/free-trial/)
* A Palo Alto Networks Captive Portal egyszeri bejelentkezés (SSO)-kompatibilis előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

A Palo Alto Networks Captive Portal a következő forgatókönyveket támogatja:

* **IDP által kezdeményezett egyszeri bejelentkezés**
* **Just-in-time felhasználói kiépítés**

## <a name="add-palo-alto-networks-captive-portal-from-the-gallery"></a>Add Palo Alto Networks Captive Portal a galériából

A kezdéshez a galériában adja hozzá a Palo Alto Networks Captive Portal-t a felügyelt SaaS-alkalmazások listájához:

1. Az [Azure Portalon](https://portal.azure.com)válassza a bal oldali **menüben**az Azure Active Directory lehetőséget.

    ![Az Azure Active Directory gombja](common/select-azuread.png)

2. Válassza **a Vállalati alkalmazások** > **minden alkalmazás lehetőséget.**

    ![A Vállalati alkalmazások beállítás a menüben](common/enterprise-applications.png)

3. Válassza az **Új alkalmazás** lehetőséget.

    ![Az Új alkalmazás gomb](common/add-new-app.png)

4. A keresőmezőbe írja be a **Palo Alto Networks Captive Portal értéket.** A keresési eredmények között válassza a **Palo Alto Networks - Captive Portal (Palo Alto Networks - Captive Portal**) lehetőséget, majd a **Hozzáadás**lehetőséget.

     ![Palo Alto Networks - Captive Portál az eredmények listájában](common/search-new-app.png)

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése

Az Azure AD egyszeri bejelentkezését a Palo Alto Networks Captive Portal segítségével konfigurálhatja és tesztelheti *egy Britta Simon*nevű tesztfelhasználó alapján. Egyszeri bejelentkezés működéséhez létre kell hoznia egy azure-felhasználó és ugyanaz a felhasználó között a Palo Alto Networks Captive Portal. 

Az Azure AD egyszeri bejelentkezésének konfigurálásához és teszteléséhez a Palo Alto Networks Captive Portal szolgáltatással hajtsa végre a következő feladatokat:

1. **[Konfigurálja az Azure AD egyszeri bejelentkezés:](#configure-azure-ad-single-sign-on)** Engedélyezze a felhasználó számára a szolgáltatás használatát.
2. **[Konfigurálja a Palo Alto Networks Captive Portal egyszeri bejelentkezést:](#configure-palo-alto-networks-captive-portal-single-sign-on)** Konfigurálja az egyszeri bejelentkezési beállításokat az alkalmazásban.
3. **[Hozzon létre egy Azure AD-tesztfelhasználót:](#create-an-azure-ad-test-user)** Tesztelje az Azure AD egyszeri bejelentkezését *britta Simon*felhasználóval.
4. **[Rendelje hozzá az Azure AD-teszt felhasználó:](#assign-the-azure-ad-test-user)** Állítsa be a Britta Simon az Azure AD egyszeri bejelentkezés.
5. **Hozzon létre egy Palo Alto Networks Captive Portal tesztfelhasználó:** Hozzon létre egy megfelelő felhasználó *Britta Simon* palo alto networks captive portál, amely kapcsolódik az Azure AD-felhasználó.
6. **[Egyszeri bejelentkezés tesztelése](#test-single-sign-on)**: Ellenőrizze, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása

Először engedélyezze az Azure AD egyszeri bejelentkezését az Azure Portalon:

1. Az [Azure Portalon](https://portal.azure.com/)a **Palo Alto Networks – Captive Portal** alkalmazásintegrációs lapon válassza az Egyszeri **bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezési kapcsolat konfigurálása](common/select-sso.png)

2. Az **Egyszeri bejelentkezési módszer kiválasztása** ablaktáblában válassza az **SAML**lehetőséget.

    ![Egyszeri bejelentkezésválasztó mód](common/select-saml-option.png)

3. Az **Egyszeri bejelentkezés beállítása SAML-lel** ablaktáblán jelölje ki a ceruza **szerkesztése** ikont.

    ![A ceruza szerkesztése ikon](common/edit-urls.png)

4. Az **Alapvető SAML-konfiguráció** ablaktáblán hajtsa végre az alábbi lépéseket:

    ![Palo Alto Networks Captive Portal Basic SAML konfigurációs ablaktábla](common/idp-intiated.png)

   1. Az **Azonosító**mezőbe írja be `https://<customer_firewall_host_name>/SAML20/SP`a mintát tartalmazó URL-címet.

   2. A **Válasz URL-címéhez**adja `https://<customer_firewall_host_name>/SAML20/SP/ACS`meg a mintát tartalmazó URL-címet.

      > [!NOTE]
      > Frissítse a helyőrző értékeket ebben a lépésben a tényleges azonosítóval és a válasz URL-címekkel. A tényleges értékek et a [Palo Alto Networks Captive Portal ügyféltámogatási csapatával](https://support.paloaltonetworks.com/support)veheti fel.

5. Az **SAML aláíró tanúsítvány** csoportban az **Összevonási metaadat-XML (Összevonási metaadatok XML) melletti**területen válassza a Letöltés **lehetőséget.** Mentse a letöltött fájlt a számítógépre.

    ![Az összevonási metaadatok XML-letöltési hivatkozása](common/metadataxml.png)

### <a name="configure-palo-alto-networks-captive-portal-single-sign-on"></a>Konfigurálja a Palo Alto Networks Captive Portal egyszeri bejelentkezést

Ezután állítsa be az egyszeri bejelentkezést a Palo Alto Networks Captive Portal-ban:

1. Egy másik böngészőablakban jelentkezzen be rendszergazdaként a Palo Alto Networks webhelyére.

2. Válassza az **Eszköz** lapot.

    ![A Palo Alto Networks weboldal eszköz lap](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin1.png)

3. A menüben válassza az **SAML identitásszolgáltató**lehetőséget, majd az **Importálás parancsot.**

    ![Az Importálás gomb](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin2.png)

4. Az **SAML-identitásszolgáltató kiszolgálóiprofil importálása** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![A Palo Alto Networks egyszeri bejelentkezéskonfigurálása](./media/paloaltonetworks-captiveportal-tutorial/tutorial_paloaltoadmin_admin3.png)

    1. A **Profilnév**mezőbe írjon be egy nevet, például **az AzureAD-CaptivePortal nevet.**
    
    2. Az **Identitásszolgáltató metaadatai**mellett válassza a **Tallózás gombot.** Válassza ki az Azure Portalon letöltött metadata.xml fájlt.
    
    3. Válassza **az OK gombot.**

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása 

Ezután hozzon létre egy *Britta Simon* nevű tesztfelhasználót az Azure Portalon:

1. Az Azure Portalon válassza az **Azure Active Directory** > **felhasználói** > **minden felhasználó lehetőséget.**

    ![A "Felhasználók és csoportok" és a "Minden felhasználó" linkek](common/users.png)

2. Válassza az **Új felhasználó**lehetőséget.

    ![Az Új felhasználó gomb](common/new-user.png)

3. A **Felhasználó** ablaktáblán hajtsa végre az alábbi lépéseket:

    ![A Felhasználó párbeszédpanel](common/user-properties.png)

    1. A **Név mezőbe**írja be a **BrittaSimon**értéket.
  
    2. A **Felhasználónév**mezőbe írja be **a BrittaSimon\@\<your_company_domain\>**. Például **BrittaSimon\@contoso.com**.

    3. A **Jelszó**mezőbe írja be a jelszót. Javasoljuk, hogy jegyezze fel a megadott jelszót. A jelszó megjelenítéséhez bejelölheti a **Jelszó megjelenítése** jelölőnégyzetet.

    4. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ezután adjon hozzáférést a Palo Alto Networks Captive Portal portálhoz, hogy Britta Simon használhassa az Azure egyszeri bejelentkezést:

1. Az Azure Portalon válassza a **Vállalati alkalmazások** > **minden alkalmazás**lehetőséget.

    ![A Vállalati alkalmazások ablaktábla](common/enterprise-applications.png)

2. Az alkalmazások listájában írja be a **Palo Alto Networks - Captive Portal**( nyelv) lehetőséget, majd válassza ki az alkalmazást.

    ![A Palo Alto Networks - Captive Portal link az alkalmazások listájában](common/all-applications.png)

3. A menüben válassza a **Felhasználók és csoportok lehetőséget.**

    ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

4. Válassza **a Felhasználó hozzáadása**lehetőséget. Ezután a **Hozzárendelés hozzáadása** ablaktáblán válassza a Felhasználók és **csoportok**lehetőséget.

    ![A Hozzárendelés hozzáadása ablaktábla](common/add-assign-user.png)

5. A **Felhasználók és csoportok** ablaktáblán a **Felhasználók** listában válassza **a Britta Simon**elemet. Válassza a **Kiválasztás** lehetőséget.

6. Ha szerepkör-értéket szeretne hozzáadni az SAML-feltételhez, a **Szerepkör kiválasztása** ablaktáblában válassza ki a megfelelő szerepkört a felhasználó számára. Válassza a **Kiválasztás** lehetőséget.

7. A **Hozzárendelés hozzáadása** ablaktáblán válassza a **Hozzárendelés lehetőséget.**

### <a name="create-a-palo-alto-networks-captive-portal-test-user"></a>Hozzon létre egy Palo Alto Networks Captive Portal teszt felhasználó

Ezután hozzon létre egy *Britta Simon* nevű felhasználót a Palo Alto Networks Captive Portal alkalmazásban. Palo Alto Networks Captive Portal támogatja a just-in-time felhasználói kiépítés, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nem kell elvégeznie egyetlen feladatot sem. Ha a felhasználó már nem létezik a Palo Alto Networks Captive Portal, egy új jön létre a hitelesítés után.

> [!NOTE]
> Ha manuálisan szeretne felhasználót létrehozni, forduljon a [Palo Alto Networks Captive Portal ügyféltámogatási csapatához.](https://support.paloaltonetworks.com/support)

### <a name="test-single-sign-on"></a>Az egyszeri bejelentkezés tesztelése 

Palo Alto Networks Captive Portal telepítve van a tűzfal mögött a Windows vm. Ha a Palo Alto Networks Captive Portal portálon szeretné tesztelni az egyszeri bejelentkezést, jelentkezzen be a Windows virtuális gépbe a Távoli asztali protokoll (RDP) használatával. Az RDP-munkamenetben nyisson meg egy böngészőt, és lépjen bármely webhelyre. Megnyílik az SSO URL-címe, és a rendszer kéri a hitelesítést. Ha a hitelesítés befejeződött, hozzáférhet a webhelyekhez.

## <a name="additional-resources"></a>További források

További információ: Az alábbi cikkek:

- [Oktatóanyagok a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
- [Egyszeri bejelentkezés az alkalmazásokba az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
- [Feltételes hozzáférés az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

