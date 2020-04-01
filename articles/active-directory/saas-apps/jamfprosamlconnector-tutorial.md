---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja a Jamf Pro-val | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és a Jamf Pro között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 35e86d08-c29e-49ca-8545-b0ff559c5faf
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/11/2020
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1d83dbe756e8e6acdb58861ac359801bc13a63c4
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "77373200"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>Oktatóanyag: Az Azure Active Directory SSO-integrációja a Jamf Pro-val

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja a Jamf Pro-t az Azure Active Directoryval (Azure AD). Ha integrálja a Jamf Pro-t az Azure AD-vel, a következőket teheti:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a Jamf Pro-hoz.
* Automatikusan jelentkezzen be a felhasználók Jamf Pro az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen: az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el az Egyszeri bejelentkezés az Azure Active Directoryval című [témakört.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.
* A Jamf Pro-előfizetés, amely egyszeri bejelentkezés (SSO) engedélyezve van.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben. 

* A Jamf Pro támogatja az **SP által kezdeményezett** és az IdP által kezdeményezett egyszeri **szolgáltatót.**
* A Jamf Pro konfigurálása után kényszerítheti a Munkamenet-vezérlést, amely valós időben védi a szervezet bizalmas adatainak kiszivárgását és beszivárgását. A munkamenet-vezérlés a feltételes hozzáférésből terjed. [Megtudhatja, hogy miként kényszerítheti ki a munkamenet-vezérlést a Microsoft Cloud App Security alkalmazással](https://docs.microsoft.com/cloud-app-security/proxy-deployment-aad)

## <a name="add-jamf-pro-from-the-gallery"></a>Jamf Pro hozzáadása a galériából

A Jamf Pro Azure AD-be való integrációjának konfigurálásához hozzá kell adnia a Jamf Pro-t a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) egy munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókjával.
1. A bal oldali ablaktáblában válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások**lehetőséget, és válassza a Minden **alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be a *Jamf Pro* kifejezést a keresőmezőbe.
1. Válassza a **Jamf Pro** elemet az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>Az SSO konfigurálása és tesztelése az Azure AD-ben a Jamf Pro-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a Jamf Pro-val egy B.Simon nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó a Jamf Pro-ban.

Ebben a szakaszban konfigurálhatja és tesztelheti az Azure AD SSO-t a Jamf Pro-val.

1. [Konfigurálja az SSO-t az Azure AD-ben,](#configure-sso-in-azure-ad) hogy a felhasználók használhassák ezt a funkciót.
    1. [Hozzon létre egy Azure AD-tesztfelhasználót](#create-an-azure-ad-test-user) az Azure AD SSO b.Simon-fiókkal való teszteléséhez.
    1. [Rendelje hozzá az Azure AD-teszt felhasználóját,](#assign-the-azure-ad-test-user) hogy B.Simon használhatja az SSO-t az Azure AD-ben.
1. [Konfigurálja az SSO-t a Jamf Pro-ban](#configure-sso-in-jamf-pro) az alkalmazás oldali SSO-beállítások konfigurálásához.
    1. [Hozzon létre egy Jamf Pro tesztfelhasználót,](#create-a-jamf-pro-test-user) hogy b.Simon megfelelője legyen a Jamf Pro-ban, amely a felhasználó Azure AD-ábrázolásához kapcsolódik.
1. [Tesztelje az sso konfigurációt,](#test-the-sso-configuration) hogy ellenőrizze, hogy a konfiguráció működik-e.

## <a name="configure-sso-in-azure-ad"></a>Az SSO konfigurálása az Azure AD-ben

Ebben a szakaszban engedélyezi az Azure AD SSO az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)a **Jamf Pro** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza **az Egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon válassza az **egyszerű SAML-konfiguráció** tollikonját a beállítások szerkesztéséhez.

   ![Az Egyszerű SAML-konfiguráció lap szerkesztése.](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IdP által kezdeményezett** módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet, amely a következő képletet használja:`https://<subdomain>.jamfcloud.com/saml/metadata`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet, amely a következő képletet használja:`https://<subdomain>.jamfcloud.com/saml/SSO`

1. Válassza **a További URL-címek beállítása**lehetőséget. Ha az alkalmazást SP által kezdeményezett módban szeretné **konfigurálni,** a **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet, amely a következő képletet használja:`https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. A tényleges azonosító értékét a Jamf Pro portál **Egyszeri bejelentkezés** szakaszából kapja meg, amelyet az oktatóanyag későbbi részében ismertetünk. Kinyerheti a tényleges altartomány értékét az azonosító értékéből, és ezt az altartomány-információt használhatja bejelentkezési URL-címként és válasz URL-címként. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható képletek is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon nyissa meg az **SAML aláíró tanúsítvány szakaszt,** válassza a **másolás** gombot az **Alkalmazásösszevonás metaadataiurl-címének**másolásához, majd mentse a számítógépre.

    ![Az SAML aláíró tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy tesztfelhasználót az Azure Portalon B.Simon nevű.

1. Az Azure Portal bal oldali ablaktáblájában válassza az **Azure Active Directory**lehetőséget, válassza a **Felhasználók**lehetőséget, majd az **Összes felhasználó**lehetőséget.
1. Válassza az **Új felhasználó** lehetőséget a képernyő tetején.
1. A **Felhasználói** tulajdonságok csoportban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.
   1. A **Felhasználónév** mezőbe írja be a [name]@[companydomain] értéket. [kiterjesztés]. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **Jelszó megjelenítése** jelölőnégyzetet, majd írja le a **Jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-tesztfelhasználó hozzárendelése

Ebben a szakaszban hozzáférést biztosít B.Simon számára a Jamf Pro-hoz.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza a **Jamf Pro**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása gomb kijelölése](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a Felhasználók listában a **B.Simon** elemet, majd a képernyő alján található **Kijelölés** gombot.
1. Ha az SAML-feltételben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört. Ezután válassza a **Kijelölés** gombot a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.

## <a name="configure-sso-in-jamf-pro"></a>Az SSO konfigurálása a Jamf Pro-ban

1. A Jamf Pro konfigurációjának automatizálásához telepítse a **My Apps Secure Bejelentkezésböngésző bővítményt** **a Bővítmény telepítése**lehetőség kiválasztásával.

    ![Saját alkalmazások biztonságos bejelentkezési böngészőbővítmény-lap](common/install-myappssecure-extension.png)

2. Miután hozzáadja a bővítményt a böngészőhöz, válassza a **Jamf Pro beállítása**lehetőséget. Amikor megnyílik a Jamf Pro alkalmazás, adja meg a rendszergazdai hitelesítő adatokat a bejelentkezéshez. A böngészőbővítmény automatikusan konfigurálja az alkalmazást, és automatizálja a 3-7.

    ![Beállítási konfigurációs lap a Jamf Pro-ban](common/setup-sso.png)

3. A Jamf Pro manuális beállításához nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként a Jamf Pro vállalati webhelyére. Ezután tegye meg a következő lépéseket.

4. Válassza a lap jobb felső sarkában található **Beállítások ikont.**

    ![A Beállítások ikon kiválasztása a Jamf Pro-ban](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Válassza **az Egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezés kiválasztása a Jamf Pro-ban](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Az **Egyszeri bejelentkezés** lapon tegye a következő lépéseket.

    ![A Jamf Pro egyszeri bejelentkezési oldala](./media/jamfprosamlconnector-tutorial/configure3.png)

    a. Válassza a **Szerkesztés** elemet.

    b. Jelölje be az **Egyszeri bejelentkezés hitelesítésének engedélyezése** jelölőnégyzetet.

  c. Válassza **Azure** ki az Azure-t, mint egy lehetőséget az **Identitásszolgáltató** legördülő menüben.

  d. Másolja az **ENTITÁS-azonosító** értékét, és illessze be az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában található **Azonosító (entitásazonosító)** mezőbe.

> [!NOTE]
> A `<SUBDOMAIN>` mezőben lévő érték használatával töltse ki a bejelentkezési URL-címet és a válasz URL-címét az Azure Portal **Alapszintű SAML-konfiguráció** szakaszában.

  e. Válassza a **Metaadatok URL-címét** az **Identitásszolgáltató metaadatforrása** legördülő menüből. A megjelenő mezőben illessze be az Azure-portálról másolt **App Federation metaadat-URL-címét.**

  f. (Nem kötelező) Szerkesztheti a token lejárati értékét, vagy válassza az "SAML token lejáratának letiltása" lehetőséget.

7. Ugyanazon az oldalon görgessen le a **Felhasználó leképezése** szakaszhoz. Ezután tegye meg a következő lépéseket.

    ![A Jamf Pro egyszeri bejelentkezési lapjának Felhasználói leképezése szakasza.](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. Válassza a **NameID** beállítást **az identitásszolgáltató felhasználói leképezéséhez.** Alapértelmezés szerint ez a beállítás **NameID**értékre van állítva, de egyéni attribútumot is megadhat.

    b. Válassza az **E-mail lehetőséget** a **Jamf Pro felhasználói leképezéséhez.** A Jamf Pro leképezi az IdP által először a felhasználók, majd a csoportok által küldött SAML-attribútumokat. Amikor egy felhasználó megpróbál hozzáférni a Jamf Pro-hoz, a Jamf Pro információkat kap a felhasználóról az Identitásszolgáltatótól, és összeveti az összes Jamf Pro felhasználói fiókkal. Ha a bejövő felhasználói fiók nem található, akkor a Jamf Pro megpróbálja egyeztetni azt a csoport név alapján.

    c. Illessze `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` be az értéket az **IDENTITY PROVIDER GROUP ATTRIBUTE NAME** mezőbe.

    d. Ugyanezen az oldalon görgessen le a **Biztonság** szakaszhoz, és válassza **a Felhasználók számára az egyszeri bejelentkezéshitelesítés megkerülésének engedélyezése**lehetőséget. Ennek eredményeképpen a felhasználók nem lesznek átirányítva az identitásszolgáltató bejelentkezési oldalára hitelesítésre, és helyette közvetlenül a Jamf Pro-ba jelentkezhetnek be. Amikor egy felhasználó az identitásszolgáltatón keresztül próbál hozzáférni a Jamf Pro-hoz, az IdP által kezdeményezett egyszeri szolgáltatás hitelesítése és engedélyezése történik.

    e. Kattintson a **Mentés** gombra.

### <a name="create-a-jamf-pro-test-user"></a>Jamf Pro tesztfelhasználó létrehozása

Annak érdekében, hogy az Azure AD-felhasználók bejelentkezhessenek a Jamf Pro-ba, ki kell építeni őket a Jamf Pro-ba. A Jamf Pro kiépítése manuális feladat.

Felhasználói fiók kiépítéséhez tegye a következő lépéseket:

1. Jelentkezzen be a Jamf Pro vállalati webhelyére rendszergazdaként.

2. Válassza a lap jobb felső sarkában található **Beállítások** ikont.

    ![A Beállítások ikon a Jamf Pro-ban](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Válassza **a Jamf Pro felhasználói fiókok & csoportok lehetőséget.**

    ![A Jamf Pro felhasználói fiókok & csoportok ikonja a Jamf Pro beállításaiban](./media/jamfprosamlconnector-tutorial/user1.png)

4. Válassza az **Új**lehetőséget.

    ![Jamf Pro felhasználói fiókok & csoportok rendszerbeállítások lap](./media/jamfprosamlconnector-tutorial/user2.png)

5. Válassza **a Standard fiók létrehozása**lehetőséget.

    ![Az Általános jogú fiók létrehozása beállítás a Jamf Pro felhasználói fiókok & csoportok lapján](./media/jamfprosamlconnector-tutorial/user3.png)

6. Az **Új fiók** párbeszédpanelen hajtsa végre az alábbi lépéseket:

    ![Új fiókbeállítási beállítások a Jamf Pro rendszerbeállításaiban](./media/jamfprosamlconnector-tutorial/user4.png)

    a. A **USERNAME** mezőbe `Britta Simon`írja be a tesztfelhasználó teljes nevét.

    b. Adja meg a szervezetnek megfelelő **HOZZÁFÉRÉSI SZINT**, **PRIVILEGE SET**és HOZZÁFÉRÉSI **ÁLLAPOT** beállításokat.

    c. A **TELJES NÉV** mezőbe `Britta Simon`írja be a mezőt.

    d. Az **E-MAIL CÍM** mezőbe írja be Britta Simon fiókjának e-mail címét.

    e. A **JELSZÓ** mezőbe írja be a felhasználó jelszavát.

    f. A **JELSZÓ ELLENŐRZÉSE** mezőbe írja be újra a felhasználó jelszavát.

    g. Kattintson a **Mentés** gombra.

## <a name="test-the-sso-configuration"></a>Az sso konfigurációjának tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelhasználatával teszteli.

Ha a Hozzáférési panelen kiválasztja a Jamf Pro csempét, automatikusan be kell jelentkeznie arra a Jamf Pro-fiókba, amelyhez konfigurálta az SSO-t. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Oktatóanyagok a SaaS-alkalmazások Azure Active Directoryval való integrálásához](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Egyszeri bejelentkezés az alkalmazásokba az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

- [Mi a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [Mi a munkamenet-vezérlés a Microsoft Cloud App Security alkalmazásban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
- [Próbálja ki a Jamf Pro-t az Azure AD-vel](https://aad.portal.azure.com/)