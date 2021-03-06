---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Cisco WebEx | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és a Cisco WebEx között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/31/2020
ms.author: jeedes
ms.openlocfilehash: 49e92c485c1a6a66dfb12b3c7a91f29939851d82
ms.sourcegitcommit: 9b8425300745ffe8d9b7fbe3c04199550d30e003
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2020
ms.locfileid: "92456104"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-cisco-webex"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Cisco WebEx

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Cisco WebEx Azure Active Directory (Azure AD) használatával. Ha az Azure AD-vel integrálja a Cisco WebEx-t, a következőket teheti:

* A Cisco WebEx hozzáférését biztosító Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a Cisco WebEx az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](../manage-apps/what-is-single-sign-on.md)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Cisco WebEx egyszeri bejelentkezés (SSO) engedélyezve előfizetés.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Cisco WebEx támogatja az **SP** által kezdeményezett egyszeri bejelentkezést.
* A Cisco WebEx támogatja a felhasználók **automatikus** üzembe helyezését.
* A Cisco WebEx konfigurálása után kényszerítheti a munkamenet-vezérlést, amely a szervezet bizalmas adatainak kiszűrése és beszivárgását valós időben teszi elérhetővé. A munkamenet-vezérlő kiterjeszthető a feltételes hozzáférésből. [Ismerje meg, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud App Security](/cloud-app-security/proxy-deployment-aad)

## <a name="adding-cisco-webex-from-the-gallery"></a>Cisco-WebEx hozzáadása a katalógusból

A Cisco WebEx Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Cisco WebEx a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Cisco WebEx** kifejezést a keresőmezőbe.
1. Válassza ki a **Cisco WebEx** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-cisco-webex"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Cisco WebEx

Konfigurálja és tesztelje az Azure AD SSO-t a Cisco WebEx egy **B. Simon**nevű tesztelési felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Cisco WebEx.

