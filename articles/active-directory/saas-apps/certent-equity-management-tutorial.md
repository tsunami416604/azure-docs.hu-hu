---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Certent Equity Management szolgáltatással | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést a Azure Active Directory és a Certent között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 01/03/2020
ms.author: jeedes
ms.openlocfilehash: 0c194ad67673ac342280b01ce2654e36efa869fc
ms.sourcegitcommit: 023d10b4127f50f301995d44f2b4499cbcffb8fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88529712"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-certent-equity-management"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a Certent Equity Management szolgáltatással

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a Certent Equity managementet Azure Active Directory (Azure AD) használatával. Ha integrálja a Certent Equity managementet az Azure AD-vel, a következőket teheti:

* A Certent Equity-felügyelethez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti a felhasználók számára, hogy automatikusan bejelentkezzenek, hogy Certent a saját Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* A Certent Equity Management egyszeri bejelentkezés (SSO) engedélyezett előfizetése.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A Certent Equity Management támogatja a **identitásszolgáltató** által kezdeményezett egyszeri bejelentkezést

## <a name="adding-certent-equity-management-from-the-gallery"></a>A Certent Equity Management hozzáadása a katalógusból

A Certent Equity Management Azure AD-be való integrálásának konfigurálásához hozzá kell adnia a Certent Equity Management szolgáltatást a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal, vagy személyes Microsoft-fiók használatával.
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **Certent Equity Management** kifejezést a keresőmezőbe.
1. Válassza ki a **Certent Equity Management** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.


## <a name="configure-and-test-azure-ad-single-sign-on-for-certent-equity-management"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a Certent Equity Management szolgáltatásban

Konfigurálja és tesztelje az Azure AD SSO-t a Certent Equity managementtel egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között a Certent Equity Management szolgáltatásban.

Az Azure AD SSO az Certent Equity managementtel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    * **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    * **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[Certent Equity Management SSO konfigurálása](#configure-certent-equity-management-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    * **[Hozzon létre egy Certent Equity Management test felhasználót](#create-certent-equity-management-test-user)** – hogy rendelkezzen a Certent Equity-felügyeletben a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon-ügyféllel.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **Certent Equity Management** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.certent.com/sys/sso/saml/acs.aspx`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<SUBDOMAIN>.certent.com/sys/sso/saml/acs.aspx`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosító és válasz URL-címmel. Vegye fel a kapcsolatot a Certent Integration Analysis által hozzárendelt, az ügyfelek sikerességének kezelőjével. Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. A Certent Equity Management alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. Az alábbi képernyőképen az alapértelmezett attribútumok listája látható.

    ![image](common/default-attributes.png)

1. A fentieken kívül a Certent Equity Management alkalmazás néhány további attribútumot vár az SAML-válaszokban, amelyek alább láthatók. Ezek az attribútumok előre fel vannak töltve, de a követelményeinek megfelelően áttekintheti őket.

    | Name | Forrás attribútum|
    | ---------------| --------------- |
    | VÁLLALATI | felhasználó. cégnév |
    | FELHASZNÁLÓI | User. userPrincipalName |
    | SZEREPKÖR | User. assignedroles |

    > [!NOTE]
    > [Ide kattintva](https://docs.microsoft.com/azure/active-directory/develop/active-directory-enterprise-app-role-management) megtudhatja, hogyan konfigurálhatja a **szerepkört** az Azure ad-ben.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML-fájlját** , és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. A **Certent Equity Management beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát azáltal, hogy hozzáférést biztosít a Certent Equity-kezeléshez.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **Certent Equity-kezelés**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-certent-equity-management-sso"></a>A Certent Equity Management SSO konfigurálása

Ha az egyszeri bejelentkezést szeretné konfigurálni a **Certent Equity Management** oldalon, el kell küldenie a letöltött **összevonási METAADATOKat tartalmazó XML** -fájlt és a megfelelő másolt url-címeket a Azure Portalról a Certent integrációs elemzője által hozzárendelt ügyfelek siker-kezelőjéhez. Ezt a beállítást úgy állították be, hogy az SAML SSO-kapcsolatok mindkét oldalon helyesen legyenek beállítva.

### <a name="create-certent-equity-management-test-user"></a>Certent Equity Management test-felhasználó létrehozása

Ebben a szakaszban egy Britta Simon nevű felhasználót hoz létre a Certent Equity Management szolgáltatásban. A Certent Equity Management platformon a felhasználók hozzáadásához használja a Certent integrációs elemzőt, amelyet az ügyfél siker-kezelője rendelt hozzá. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a Certent Equity Management csempére kattint, automatikusan be kell jelentkeznie a Certent Equity-felügyeletbe, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [ Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory? ](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [A Certent Equity-felügyelet kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

