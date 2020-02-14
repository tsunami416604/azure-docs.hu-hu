---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a JAMF Pro-val | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a JAMF Pro között.
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
ms.openlocfilehash: 68de948261ec8bad429daafcb27e63ca5f7efb7a
ms.sourcegitcommit: 2823677304c10763c21bcb047df90f86339e476a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/14/2020
ms.locfileid: "77207298"
---
# <a name="tutorial-azure-active-directory-sso-integration-with-jamf-pro"></a>Oktatóanyag: Azure Active Directory SSO-integráció a JAMF Pro-val

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a JAMF Pro-t Azure Active Directory (Azure AD) használatával. Ha integrálja az JAMF Pro-t az Azure AD-vel, a következőket teheti:

* Az Azure AD segítségével szabályozhatja, hogy ki férhet hozzá a JAMF Pro szolgáltatáshoz.
* Az Azure AD-fiókokkal automatikusan bejelentkezhet a felhasználók JAMF Pro-ba.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg [az egyszeri bejelentkezést Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)használatával.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Az egyszeri bejelentkezést (SSO) engedélyező JAMF Pro-előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben. A JAMF Pro az **SP által kezdeményezett** és a **identitásszolgáltató által kezdeményezett** egyszeri bejelentkezést támogatja.

## <a name="add-jamf-pro-from-the-gallery"></a>A JAMF Pro hozzáadása a katalógusból

A JAMF Pro Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a JAMF Pro-t a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiók használatával.
1. A bal oldali ablaktáblán válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban adja meg a *JAMF Pro* kifejezést a keresőmezőbe.
1. Válassza az **JAMF Pro** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-sso-in-azure-ad-for-jamf-pro"></a>Egyszeri bejelentkezés konfigurálása és tesztelése az Azure AD-ben a JAMF Pro-ban

Konfigurálja és tesztelje az Azure AD SSO-t a JAMF Pro-val egy B. Simon nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a JAMF Pro-ban.

Ebben a szakaszban az Azure AD SSO konfigurálását és tesztelését végezheti el a JAMF Pro-val.

