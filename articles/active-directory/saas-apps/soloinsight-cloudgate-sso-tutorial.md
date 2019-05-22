---
title: 'Oktatóanyag: Az Azure Active Directory-integráció Soloinsight-CloudGate SSO-val |} A Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhatja az egyszeri bejelentkezés Soloinsight-CloudGate egyszeri bejelentkezés és az Azure Active Directory között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: celested
ms.assetid: 9263c241-85a4-4724-afac-0351d6275958
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/06/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: c131f034b4a8b5afaa2556c5c8d053b3e6793b4d
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/21/2019
ms.locfileid: "65987218"
---
# <a name="tutorial-integrate-soloinsight-cloudgate-sso-with-azure-active-directory"></a>Oktatóanyag: Soloinsight-CloudGate SSO integrálása az Azure Active Directoryval

Ebben az oktatóanyagban elsajátíthatja a Soloinsight-CloudGate SSO integrálása az Azure Active Directory (Azure AD) lesz. Soloinsight-CloudGate SSO integrálása az Azure ad-vel, akkor a következőket teheti:

* Szabályozza, ki férhet hozzá Soloinsight-CloudGate egyszeri bejelentkezés az Azure AD-ben.
* Engedélyezze a felhasználókat, hogy a rendszer automatikusan bejelentkezve Soloinsight-CloudGate egyszeri bejelentkezés az Azure AD-fiókjukat.
* A fiókok egyetlen központi helyen – az Azure Portalon kezelheti.

