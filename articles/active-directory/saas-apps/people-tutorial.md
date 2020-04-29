---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az emberekkel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és személyek között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 7c9b6202-11dd-4bb6-a679-8fb0a7a0ef4e
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 08/27/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3a9b8f08a54c978d81a8d33c61ab3d5f5fc7271f
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "70164220"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-people"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az emberekkel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhat személyeket Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja az embereket, a következőket teheti:

* Hozzáférés az Azure AD-hez, akik hozzáférnek a személyekhez.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek az Azure AD-fiókjával rendelkező személyek számára.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Egyszeri bejelentkezési (SSO) engedélyezett előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A felhasználók támogatják az **SP** által KEZDEMÉNYEZett SSO-t
* A People Mobile Application mostantól az Azure AD-vel is konfigurálható az egyszeri bejelentkezés engedélyezéséhez. Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

>[!NOTE]
>Az alkalmazás azonosítója egy rögzített karakterlánc-érték, így csak egy példány konfigurálható egyetlen bérlőn.

## <a name="adding-people-from-the-gallery"></a>Személyek felvétele a katalógusból

A személyek Azure AD-be való integrálásának konfigurálásához hozzá kell adnia személyeket a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **People** kifejezést a keresőmezőbe.
1. Válassza ki a **személyek** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-people"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése személyeknek

Konfigurálja és tesztelje az Azure AD SSO-t olyan személyekkel, akik egy **B. Simon**nevű teszt felhasználót használnak. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között.

Az Azure AD SSO személyekkel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. A **[felhasználók egyszeri](#configure-people-sso)** bejelentkezésének beállítása – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy felhasználót](#create-people-test-user)** , aki a felhasználó Azure ad-képviseletéhez csatolt B. Simon-partneri jogosultsággal rendelkezik.
6. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **People** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. A **bejelentkezési URL-cím** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<company name>.peoplehr.net`

    b. Az **azonosító** mezőbe írjon be egy URL-címet:`https://www.peoplehr.com`

    c. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával:`https://<company name>.peoplehr.net/Pages/Saml/ConsumeAzureAD.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és a válasz URL-címével. Vegye fel a kapcsolatot az [ügyfelek ügyfélszolgálatával](mailto:customerservices@peoplehr.com) , hogy megkapják ezeket az értékeket. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

4. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

6. A **személyek beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

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

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a személyekhez.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **személyek**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-people-sso"></a>Felhasználók egyszeri bejelentkezésének konfigurálása

1. A konfigurációnak a felhasználókon belüli automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **telepítés** gombra a felhasználók számára. Itt adja meg a rendszergazdai hitelesítő adatokat a felhasználókba való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-6-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha manuálisan szeretné beállítani a személyeket, nyisson meg egy új böngészőablakot, és jelentkezzen be a vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:
   
4. A bal oldali menüben kattintson a **Beállítások**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/people-tutorial/tutorial_people_001.png)

5. Kattintson a **vállalat**elemre.

    ![Egyszeri bejelentkezés konfigurálása](./media/people-tutorial/tutorial_people_002.png)

6. Az **"egyszeri bejelentkezés" SAML meta-adatfájljának feltöltéséhez**kattintson a **Tallózás** gombra a letöltött metaadat-fájl feltöltéséhez.

    ![Egyszeri bejelentkezés konfigurálása](./media/people-tutorial/tutorial_people_003.png)

### <a name="create-people-test-user"></a>Felhasználók létrehozása tesztelési felhasználó

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a People-ben. Az [ügyfél-támogatási csapattal](mailto:customerservices@peoplehr.com) együttműködve veheti fel a felhasználókat a People platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a személyek csempére kattint, akkor automatikusan be kell jelentkeznie azokra a felhasználókra, akiknek be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="test-sso-for-people-mobile"></a>SSO-teszt személyek számára (mobil)

1. Nyissa meg a People Mobile Application alkalmazást. A bejelentkezés lapon adja meg az **e-mail-azonosítót** , és kattintson az **egyszeri bejelentkezés**lehetőségre.

    ![A bejelentkezés](./media/people-tutorial/test01.png)

2. Adja meg a **szervezeti azonosítóját** , és kattintson a **tovább**gombra.

    ![Az e-mail cím](./media/people-tutorial/test02.png)

3. Végül a sikeres bejelentkezés után az alkalmazás kezdőlapja az alábbi képen látható:

    ![Egyszer](./media/people-tutorial/test03.png)

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Felhasználók kipróbálása az Azure AD-vel](https://aad.portal.azure.com)