1. [Konfigurálja az egyszeri bejelentkezést az Azure ad-ben](#configure-sso-in-azure-ad) , így a felhasználók használhatják ezt a funkciót.
    1. [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad SSO teszteléséhez a B. Simon-fiókkal.
    1. [Rendelje hozzá az Azure ad-teszt felhasználóját](#assign-the-azure-ad-test-user) , hogy B. Simon használhassa az egyszeri bejelentkezést az Azure ad-ben.
1. [Konfigurálja az egyszeri bejelentkezést a JAMF Pro](#configure-sso-in-jamf-pro) szolgáltatásban az SSO-beállítások konfigurálásához az alkalmazás oldalán.
    1. [Hozzon létre egy JAMF Pro-teszt felhasználót](#create-a-jamf-pro-test-user) , hogy rendelkezzen a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon JAMF Pro-ügyféllel.
1. [Tesztelje az egyszeri bejelentkezés konfigurációját](#test-the-sso-configuration) annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-sso-in-azure-ad"></a>Egyszeri bejelentkezés konfigurálása az Azure AD-ben

Ebben a szakaszban engedélyezheti az Azure AD SSO-t a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **JAMF Pro** -alkalmazás integrációja lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza az **alapszintű SAML-konfigurációhoz** tartozó toll ikont a beállítások szerkesztéséhez.

   ![Szerkessze az alapszintű SAML-konfiguráció lapot.](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha az alkalmazást **identitásszolgáltató** módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be a következő képletet használó URL-címet: `https://<subdomain>.jamfcloud.com/saml/metadata`

    b. A **Válasz URL-címe** szövegmezőben adjon meg egy URL-címet, amely a következő képletet használja: `https://<subdomain>.jamfcloud.com/saml/SSO`

1. Válassza a **további URL-címek beállítása**lehetőséget. Ha az alkalmazást **SP-kezdeményezésű** módban szeretné konfigurálni, a **bejelentkezési URL-cím** szövegmezőben adjon meg egy URL-címet, amely a következő képletet használja: `https://<subdomain>.jamfcloud.com`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. A tényleges azonosító értékét a JAMF Pro portál **egyszeri bejelentkezés** szakaszában találja, amelyet az oktatóanyag későbbi részében talál. Kibonthatja a tényleges altartomány értéket az azonosító értékből, és a bejelentkezési URL-cím és a válasz URL-címének használatával kinyerheti az altartomány adatait. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható képleteket is megtekintheti.

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon lépjen az **SAML aláíró tanúsítvány** szakaszra, válassza a **Másolás** gombot az **alkalmazás-összevonási metaadatok URL-címének**másolásához, majd mentse a számítógépre.

    ![Az SAML-aláíró tanúsítvány letöltési hivatkozása](common/copy-metadataurl.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali ablaktábláján válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.
   1. A **Felhasználónév** mezőbe írja be a (z) [name] @ [cégestartomány] nevet. [kiterjesztés]. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban B. Simon hozzáférést biztosít a JAMF Pro-hoz.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **JAMF Pro**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![Felhasználók és csoportok kiválasztása](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![Válassza a felhasználó hozzáadása gombot](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-kijelentésben bármelyik szerepkör értékét várta, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára. Ezután kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-sso-in-jamf-pro"></a>SSO konfigurálása a JAMF Pro-ban

1. A JAMF Pro-n belüli konfiguráció automatizálásához telepítse a **saját alkalmazások biztonságos bejelentkezési böngésző bővítményét** a **bővítmény telepítése**lehetőség kiválasztásával.

    ![Saját alkalmazások – biztonságos bejelentkezési böngésző bővítmény lapja](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, válassza a **JAMF Pro beállítása**lehetőséget. Amikor megnyílik a JAMF Pro alkalmazás, adja meg a bejelentkezéshez szükséges rendszergazdai hitelesítő adatokat. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja a 3 – 7. lépést.

    ![Konfigurációs lap beállítása a JAMF Pro-ban](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a JAMF Pro-t, nyisson meg egy új böngészőablakot, és jelentkezzen be a JAMF Pro vállalati webhelyre rendszergazdaként. Ezután hajtsa végre az alábbi lépéseket.

4. Válassza a **Beállítások ikont** az oldal jobb felső sarkában.

    ![Válassza a beállítások ikont a JAMF Pro-ban](./media/jamfprosamlconnector-tutorial/configure1.png)

5. Válassza az **egyszeri bejelentkezés**lehetőséget.

    ![Egyszeri bejelentkezés kiválasztása a JAMF Pro-ban](./media/jamfprosamlconnector-tutorial/configure2.png)

6. Az **egyszeri bejelentkezés** oldalon hajtsa végre az alábbi lépéseket.

    ![Az egyszeri bejelentkezési oldal a JAMF Pro-ban](./media/jamfprosamlconnector-tutorial/configure3.png)

  a. Válassza a **Szerkesztés** elemet.

  b. Jelölje be az **egyszeri bejelentkezéses hitelesítés engedélyezése** jelölőnégyzetet.

    c. Válassza az **Azure** lehetőséget az identitás- **szolgáltató** legördülő menüből.

    d. Másolja az **entitás-azonosító** értékét, és illessze be a Azure Portal **ALAPszintű SAML-konfiguráció** szakaszának **azonosító (entitás azonosítója)** mezőjébe.

    > [!NOTE]
    > A `<SUBDOMAIN>` mezőben található érték használatával fejezze be a bejelentkezési URL-címet és a válasz URL-címét a Azure Portal **alapszintű SAML-konfiguráció** szakaszában.

    e. Válassza ki a **metaadatok URL-címét** az **Identity Provider metaadatok forrása** legördülő menüből. A megjelenő mezőben illessze be az **alkalmazás-összevonási metaadatok URL-címét** , amelyet a Azure Portal másolt.

    f. Választható Szerkessze a jogkivonat lejárati értékét, vagy válassza az "SAML-jogkivonat lejáratának letiltása" lehetőséget.

7. Ugyanazon a lapon görgessen le a felhasználó- **hozzárendelés** szakaszhoz. Ezután hajtsa végre az alábbi lépéseket.

    ![A JAMF Pro egyszeri bejelentkezési oldalának felhasználói leképezése szakasza.](./media/jamfprosamlconnector-tutorial/tutorial-jamfprosamlconnector-single.png)

    a. Válassza a **NameID** lehetőséget az **identitás-szolgáltató felhasználói leképezéséhez**. Alapértelmezés szerint ez a beállítás a **NameID**értékre van állítva, de egyéni attribútumot is meghatározhat.

    b. Válassza az **e-mail** lehetőséget a **JAMF Pro felhasználói leképezéshez**. A JAMF Pro Maps SAML-attribútumait a identitásszolgáltató először a felhasználók, majd csoportok szerint küldik el. Amikor egy felhasználó megpróbál hozzáférni a JAMF Pro-hoz, a JAMF Pro információt kér a felhasználótól az identitás-szolgáltatótól, és megfelel az összes JAMF Pro felhasználói fióknak. Ha a bejövő felhasználói fiók nem található, akkor a JAMF Pro megkísérli megfeleltetni a csoport nevét.

    c. Illessze be a `http://schemas.microsoft.com/ws/2008/06/identity/claims/groups` értéket az **Identity Provider csoport attribútumának neve** mezőbe.

    d. Ugyanazon a lapon görgessen le a **Biztonság** szakaszhoz, és jelölje be az **egyszeri bejelentkezéses hitelesítés megkerülésének engedélyezése a felhasználóknak**jelölőnégyzetet. Ennek eredményeképpen a felhasználók nem lesznek átirányítva az Identity Provider bejelentkezési oldalára hitelesítésre, és közvetlenül a JAMF Pro-be is bejelentkezhetnek. Ha a felhasználó az JAMF keresztül próbál hozzáférni a Pro-hoz, az identitásszolgáltató által kezdeményezett egyszeri bejelentkezéses hitelesítés és engedélyezés történik.

    e. Kattintson a **Mentés** gombra.

### <a name="create-a-jamf-pro-test-user"></a>JAMF Pro test-felhasználó létrehozása

Ahhoz, hogy az Azure AD-felhasználók bejelentkezzenek a JAMF Pro-ba, a JAMF Pro-ban kell kiépíteni őket. A JAMF Pro-ban való kiépítés manuális feladat.

Felhasználói fiók létrehozásához hajtsa végre a következő lépéseket:

1. Jelentkezzen be a JAMF Pro vállalati webhelyre rendszergazdaként.

2. Válassza a **Beállítások** ikont az oldal jobb felső sarkában.

    ![A beállítások ikon a JAMF Pro-ban](./media/jamfprosamlconnector-tutorial/configure1.png)

3. Válassza a **JAMF Pro felhasználói fiókok & csoportok**lehetőséget.

    ![A JAMF Pro felhasználói fiókjai & csoportok ikon a JAMF Pro beállításaiban](./media/jamfprosamlconnector-tutorial/user1.png)

4. Válassza az **Új** lehetőséget.

    ![JAMF Pro felhasználói fiókok & csoportok rendszerbeállítások oldal](./media/jamfprosamlconnector-tutorial/user2.png)

5. Válassza a **normál fiók létrehozása**lehetőséget.

    ![A standard fiók létrehozása lehetőség a JAMF Pro felhasználói fiókok & csoportok lapon](./media/jamfprosamlconnector-tutorial/user3.png)

6. Az **új fiók** párbeszédpanelen hajtsa végre az alábbi lépéseket.

    ![Új fiók beállítási lehetőségei a JAMF Pro rendszerbeállításokban](./media/jamfprosamlconnector-tutorial/user4.png)

    a. A **Felhasználónév** mezőbe írja be a `Britta Simon`nevet, a teszt felhasználó teljes nevét.

    b. Válassza ki az Ön szervezetének megfelelő **hozzáférési szint**, **jogosultsági csoport**és **hozzáférési állapot** beállításait.

    c. A **teljes név** mezőben adja meg a `Britta Simon`.

    d. Az **E-mail cím** mezőbe írja be a Britta Simon fiókjának e-mail-címét.

    e. A **jelszó** mezőbe írja be a felhasználó jelszavát.

    f. A **jelszó ellenőrzése** mezőben adja meg újra a felhasználó jelszavát.

    g. Kattintson a **Mentés** gombra.

## <a name="test-the-sso-configuration"></a>Az egyszeri bejelentkezés konfigurációjának tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Amikor kiválasztja a JAMF Pro csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a JAMF Pro-fiókba, amelyhez az SSO-t konfigurálta. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Oktatóanyagok az SaaS-alkalmazások integrálásához a Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Egyszeri bejelentkezés a Azure Active Directory alkalmazásaiba](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A JAMF Pro kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)