Az Azure AD SSO és a Cisco WebEx konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure ad SSO](#configure-azure-ad-sso)** -t, hogy a felhasználók használhatják ezt a funkciót.
    1. **[Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user)** az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
2. **[Konfigurálja a Cisco WebEx](#configure-cisco-webex)** az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    1. **[Hozzon létre Cisco WebEx-tesztelési felhasználót](#create-cisco-webex-test-user)** , hogy a "B. Simon" partnere legyen a Cisco WebEx, amely a felhasználó Azure ad-képviseletéhez van társítva.
3. Ellenőrizze az **[SSO](#test-sso)** -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

### <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Cisco WebEx** alkalmazás-integráció lapján keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri Sign-On beállítása az SAML-vel** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

4. Az **alapszintű SAML-konfiguráció** szakaszban töltse fel a letöltött **szolgáltatói metaadat** -fájlt, és konfigurálja az alkalmazást az alábbi lépések végrehajtásával:

    >[!Note]
    >A szolgáltatói metaadatokat a **Cisco WebEx konfigurálása** szakaszban találja, amelyet az oktatóanyag későbbi részében talál. 

    a. Kattintson a **metaadat-fájl feltöltése**elemre.

    b. Kattintson a **mappa emblémára** a metaadat-fájl kiválasztásához, majd kattintson a **feltöltés**elemre.

    c. A szolgáltatói metaadatok feltöltésének sikeres befejezése után az **azonosító** és a **Válasz URL-** értékei az **alapszintű SAML-konfiguráció** szakaszban automatikusan feltöltve lesznek:

    A **bejelentkezési URL** szövegmezőbe illessze be a **Válasz URL-címét**, amely az SP metaadat-fájl feltöltésekor automatikus kitöltést kap.

1. A Cisco WebEx-alkalmazás meghatározott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a Cisco WebEx alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.
  
    | Name |  Forrás attribútum|
    | ---------------|--------- |
    | UID | User. userPrincipalName |

1. Az **egyszeres Sign-On beállítása SAML** használatával lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

   ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Cisco WebEx beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmény alapján.

   ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** lehetőségre.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a Cisco WebEx.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Cisco WebEx**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-cisco-webex"></a>A Cisco WebEx konfigurálása

1. A Cisco WebEx belüli konfiguráció automatizálásához telepítenie kell az **alkalmazások biztonságos bejelentkezési böngésző bővítményét** **a bővítmény telepítése**lehetőségre kattintva.

    ![Saját alkalmazások bővítmény](common/install-myappssecure-extension.png)

2. Miután hozzáadta a bővítményt a böngészőhöz, kattintson a **Cisco WebEx beállítása** lehetőségre a Cisco WebEx alkalmazáshoz. Itt adja meg a rendszergazdai hitelesítő adatokat a Cisco WebEx való bejelentkezéshez. A böngésző bővítménye automatikusan konfigurálja az alkalmazást, és automatizálja az 3-8-es lépést.

    ![Telepítési konfiguráció](common/setup-sso.png)

3. Ha a Cisco WebEx-t manuálisan szeretné beállítani, jelentkezzen be a [Cisco Cloud Collaboration Management](https://admin.ciscospark.com/) szolgáltatásba a teljes körű rendszergazdai hitelesítő adataival.

4. Válassza a **Beállítások** lehetőséget, majd a **hitelesítés** szakaszban kattintson a **módosítás**lehetőségre.

    ![A képernyőképen a módosítás lehetőségre kattintva megjelenítheti a hitelesítési beállításokat.](./media/cisco-spark-tutorial/tutorial-cisco-spark-10.png)
  
5. Válassza **a harmadik féltől származó identitás-szolgáltató integrálása lehetőséget. (Speciális)** és lépjen a következő képernyőre.

6. Az **identitásszolgáltató-Metaadatok importálása** lapon húzza át az Azure ad metaadat-fájlját az oldalra, vagy használja az Azure ad metaadat-fájljának megkereséséhez és feltöltéséhez a fájl tallózása lehetőséget. Ezután jelölje be **a hitelesítésszolgáltató által aláírt tanúsítvány megkövetelése a metaadatokban (biztonságosabb)** jelölőnégyzetet, majd kattintson a **tovább**gombra.

    ![Képernyőfelvétel: az import I d P metaadatok lapja.](./media/cisco-spark-tutorial/tutorial-cisco-spark-11.png)

7. Válassza az **egyszeri bejelentkezéses csatlakozás tesztelése**lehetőséget, és amikor megnyílik egy új böngésző lap, jelentkezzen be az Azure ad-be.

8. Térjen vissza a **Cisco Cloud Collaboration Management** böngésző lapjára. Ha a teszt sikeres volt, válassza ki **a teszt sikerességét. Engedélyezze az egyszeri Sign-On lehetőséget** , és kattintson a **tovább**gombra.

### <a name="create-cisco-webex-test-user"></a>Cisco WebEx-tesztelési felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a Cisco WebEx. Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre a Cisco WebEx.

1. Nyissa meg a [Cisco Cloud Collaboration managementet](https://admin.ciscospark.com/) a teljes körű rendszergazdai hitelesítő adataival.

2. Kattintson a **felhasználók** , majd a **felhasználók kezelése**lehetőségre.
   
    ![Képernyőfelvétel: a felhasználók lap, amelyen kezelheti a felhasználókat.](./media/cisco-spark-tutorial/tutorial-cisco-spark-12.png) 

3. A **felhasználó kezelése** ablakban válassza a **felhasználók manuális hozzáadása vagy módosítása** lehetőséget, majd kattintson a **tovább**gombra.

4. Válassza **a nevek és e-mail-cím**elemet. Ezután töltse ki a szövegmezőt a következő módon:

    ![Képernyőfelvétel: a felhasználók manuális hozzáadására és módosítására szolgáló panel.](./media/cisco-spark-tutorial/tutorial-cisco-spark-13.png) 

    a. Az **Utónév** szövegmezőbe írja be a (z) " **B**" felhasználó utónevét.

    b. A **vezetéknév** szövegmezőbe írja be a felhasználó vezetéknevét (például **Simon**).

    c. Az **e-mail cím** szövegmezőbe írja be a hasonló felhasználó e-mail címét b.simon@contoso.com .

5. Kattintson a plusz jelre a B. Simon hozzáadásához. Ezután kattintson a **tovább**gombra.

6. A **szolgáltatások hozzáadása a felhasználók számára** ablakban kattintson a **Mentés** , majd a **Befejezés**gombra.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Amikor kiválasztja a Cisco WebEx csempét a hozzáférési panelen, automatikusan be kell jelentkeznie arra a Cisco-WebEx, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](../user-help/my-apps-portal-end-user-access.md).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](./tutorial-list.md)

- [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](../manage-apps/what-is-single-sign-on.md)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](../conditional-access/overview.md)

- [A Cisco WebEx kipróbálása az Azure AD-vel](https://aad.portal.azure.com)

- [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](/cloud-app-security/proxy-intro-aad)

- [A Cisco WebEx elleni védelem speciális láthatósággal és ellenőrzésekkel](/cloud-app-security/protect-webex)