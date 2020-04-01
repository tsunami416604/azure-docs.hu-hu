---
title: 'Oktatóanyag: Az Azure Active Directory integrációja az SAP Cloud Platform identitáshitelesítésével | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az SAP Cloud Platform-identitás-hitelesítés között.
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
ms.date: 01/16/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 95be73bd125c124409585a478fa9707e7b6a2ac2
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "76289067"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-sap-cloud-platform-identity-authentication"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az SAP Cloud Platform identitáshitelesítésével

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az SAP Cloud Platform identitás-hitelesítését az Azure Active Directoryval (Azure AD). Ha integrálja az SAP Cloud Platform identitás-hitelesítését az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az SAP cloud platform identitás-hitelesítéshez.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve az SAP Cloud Platform identitás-hitelesítés az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* SAP Cloud Platform Identity Authentication egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD egyszeri bejelentkezést egy tesztkörnyezetben.

* Az SAP cloud platform identitáshitelesítése támogatja az **SP** és **az IDP** által kezdeményezett sso-t
* Az SAP cloud platform identitás-hitelesítésének konfigurálása után kényszerítheti a munkamenet-vezérlőket, amelyek valós időben védik a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlők a feltételes hozzáféréstől származnak. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

Mielőtt belevetné magát a technikai részletekbe, létfontosságú, hogy megértse azokat a fogalmakat, amelyeket meg fog nézni. Az SAP cloud platform identitás-hitelesítés és az Active Directory összevonási szolgáltatások lehetővé teszik, hogy az Azure AD által védett alkalmazások vagy szolgáltatások között (IdP-ként) olyan SAP-alkalmazásokkal és szolgáltatásokkal valósítsa meg az SSO-t, amelyeket az SAP felhőplatform-identitás-hitelesítés véd.

Jelenleg az SAP cloud platform identitás-hitelesítése az SAP-alkalmazások proxyidentitás-szolgáltatójaként működik. Az Azure Active Directory viszont a vezető identitásszolgáltatóként működik ebben a beállításban. 

Az alábbi ábra ezt a kapcsolatot mutatja be:

![Azure AD-tesztfelhasználó létrehozása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/architecture-01.png)

Ezzel a beállítással az SAP Cloud Platform identity authentication bérlő megbízható alkalmazásként van konfigurálva az Azure Active Directoryban.

Az ilyen módon védeni kívánt összes SAP-alkalmazás és szolgáltatás később konfigurálva lesz az SAP Cloud Platform Identity Authentication felügyeleti konzolon.

Ezért az SAP-alkalmazásokhoz és -szolgáltatásokhoz való hozzáférés engedélyezésének engedélyezésére vonatkozó engedélyt az SAP Cloud Platform-identitáshitelesítésben kell megadni (szemben az Azure Active Directoryval).

Az SAP Cloud Platform identity authentication alkalmazásként való konfigurálásával az Azure Active Directory piactéren keresztül nem kell konfigurálnia az egyes jogcímeket vagy SAML-állításokat.

> [!NOTE]
> Jelenleg csak a webes SSO tesztelték mindkét fél. Az Alkalmazás-API vagy API-ból API-ba kommunikációhoz szükséges folyamatoknak működniük kell, de még nem lettek tesztelve. Ezeket a későbbi tevékenységek során tesztelik.

## <a name="adding-sap-cloud-platform-identity-authentication-from-the-gallery"></a>SAP felhőplatform-identitáshitelesítés hozzáadása a katalógusból

Az SAP cloud platform identitás-hitelesítésének az Azure AD-be való integrálásához hozzá kell adnia az SAP Cloud Platform identitás-hitelesítést a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a katalógusból szakaszban** írja be az **SAP cloud platform identitásának hitelesítése** a keresőmezőbe.
1. Válassza ki az **SAP cloud platform identitás-hitelesítés** az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.


