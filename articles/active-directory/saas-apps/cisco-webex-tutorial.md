---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Cisco WebEx-találkozókkal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Cisco WebEx-értekezletek között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 26704ca7-13ed-4261-bf24-fd6252e2072b
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/16/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1adda943676eb4918369d3cb8027f559d5dd0f6e
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050017"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex-meetings"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció Cisco WebEx-értekezletekkel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Cisco WebEx-értekezleteket Azure Active Directory (Azure AD) használatával. Ha a Cisco WebEx-értekezleteket az Azure AD-vel integrálja, a következőket teheti:

* A Cisco WebEx-értekezletekhez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Cisco WebEx-üléseire az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Cisco WebEx az egyszeri bejelentkezést (SSO) engedélyezte az előfizetést.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Cisco WebEx-találkozók támogatják **az SP és a identitásszolgáltató** által kezdeményezett SSO-t

* A Cisco WebEx-értekezletek **a** felhasználó üzembe helyezésének időpontját támogatják

## <a name="adding-cisco-webex-meetings-from-the-gallery"></a>Cisco WebEx-értekezletek hozzáadása a katalógusból

A Cisco WebEx-értekezleteknek az Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Cisco WebEx-találkozókat a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Cisco WebEx** -értekezletek kifejezést a keresőmezőbe.
1. Válassza a **Cisco WebEx** -értekezletek lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex-meetings"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Cisco WebEx-értekezletekhez

Konfigurálja és tesztelje az Azure AD SSO-t a Cisco WebEx ülésein egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Cisco WebEx-értekezleteken.

Az Azure AD SSO és a Cisco WebEx-értekezletek konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. **[Cisco WebEx-találkozók egyszeri bejelentkezésének konfigurálása](#configure-cisco-webex-meetings-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Cisco WebEx-értekezletek létrehozása](#create-cisco-webex-meetings-test-user)** – tesztelje a felhasználót –, hogy a B. Simon partnere legyen a Cisco WebEx-találkozókon, amely a felhasználó Azure ad-képviseletéhez van társítva.
3. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Cisco WebEx** -értekezletek alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az alapszintű **SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az alapszintű **SAML-konfiguráció** szakaszban töltse fel a letöltött **szolgáltatói metaadat** -fájlt, és konfigurálja az alkalmazást **identitásszolgáltató** kezdeményezett módban az alábbi lépések végrehajtásával:

    >[!Note]
    >Ekkor megjelenik a szolgáltatói metaadatokat tartalmazó fájl, amelyet később a **Cisco WebEx-értekezletek SSO konfigurálása** című részében talál az oktatóanyagban. 

    a. Kattintson a **metaadatfájl feltöltése**.

    b. Kattintson a **mappa embléma** válassza ki a metaadat-fájlt, és kattintson a **feltöltése**.

    c. A szolgáltatói metaadatok feltöltésének sikeres befejezése után az **azonosító** és a **Válasz URL-** értékei az alapszintű **SAML-konfiguráció** szakaszban automatikusan feltöltve lesznek:

5. Ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni, hajtsa végre a következő lépést:
    
    A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<customername>.webex.com`

5. A Cisco WebEx-értekezletek alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható. Kattintson a **Szerkesztés** ikonra a felhasználói attribútumok párbeszédpanel megnyitásához.

    ![image](common/edit-attribute.png)

6. A fentieken kívül a Cisco WebEx üléseinek alkalmazása néhány további attribútumot vár az SAML-válaszban való visszatéréshez. A felhasználó attribútumai párbeszédpanel felhasználói jogcímek szakaszában a következő lépésekkel adja hozzá az SAML-jogkivonat attribútumát az alábbi táblázatban látható módon: 

    | Name (Név) | Forrás attribútum|
    | ---------------|  --------- |
    |   FirstName    | User. givenName |
    |   LastName    | felhasználó. vezetéknév |
    |   email       | user.mail |
    |   UID    | user.mail |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listából válassza ki az adott sorhoz tartozó attribútum értékét a legördülő listából.

    f. Kattintson a **Save** (Mentés) gombra.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML** -fájlját, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

6. A **Cisco WebEx-találkozók beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza ki **új felhasználó** a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
    1. A **Név** mezőbe írja a következőt: `B.Simon`.  
    1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
    1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
    1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Cisco WebEx-értekezletekhez.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Cisco WebEx**-értekezletek lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

    ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-cisco-webex-meetings-sso"></a>Cisco WebEx-értekezletek – SSO konfigurálása

1. Nyissa meg az URL-címet az adminisztrációs hitelesítő adataival. `https://<customername>.webex.com/admin`

2. Nyissa meg a **gyakori hely beállításait** , és navigáljon az **SSO**-konfigurációhoz.
 
    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial-cisco-webex-11.png)

3. A **WebEx-felügyelet** oldalon hajtsa végre a következő lépéseket:

    ![Egyszeri bejelentkezés konfigurálása](./media/cisco-webex-tutorial/tutorial-cisco-webex-10.png)

    a. Válassza az **SAML 2,0** -as **összevonási protokoll**lehetőséget.

    b. Kattintson az **SAML-Metaadatok importálása** hivatkozásra a metaadatok fájljának feltöltéséhez, amelyet a Azure Portal letöltött.

    c. Az **Exportálás** gombra kattintva letöltheti a szolgáltatói metaadat-fájlt, és feltöltheti azt az alapszintű **SAML-konfigurációs** szakaszban Azure Portal.

    d. A **AuthContextClassRef** szövegmezőbe írja be `urn:oasis:names:tc:SAML:2.0:ac:classes:unspecified` a (z) értéket, és ha engedélyezni szeretné az MFA használatát az Azure ad-ban, írja be a két értéket, például:`urn:oasis:names:tc:SAML:2.0:ac:classes:PasswordProtectedTransport;urn:oasis:names:tc:SAML:2.0:ac:classes:X509`

    e. Válassza az **automatikus fiók létrehozása**lehetőséget.

    >[!NOTE]
    >Az igény szerinti felhasználó üzembe helyezésének engedélyezéséhez ellenőriznie kell az **automatikus fiók létrehozását**. Az SAML-jogkivonat attribútumait is át kell adni az SAML-válaszban.

    f. Kattintson a **Save** (Mentés) gombra.

    >[!NOTE]
    >Ez a konfiguráció csak azoknál az ügyfeleknél használható, akik e-mail-formátumban használják a WebEx UserID-t.

### <a name="create-cisco-webex-meetings-test-user"></a>Cisco WebEx-értekezletek létrehozása tesztelési felhasználó

Ennek a szakasznak a célja, hogy létrehozzon egy B. Simon nevű felhasználót a Cisco WebEx ülésein. A Cisco WebEx-értekezletek támogatják az **igény** szerinti üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ez a szakasz nem tartalmaz műveleti elemeket. Ha egy felhasználó még nem létezik a Cisco WebEx ülésein, akkor a rendszer létrehoz egy újat, amikor megpróbál hozzáférni a Cisco WebEx-értekezletekhez.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Ha a hozzáférési panelen a Cisco WebEx-értekezletek csempére kattint, automatikusan be kell jelentkeznie a Cisco WebEx-üléseire, amelyekhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A ServiceNow kipróbálása az Azure AD-vel](https://aad.portal.azure.com)