SaaS-alkalmazás integráció az Azure ad-vel kapcsolatos további információkért lásd: [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="prerequisites"></a>Előfeltételek

Első lépésként szüksége van a következő elemek:

* Az Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, beszerezheti a egyhónapos ingyenes próbaidőszakot [Itt](https://azure.microsoft.com/pricing/free-trial/).
* Soloinsight-CloudGate SSO egyszeri bejelentkezés (SSO) engedélyezve van az előfizetésben.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban, tesztelése és konfigurálása az Azure AD SSO-t egy tesztkörnyezetben. Soloinsight-CloudGate egyszeri Bejelentkezést támogatja **SP** által kezdeményezett egyszeri bejelentkezés.

## <a name="adding-soloinsight-cloudgate-sso-from-the-gallery"></a>Soloinsight-CloudGate SSO hozzáadása a katalógusból

Soloinsight-CloudGate SSO integrálása az Azure AD beállítása, hozzá kell Soloinsight-CloudGate SSO a galériából a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs ablaktáblán válassza ki a **Azure Active Directory** szolgáltatás.
1. Navigáljon a **vállalati alkalmazások** majd **minden alkalmazás**.
1. Új alkalmazás hozzáadásához válassza **új alkalmazás**.
1. Az a **Hozzáadás a katalógusból** területén írja be a **Soloinsight-CloudGate SSO** kifejezést a keresőmezőbe.
1. Válassza ki **Soloinsight-CloudGate SSO** az eredmények panelen, és vegye fel az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőn.

## <a name="configure-and-test-azure-ad-single-sign-on"></a>Az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása

Konfigurálás és tesztelés az Azure AD SSO Soloinsight-CloudGate SSO-t egy tesztfelhasználót nevű **Britta Simon**. Az SSO működjön kell Soloinsight-CloudGate egyszeri bejelentkezés az Azure AD-felhasználót és a kapcsolódó felhasználó közötti hivatkozás kapcsolatot hozhat létre.

Soloinsight-CloudGate SSO-val az Azure AD egyszeri bejelentkezés tesztelése és konfigurálása, hajtsa végre a következő építőelemeket:

1. **[Az Azure AD SSO konfigurálása](#configure-azure-ad-sso)**  ahhoz, hogy ez a funkció használatát a felhasználók számára.
2. **[Soloinsight-CloudGate egyszeri bejelentkezést](#configure-soloinsight-cloudgate-sso)**  alkalmazás oldalán az egyszeri bejelentkezési beállításainak konfigurálására.
3. **[Hozzon létre egy Azure ad-ben tesztfelhasználót](#create-an-azure-ad-test-user)**  az Azure AD egyszeri bejelentkezés az Britta Simon teszteléséhez.
4. **[Rendelje hozzá az Azure ad-ben tesztfelhasználó](#assign-the-azure-ad-test-user)**  Britta Simon használata az Azure AD egyszeri bejelentkezés engedélyezéséhez.
5. **[Hozzon létre Soloinsight-CloudGate SSO tesztfelhasználót](#create-soloinsight-cloudgate-sso-test-user)**  van egy megfelelője a Britta Simon Soloinsight-CloudGate SSO, amely kapcsolódik az Azure AD felhasználói ábrázolása.
6. **[Egyszeri bejelentkezés tesztelése](#test-sso)**  ellenőrzése, hogy működik-e a konfiguráció.

### <a name="configure-azure-ad-sso"></a>Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD egyszeri bejelentkezés engedélyezése az Azure Portalon.

1. Az a [az Azure portal](https://portal.azure.com/), a a **Soloinsight-CloudGate SSO** alkalmazás integráció lapon keresse meg a **kezelése** szakaszt, és válassza **egyszeri bejelentkezés**.
1. Az a **egyszeri bejelentkezési módszer** lapra, jelölje be **SAML**.
1. Az a **állítsa be egyszeri bejelentkezést az SAML** lap, kattintson a Szerkesztés/toll ikonra a **alapszintű SAML-konfigurációja** beállításait módosíthatja.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az a **alapszintű SAML-konfigurációja** lap, adja meg az értékeket a következő mezőket:

    1. Az a **bejelentkezési URL-cím** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<SUBDOMAIN>.sigateway.com/login`

    1. Az a **azonosító (entityid)** szövegmezőbe írja be a következő minta használatával URL-cím: `https://<SUBDOMAIN>.sigateway.com/process/sso`

   > [!NOTE]
   > Ezek a értékei nem valódi. Frissítse a tényleges bejelentkezési URL-címet és a később ismertetett azonosító ezeket az értékeket a **konfigurálása Soloinsight-CloudGate SSO egyszeri bejelentkezés** az oktatóanyag szakaszában.

1. A a **állítsa be egyszeri bejelentkezést az SAML** lap a **SAML-aláíró tanúsítvány** területén található **tanúsítvány (Base64)** válassza **letöltése** töltse le a tanúsítványt, és menti azt a számítógépet.

   ![A tanúsítvány letöltési hivatkozás](common/certificatebase64.png)

1. Az a **Soloinsight-CloudGate egyszeri bejelentkezés beállítása** területén másolja a megfelelő URL-címe szerint.

   ![Másolja a konfigurációs URL-címek](common/copy-configuration-urls.png)

### <a name="configure-soloinsight-cloudgate-sso"></a>Soloinsight-CloudGate egyszeri bejelentkezés konfigurálása

1. Belül Soloinsight-CloudGate egyszeri Bejelentkezéssel konfigurálásának automatizálásához, telepítenie kell **saját alkalmazások biztonságos bejelentkezési böngészőbővítmény** kattintva **a bővítmény telepítése**.

    ![Saját alkalmazások kiterjesztése](common/install-myappssecure-extension.png)

2. A felvett bővítmény a böngészőre, kattintson a **telepítő Soloinsight-CloudGate egyszeri bejelentkezés** fog irányítja át a Soloinsight-CloudGate SSO-alkalmazás. Itt adja meg a rendszergazdai hitelesítő adataival bejelentkezni Soloinsight-CloudGate egyszeri Bejelentkezést. A webböngésző-bővítmény automatikusan konfigurálja az alkalmazást, és 3 – 8. lépések automatizálásához.

    ![Konfiguráció beállítása](common/setup-sso.png)

3. Szeretne Soloinsight-CloudGate egyszeri bejelentkezés manuális beállítása, ha nyisson meg egy új böngészőablakban, és jelentkezzen be rendszergazdaként vállalati Soloinsight-CloudGate SSO webhelyét, és hajtsa végre az alábbi lépéseket:

4. Alapszintű SAML konfigurálása során az Azure Portalon beillesztésre váró értékek beszerzéséhez jelentkezzen be a hitelesítő adatok használatával CloudGate webes portálon, majd elérni az SSO-beállításokat, amelyeket a következő elérési úton található **Kezdőlap > Administration > Rendszerbeállítások > Általános**.

    ![CloudGate SSO Settings](./media/soloinsight-cloudgate-sso-tutorial/sso-main-settings.png)

5. **SAML-ügyfél URL-címe**

    * Elleni elérhető hivatkozásokra kattintva másolja a **Saml ügyfél URL-címe** és a **átirányítási URL-cím** mezőket, és illessze be őket az Azure Portalon **alapszintű SAML-konfigurációja** akövetkezőszakaszban **Azonosító (entityid)** és **válasz URL-cím** mezők jelölik.

        ![SAMLIdentifier](./media/soloinsight-cloudgate-sso-tutorial/saml-identifier.png)

6. **SAML-aláíró tanúsítvány**

    * Nyissa meg a tanúsítvány (Base64) fájl, a SAML-aláíró tanúsítvány az Azure Portalról letöltött listák forrását, és kattintson rá a jobb gombbal. Válasszon **Szerkesztés a Jegyzettömb ++** lehetőséget a listából. 

        ![SAMLcertificate](./media/soloinsight-cloudgate-sso-tutorial/certificate-file.png)

    * Másolja a tartalmat a tanúsítvány (Base64) Jegyzettömb ++ fájlban.

        ![Tanúsítvány másolása](./media/soloinsight-cloudgate-sso-tutorial/certificate-copy.png)

    * Illessze be a CloudGate webes portál egyszeri bejelentkezési beállításainak **tanúsítvány** mezőbe, majd kattintson a Mentés gombra.

        ![Tanúsítvány-portál](./media/soloinsight-cloudgate-sso-tutorial/certificate-portal.png)

7. **Alapértelmezett csoport**

    * Válassza ki **üzleti felügyeleti** a legördülő listából a **alapértelmezett csoport** CloudGate webes portálon való lehetőség

        ![Alapértelmezett csoport](./media/soloinsight-cloudgate-sso-tutorial/default-group.png)

8. **AD-azonosítóját, valamint a bejelentkezési URL-címe**

    * A másolt **bejelentkezési URL-cím** az Azure Portalról **Soloinsight-CloudGate egyszeri bejelentkezés beállítása** konfigurációk a következők megadni a CloudGate webes portál egyszeri bejelentkezési beállítások szakaszban.

    * Illessze be a **bejelentkezési URL-cím** Azure Portalról CloudGate webes portálon való hivatkozás **AD bejelentkezési URL-cím** mező.

    * Illessze be a **az Azure AD-azonosító** Azure Portalról CloudGate webes portálon való hivatkozás **AD azonosító** mező

        ![Ad-bejelentkezés](./media/soloinsight-cloudgate-sso-tutorial/ad-login.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban az Azure Portalon Britta Simon nevű tesztfelhasználó fog létrehozni.

1. Az Azure Portal bal oldali panelén válassza **Azure Active Directory**válassza **felhasználók**, majd válassza ki **minden felhasználó**.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. Az a **felhasználói** tulajdonságok, kövesse az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `Britta Simon`.  
   1. Az a **felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `BrittaSimon@contoso.com`.
   1. Válassza ki a **Show jelszó** jelölje be a jelölőnégyzetet, és jegyezze fel a megjelenített érték a **jelszó** mezőbe.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban Britta Simon által biztosított hozzáférés Soloinsight-CloudGate SSO Azure egyszeri bejelentkezés használatához engedélyeznie kell.

1. Az Azure Portalon válassza ki a **vállalati alkalmazások**, majd válassza ki **minden alkalmazás**.
1. Az alkalmazások listájában jelölje ki a **Soloinsight-CloudGate SSO**.
1. Az alkalmazás áttekintése lapon keresse meg a **kezelés** szakaszt, és válassza **felhasználók és csoportok**.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza ki **felhasználó hozzáadása**, majd **felhasználók és csoportok** a a **hozzárendelés hozzáadása** párbeszédpanel.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. Az a **felhasználók és csoportok** párbeszédablakban válassza **Britta Simon** a felhasználók listájából, majd kattintson a **kiválasztása** gombra a képernyő alján.
1. Ha a SAML helyességi feltétel, a szerepkör értéket vár a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználóhoz a listából, és kattintson a **kiválasztása** gombra a képernyő alján.
1. Az a **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelése** gombra.

### <a name="create-soloinsight-cloudgate-sso-test-user"></a>Soloinsight-CloudGate SSO tesztfelhasználó létrehozása

Hozzon létre egy tesztfelhasználót, jelölje be **alkalmazottak** a főmenüből a CloudGate webes portálon, és töltse ki az új hozzáadása alkalmazott képernyőt. A szolgáltató szintje, amely hozzá kell rendelni a tesztfelhasználó számára **üzleti felügyeleti** kattintson a **létrehozás** után az összes kötelező mezőt ki vannak töltve.

![Alkalmazott teszt](./media/soloinsight-cloudgate-sso-tutorial/employee-test.png)

### <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

A Soloinsight-CloudGate SSO csempe kiválasztásakor a hozzáférési panelen, kell lennie automatikusan bejelentkezett a Soloinsight-CloudGate SSO, amelynek beállítása egyszeri Bejelentkezést. A hozzáférési panelen kapcsolatos további információkért lásd: [Bevezetés a hozzáférési Panel használatába](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [SaaS-alkalmazások integrálása az Azure Active Directory foglalkozó oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az az Azure Active Directory feltételes hozzáférés?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)