## <a name="configure-and-test-azure-ad-single-sign-on-for-sap-cloud-platform-identity-authentication"></a>Konfigurálja és tesztelje az Azure AD egyszeri bejelentkezését az SAP felhőplatform-identitáshitelesítéshez

Konfigurálja és tesztelje az Azure AD SSO-t az SAP cloud platform identitáshitelesítésével egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az SAP Cloud Platform Identity Authentication.For SSO to work, you need to establish a link relationship between a Azure AD user and the related user in SAP Cloud Platform Identity Authentication.

Az Azure AD SSO SAP-felhőalapú platformidentitás-hitelesítéssel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    * **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    * **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja az SAP Cloud Platform Identity Authentication SSO-t](#configure-sap-cloud-platform-identity-authentication-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    * **[Hozzon létre SAP Cloud Platform Identity Authentication teszt felhasználó](#create-sap-cloud-platform-identity-authentication-test-user)** – egy megfelelője B.Simon az SAP Cloud Platform identitás-hitelesítés, amely kapcsolódik az Azure AD felhasználói ábrázolása.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **SAP Cloud Platform identity Authentication alkalmazásintegrációs** lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **Egyszerű SAML-konfiguráció** szakaszban, ha **IDP**által kezdeményezett módban szeretne konfigurálni, hajtsa végre a következő lépéseket:

    ![AZ SAP felhőalapú platformidentitás-hitelesítési tartomány és az URL-címek egyszeri bejelentkezési adatai](common/idp-intiated.png)

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`<IAS-tenant-id>.accounts.ondemand.com`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<IAS-tenant-id>.accounts.ondemand.com/saml2/idp/acs/<IAS-tenant-id>.accounts.ondemand.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval és a Válasz URL-címével. Lépjen kapcsolatba az [SAP Cloud Platform identity authentication ügyfél támogatási csapatával,](https://cloudplatform.sap.com/capabilities/security/trustcenter.html) hogy megkapja ezeket az értékeket. Ha nem érti az azonosító értékét, olvassa el az SAP Cloud Platform Identity Authentication dokumentációját a [bérlői SAML 2.0 konfigurációról.](https://help.hana.ondemand.com/cloud_identity/frameset.htm?e81a19b0067f4646982d7200a8dab3ca.html)

5. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP**-initiated módban kívánja konfigurálni:

    ![AZ SAP felhőalapú platformidentitás-hitelesítési tartomány és az URL-címek egyszeri bejelentkezési adatai](common/metadata-upload-additional-signon.png)

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`{YOUR BUSINESS APPLICATION URL}`

    > [!NOTE]
    > Ez az érték nem valós. Frissítse ezt az értéket a tényleges bejelentkezési URL-címmel. Kérjük, használja a konkrét üzleti alkalmazás Bejelentkezési URL-t. Ha kétségei vannak, forduljon az [SAP Cloud Platform Identity Authentication Ügyfél támogatási csapatához.](https://cloudplatform.sap.com/capabilities/security/trustcenter.html)

1. Az SAP Cloud Platform Identity Authentication alkalmazás az SAML-állításokat egy adott formátumban várja, amely megköveteli, hogy egyéni attribútumleképezéseket adjon hozzá az SAML tokenattribútum-konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül az SAP Cloud Platform identity Authentication alkalmazás azt várja, hogy néhány további attribútumok at kell átadni saml válasz, amely az alábbiakban látható. Ezek az attribútumok is előre ki vannak töltve, de áttekintheti őket a követelmények nek megfelelően.

    | Név | Forrás attribútuma|
    | ---------------| --------------- |
    | firstName | user.givenname |

8. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az **SAML aláíró tanúsítvány csoportjában** kattintson a **Letöltés** gombra a **metaadat-XML** letöltéséhez a megadott beállításokból a követelménynek megfelelően, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

9. Az **SAP cloud platform identitás-hitelesítésbeállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény nek megfelelően.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

    a. Bejelentkezési URL

    b. Azure-hirdetésazonosító

    c. Kijelentkezés URL-címe

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban egy tesztfelhasználót hoz létre az Azure Portalon B.Simon néven.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd válassza az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja username@companydomain.extensionbe a mezőt. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson **a Létrehozás gombra.**

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés az SAP Cloud Platform identity authentication használatával hozzáférést biztosít.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **SAP cloud platform identitáshitelesítése**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-sap-cloud-platform-identity-authentication-sso"></a>SAP felhőplatform-identitás-hitelesítési szolgáltató konfigurálása

1. Az alkalmazáshoz konfigurált SSO-hoz keresse fel az SAP Cloud Platform Identity Authentication felügyeleti konzolját. Az URL-cím a `https://<tenant-id>.accounts.ondemand.com/admin`következő mintával rendelkezik: . Ezután olvassa el az SAP Cloud Platform identity authentication dokumentációját [a Microsoft Azure AD-vel való integrációkor.](https://help.hana.ondemand.com/cloud_identity/frameset.htm?626b17331b4d4014b8790d3aea70b240.html)

2. Az Azure Portalon válassza a **Mentés** gombot.

3. Csak akkor folytassa az alábbiakkal, ha egy másik SAP-alkalmazáshoz szeretne sso-t hozzáadni és engedélyezni. Ismételje meg a lépéseket az **SAP felhőplatform-identitáshitelesítés hozzáadása a katalógusból című szakaszban.**

4. Az Azure Portalon az **SAP Cloud Platform identitás-hitelesítési alkalmazásintegrációs** lapján válassza a Csatolt **bejelentkezés**lehetőséget.

    ![Csatolt bejelentkezés konfigurálása](./media/sap-hana-cloud-platform-identity-authentication-tutorial/linked_sign_on.png)

5. Mentse a konfigurációt.

> [!NOTE]
> Az új alkalmazás az előző SAP-alkalmazás egyszeri bejelentkezési konfigurációját használja. Győződjön meg arról, hogy ugyanazokat a vállalati identitásszolgáltatókat használja az SAP Cloud Platform Identity Authentication felügyeleti konzolon.

### <a name="create-sap-cloud-platform-identity-authentication-test-user"></a>SAP felhőplatform-identitás-hitelesítési tesztfelhasználó létrehozása

Nem kell létrehoznia egy felhasználót az SAP Cloud Platform identitás-hitelesítés. Az Azure AD felhasználói áruházban lévő felhasználók használhatják az SSO-funkciót.

Az SAP cloud platform identitáshitelesítése támogatja az identitás-összevonási beállítást. Ez a beállítás lehetővé teszi, hogy az alkalmazás ellenőrizze, hogy a felhasználók, akik a vállalati identitásszolgáltató által hitelesített létezik a felhasználói tárolóban az SAP Cloud Platform Identity Authentication.

Az Identitásösszevonás beállítás alapértelmezés szerint le van tiltva. Ha az identitás-összevonás engedélyezve van, csak az SAP felhőplatform-identitás-hitelesítésben importált felhasználók férhetnek hozzá az alkalmazáshoz.

Az identitás-összevonás SAP-alapú felhőalapú platformidentitás-hitelesítéssel való engedélyezéséről és letiltásáról az [Identitás-összevonás konfigurálása az SAP felhőplatform-identitás-hitelesítés felhasználói tárolójával](https://help.hana.ondemand.com/cloud_identity/frameset.htm?c029bbbaefbf4350af15115396ba14e2.html)című témakörben talál további információt az "Identitás-összevonás engedélyezése AZ SAP-platform identitáshitelesítésével" című részében.

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Ha a hozzáférési panelen az SAP cloud platform identitás-hitelesítési csempéjére kattint, automatikusan be kell jelentkeznie az SAP felhőplatform-identitás-hitelesítésbe, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az SAP felhőplatform-identitás-hitelesítést az Azure AD-vel](https://aad.portal.azure.com/)

- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)

- [Az SAP cloud platform identitáshitelesítésének védelme fejlett láthatósággal és vezérlőkkel](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
