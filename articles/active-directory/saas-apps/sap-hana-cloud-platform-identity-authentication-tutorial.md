---
title: 'Oktatóanyag: Azure Active Directory integráció az SAP Cloud platform Identity Authentication szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és az SAP Cloud platform Identity Authentication között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 1c1320d1-7ba4-4b5f-926f-4996b44d9b5e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: tutorial
ms.date: 04/28/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: db852254d7f6ec9c0cb76151fa23e58e2d97bd2c
ms.sourcegitcommit: 999ccaf74347605e32505cbcfd6121163560a4ae
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2020
ms.locfileid: "82981765"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az SAP Cloud platform Identity Authentication szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az SAP Cloud platform identitás-hitelesítését Azure Active Directory (Azure AD) használatával. Ha integrálja az SAP Cloud platform identitás-hitelesítését az Azure AD-vel, a következőket teheti:

* Az SAP Cloud platform Identity Authentication szolgáltatáshoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek az SAP Cloud platform Identity Authentication szolgáltatásba az Azure AD-fiókkal.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* SAP Cloud platform Identity Authentication egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban egy tesztkörnyezetben konfigurálja és teszteli az Azure AD egyszeri bejelentkezést.

* Az SAP Cloud platform Identity Authentication támogatja az **SP** és a **identitásszolgáltató** által kezdeményezett SSO-t
* Miután konfigurálta az SAP Cloud platform identitás-hitelesítését, kikényszerítheti a munkamenet-vezérlést, amely a szervezet bizalmas adatainak valós idejű kiszűrése és beszivárgását biztosítja. A munkamenet-vezérlőelemek kiterjeszthetők a feltételes hozzáférésből. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

Mielőtt belemerül a technikai részletekbe, fontos megérteni a megtekinteni kívánt fogalmakat. Az SAP Cloud platform Identity Authentication és Active Directory összevonási szolgáltatások (AD FS) lehetővé teszi, hogy az egyszeri bejelentkezést az Azure AD (identitásszolgáltató) által védett alkalmazásokban vagy szolgáltatásokban implementálja az SAP Cloud platform Identity Authentication által védett SAP-alkalmazásokkal és-szolgáltatásokkal.

Jelenleg az SAP Cloud platform Identity Authentication proxy-identitás-szolgáltatóként működik az SAP-alkalmazásokban. A Azure Active Directory ebben a beállításban a vezető identitás-szolgáltatóként működik. 

A következő ábra ezt a kapcsolatot szemlélteti:

![Azure AD-tesztkörnyezet létrehozása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Ezzel a beállítással az SAP Cloud platform Identity Authentication bérlője megbízható alkalmazásként van konfigurálva Azure Active Directoryban.

Az ezen a módon védelemmel ellátni kívánt SAP-alkalmazásokat és-szolgáltatásokat az SAP Cloud platform Identity Authentication felügyeleti konzolján kell konfigurálni.

Ezért az SAP-alkalmazásokhoz és-szolgáltatásokhoz való hozzáférés engedélyezésének engedélyezése szükséges az SAP Cloud platform Identity Authentication (nem pedig a Azure Active Directory) számára.

Ha az SAP Cloud platform Identity Authentication alkalmazást a Azure Active Directory piactéren konfigurálja, nem kell külön jogcímeket vagy SAML-jogcímeket konfigurálnia.

> [!NOTE]
> A két fél jelenleg csak a webes egyszeri bejelentkezést tesztelte. Az alkalmazás-API vagy API-to-API kommunikációhoz szükséges folyamatoknak működniük kell, de még nem tesztelték őket. A következő tevékenységek során lesznek tesztelve.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>SAP Cloud platform identitás-hitelesítés hozzáadása a katalógusból

Az SAP Cloud platform identitás-hitelesítés Azure AD-be való integrálásának konfigurálásához hozzá kell adnia az SAP Cloud platform Identity Authentication hitelesítést a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be az **SAP Cloud platform Identity Authentication** kifejezést a keresőmezőbe.
1. Válassza az **SAP Cloud platform Identity Authentication** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-platform-identity-authentication"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése az SAP Cloud platform identitás-hitelesítéséhez

Konfigurálja és tesztelje az Azure AD SSO-t az SAP Cloud platform Identity Authentication használatával egy **B. Simon**nevű teszt felhasználó segítségével. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az SAP Cloud platform Identity Authentication szolgáltatásban.

Az Azure AD SSO az SAP Cloud platform Identity Authentication használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[SAP Cloud platform Identity Authentication SSO konfigurálása](#configure-sap-cloud-platform-identity-authentication-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre egy SAP Cloud platform Identity Authentication test User](#create-sap-cloud-platform-identity-authentication-test-user)** -to have a B. Simon az SAP Cloud platform Identity Authentication, amely a felhasználó Azure ad-képviseletéhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **SAP Cloud platform Identity Authentication** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Ha az **alapszintű SAML-konfigurációt** szeretné **identitásszolgáltató**módban konfigurálni, hajtsa végre a következő lépéseket:

    ![SAP Cloud platform Identity Authentication tartomány és URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:`<IAS-tenant-id>.accounts.ondemand.com`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Az értékek beszerzéséhez lépjen kapcsolatba az [SAP Cloud platform Identity Authentication ügyfél-támogatási csapatával](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) . Ha nem érti az azonosító értékét, olvassa el az SAP Cloud platform Identity Authentication dokumentációját a [bérlői SAML 2,0-konfigurációról](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html).

5. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP**-kezdeményezésű módban szeretné konfigurálni:

    ![SAP Cloud platform Identity Authentication tartomány és URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Ez az érték nem valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Használja az adott üzleti alkalmazás bejelentkezési URL-címét. Ha kétségei vannak, lépjen kapcsolatba az [SAP Cloud platform Identity Authentication ügyfél-támogatási csapatával](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) .

1. Az SAP Cloud platform Identity Authentication alkalmazás meghatározott formátumban várja az SAML-jogcímeket, így egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül az SAP Cloud platform Identity Authentication alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name | Forrás attribútum|
    | ---------------| --------------- |
    | firstName | User. givenName |

8. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban kattintson a **Letöltés** gombra a **metaadatok XML** -fájljának a megadott beállítások alapján történő letöltéséhez, és mentse azt a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

9. Az **SAP Cloud platform Identity Authentication beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények szerint.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure ad-azonosító

    c. Kijelentkezési URL-cím

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőben adja meg a username@companydomain.extensionnevet. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás**gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezést, ha hozzáférést biztosít az SAP Cloud platform Identity Authentication szolgáltatáshoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **SAP Cloud platform Identity Authentication**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>SAP Cloud platform Identity Authentication – SSO konfigurálása

1. Az alkalmazáshoz tartozó egyszeri bejelentkezés beszerzéséhez nyissa meg az SAP Cloud platform Identity Authentication felügyeleti konzolt. Az URL-cím a következő mintával rendelkezik: `https://<tenant-id>.accounts.ondemand.com/admin`. Ezután olvassa el az SAP Cloud platform Identity Authentication dokumentációját a [Microsoft Azure ad integrációjában](https://developers.sap.com/tutorials/cp-ias-azure-ad.html).

2. A Azure Portal válassza a **Mentés** gombot.

3. Folytassa a következőket csak abban az esetben, ha az SSO-t egy másik SAP-alkalmazáshoz szeretné hozzáadni és engedélyezni. Ismételje meg a következő szakaszt: **SAP Cloud platform Identity Authentication hozzáadása**a katalógusból.

4. A Azure Portal az **SAP Cloud platform Identity Authentication** Application Integration lapon válassza a **csatolt bejelentkezés**lehetőséget.

    ![Csatolt bejelentkezés konfigurálása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Mentse a konfigurációt.

> [!NOTE]
> Az új alkalmazás kihasználja az előző SAP-alkalmazás egyszeri bejelentkezési konfigurációját. Győződjön meg arról, hogy ugyanazt a vállalati identitás-szolgáltatót használja az SAP Cloud platform Identity Authentication felügyeleti konzolján.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>SAP Cloud platform identitás-hitelesítési teszt felhasználó létrehozása

Nem kell felhasználót létrehoznia az SAP Cloud platform Identity Authentication szolgáltatásban. Az Azure AD felhasználói tárolójában lévő felhasználók használhatják az egyszeri bejelentkezés funkciót.

Az SAP Cloud platform Identity Authentication támogatja az identitás-összevonási lehetőséget. Ez a beállítás lehetővé teszi, hogy az alkalmazás meggyőződjön arról, hogy a vállalati identitás-szolgáltató által hitelesített felhasználók léteznek-e az SAP Cloud platform Identity Authentication felhasználói tárolójában.

Az identitás-összevonás beállítás alapértelmezés szerint le van tiltva. Ha az identitás-összevonás engedélyezve van, csak az SAP Cloud platform Identity Authentication szolgáltatásban importált felhasználók férhetnek hozzá az alkalmazáshoz.

További információ arról, hogyan engedélyezheti vagy tilthatja le az identitás-összevonást az SAP Cloud platform Identity Authentication használatával: "az identitás-összevonás engedélyezése az SAP Cloud platform Identity Authentication szolgáltatással" című részt az [Identity összevonás konfigurálása az SAP Cloud platform Identity Authentication felhasználói tárolójával](https://help.sap.com/viewer/6d6d63354d1242d185ab4830fc04feb1/Cloud/c029bbbaefbf4350af15115396ba14e2.html)című témakörben.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a SAP Cloud platform Identity Authentication csempére kattint, automatikusan be kell jelentkeznie az SAP Cloud platform Identity Authentication szolgáltatásba, amelyhez be kell állítania az SSO-t. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Az SAP Cloud platform identitás-hitelesítésének kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Az SAP Cloud platform identitás-hitelesítésének biztosítása speciális láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)