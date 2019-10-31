---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a G Suite-nal | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és G Suite között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 38a6ca75-7fd0-4cdc-9b9f-fae080c5a016
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/23/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 540dbc3605cfddc9b8d83eceeae8407848f1a91e
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73175998"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-g-suite"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a G Suite-nal

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a G Suite-t Azure Active Directory (Azure AD) használatával. Ha integrálja a G Suite-t az Azure AD-val, a következőket teheti:

* A G Suite-hoz hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók automatikusan bejelentkezzenek a G Suite-ba az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrálásáról, tekintse meg a [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés Azure Active Directorykal](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

- Egy Azure AD-előfizetés.
- A G Suite egyszeri bejelentkezés (SSO) engedélyezett előfizetése.
- Google Apps-előfizetés vagy Google Cloud Platform-előfizetés.

> [!NOTE]
> Az oktatóanyag lépéseinek teszteléséhez nem javasoljuk éles környezet használatát. Ez a dokumentum az új felhasználói egyszeri bejelentkezés használatával lett létrehozva. Ha továbbra is a régit használja, a telepítő eltérő lesz. A G-Suite alkalmazás egyszeri bejelentkezés beállításaiban engedélyezheti az új felhasználói élményt. Nyissa meg az **Azure ad-t, a vállalati alkalmazásokat**, válassza a **G Suite**lehetőséget, válassza az **egyszeri bejelentkezés** lehetőséget, majd kattintson az **új felhasználói élmény kipróbálása**lehetőségre.

Az oktatóanyag lépéseinek teszteléséhez kövesse az alábbi javaslatokat:

- Ne használja éles környezetét, ha szükséges.
- Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

1. **K: az integráció támogatja az Azure AD-vel való Google Cloud Platform SSO-integrációt?**

    V: Igen. A Google Cloud Platform és a Google Apps ugyanazt a hitelesítési platformot használja. Így a GCP-integrációhoz konfigurálnia kell az egyszeri bejelentkezést a Google Apps szolgáltatásban.

2. **K: az Azure AD egyszeri bejelentkezéssel kompatibilis Chromebook és más Chrome-eszközök?**
  
    Válasz: igen, a felhasználók Azure AD-beli hitelesítő adataikkal jelentkezhetnek be a Chromebook-eszközökre. Tekintse meg ezt a [G Suite támogatási cikket](https://support.google.com/chrome/a/answer/6060880) , amely arról nyújt tájékoztatást, hogy a felhasználók miért kapják meg kétszer a hitelesítő adatokat.

3. **K: ha engedélyezem az egyszeri bejelentkezést, a felhasználók Azure AD-beli hitelesítő adataikkal jelentkezhetnek be bármely Google-termékbe, például a Google Tanteremba, a GMailbe, a Google Drive-ba, a YouTube-ra stb.**

    Válasz: igen, attól függően, hogy [melyik G Suite](https://support.google.com/a/answer/182442?hl=en&ref_topic=1227583) -t szeretné engedélyezni vagy letiltani a szervezet számára.

4. **K: engedélyezhető az egyszeri bejelentkezés a G Suite-felhasználók csak egy részhalmaza számára?**

    A: nem, az egyszeri bejelentkezés azonnali bekapcsolása esetén az összes G Suite-felhasználónak hitelesítenie kell magát az Azure AD-beli hitelesítő adataival. Mivel a G Suite nem támogatja több identitás-szolgáltató használatát, a G Suite-környezet identitás-szolgáltatója az Azure AD vagy a Google is lehet, de egyszerre nem.

5. **K: Ha a felhasználó a Windowson keresztül jelentkezik be, a rendszer automatikusan hitelesíti a G Suite-t a jelszó kérése nélkül?**

    A: két lehetőség van ennek a forgatókönyvnek a engedélyezésére. Először is a felhasználók bejelentkezhetnek a Windows 10-es eszközökre [Azure Active Directory JOIN](../device-management-introduction.md)használatával. Azt is megteheti, hogy a felhasználók bejelentkezhetnek olyan Windows-eszközökre, amelyek tartományhoz csatlakoznak egy olyan helyszíni Active Directoryhoz, amely engedélyezve van az Azure AD-be való egyszeri bejelentkezésre egy [Active Directory összevonási szolgáltatások (AD FS) (AD FS)](../hybrid/plan-connect-user-signin.md) üzemelő példányon keresztül. Mindkét beállításhoz szükség van az alábbi oktatóanyag lépéseinek elvégzésére az Azure AD és a G Suite közötti egyszeri bejelentkezés engedélyezéséhez.

6. **K: mit tegyek, ha "érvénytelen e-mail" hibaüzenet jelenik meg?**

    A: ehhez a beállításhoz az e-mail-attribútum szükséges ahhoz, hogy a felhasználók be tudják jelentkezni. Ez az attribútum nem állítható be manuálisan.

    Az e-mail-attribútum minden érvényes Exchange-licenccel rendelkező felhasználó számára automatikusan fel van töltve. Ha a felhasználó nem rendelkezik e-mail-üzenettel, akkor a rendszer ezt a hibaüzenetet kapja, mivel az alkalmazásnak hozzáférést kell adnia ehhez az attribútumhoz.

    A portal.office.com rendszergazdai fiókkal, majd a felügyeleti központban, a számlázásban, az előfizetések területen válassza ki az Office 365-előfizetést, majd kattintson a hozzárendelés felhasználókhoz lehetőségre, válassza ki azokat a felhasználókat, akikkel ellenőriznie szeretné az előfizetését, és a jobb oldali ablaktáblán kattintson a licencek szerkesztése.

    A O365-licenc hozzárendelése után néhány percet is igénybe vehet. Ezt követően a User. mail attribútum automatikusan ki lesz töltve, és a problémát fel kell oldani.

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* A G Suite támogatja az **SP** által KEZDEMÉNYEZett SSO-t

* A G Suite támogatja a [felhasználók **automatikus** üzembe helyezését](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)

## <a name="adding-g-suite-from-the-gallery"></a>G Suite hozzáadása a katalógusból

A G Suite Azure AD-integrációjának konfigurálásához G Suite-t kell felvennie a katalógusból a felügyelt SaaS-alkalmazások listájára.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **G Suite** kifejezést a keresőmezőbe.
1. Válassza ki a **G Suite** elemet az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-g-suite"></a>Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a G Suite-hoz

Konfigurálja és tesztelje az Azure AD SSO-t a G Suite-mel egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és egy G Suite-beli kapcsolódó felhasználó között.

Az Azure AD SSO a G Suite-vel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. A **[G Suite SSO konfigurálása](#configure-g-suite-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre egy g Suite-teszt felhasználót](#create-g-suite-test-user)** – hogy a B. Simon egy, a felhasználó Azure ad-képviseletéhez kapcsolódó, g Suite-beli párja legyen.
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)a **G Suite** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha a **Gmail** konfigurálását szeretné elvégezni, hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://mail.google.com`

    b. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:

    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |

1. Ha az **alapszintű SAML-konfiguráció** szakaszban szeretné konfigurálni a **Google Cloud platform** , hajtsa végre a következő lépéseket:

    a. A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://www.google.com/a/<yourdomain.com>/ServiceLogin?continue=https://console.cloud.google.com`

    b. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával:
    
    | |
    |--|
    | `google.com/a/<yourdomain.com>` |
    | `google.com` |
    | `https://google.com` |
    | `https://google.com/a/<yourdomain.com>` |
    
    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges bejelentkezési URL-címmel és azonosítóval. A G Suite nem biztosít entitás-azonosító/azonosító értéket az egyszeri bejelentkezés konfigurációjában, így ha törli a **tartományi specifikus kiállítói** beállítást, az azonosító értéke `google.com`lesz. Ha bejelöli a **tartományi specifikus kiállítói** beállítást, `google.com/a/<yourdomainname.com>`lesz. A **tartomány-specifikus kiállítói** lehetőség bejelöléséhez vagy kikapcsolásához lépjen a **G Suite SSO konfigurálása** szakaszra, amelyet az oktatóanyag későbbi részében ismertet. További információért forduljon a [G Suite ügyfél-támogatási csapatához](https://www.google.com/contact/).

1. A G Suite-alkalmazás egy adott formátumban várja az SAML-jogcímeket, ehhez pedig egyéni attribútum-hozzárendeléseket kell hozzáadnia az SAML-jogkivonat attribútumainak konfigurációjához. A következő képernyőképen egy példa látható. Az **egyedi felhasználói azonosító** alapértelmezett értéke a **User. userPrincipalName** , de a G Suite azt várja, hogy a felhasználó e-mail-címével legyen leképezve. Ehhez használhatja a **User. mail** attribútumot a listából, vagy használhatja a megfelelő attribútumot a szervezeti konfiguráció alapján.

    ![image](common/edit-attribute.png)

1. A **felhasználó attribútumai** párbeszédpanel **felhasználói jogcímek** szakaszában szerkessze a jogcímeket a **Szerkesztés ikon** használatával, vagy adja hozzá a jogcímeket az **új jogcím hozzáadása** paranccsal az SAML-token attribútum konfigurálásához a fenti képen látható módon, és hajtsa végre a következő lépéseket:

    | Név | Forrás attribútum |
    | ---------------| --------------- |
    | Egyedi felhasználói azonosító | User. mail |

    a. Kattintson az **új jogcím hozzáadása** elemre a **felhasználói jogcímek kezelése** párbeszédpanel megnyitásához.

    ![image](common/new-save-attribute.png)

    ![image](common/new-attribute-details.png)

    b. A **név** szövegmezőbe írja be az adott sorhoz megjelenített attribútum nevét.

    c. Hagyja üresen a **névteret** .

    d. Válassza a forrás **attribútumként**lehetőséget.

    e. A **forrás attribútum** listáról írja be az adott sorhoz megjelenő attribútum értékét.

    f. Kattintson **az OK** gombra

    g. Kattintson a **Save** (Mentés) gombra.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg a **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozása](common/certificatebase64.png)

1. A **G Suite beállítása** szakaszban másolja ki a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension értéket. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a  **Create** (Létrehozás) gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon számára engedélyezi az Azure egyszeri bejelentkezés használatát a G Suite elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **G Suite**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.

## <a name="configure-g-suite-sso"></a>A G Suite SSO konfigurálása

1. Nyisson meg egy új fület a böngészőben, és jelentkezzen be a [G Suite felügyeleti konzolra](https://admin.google.com/) a rendszergazdai fiók használatával.

2. Kattintson a **Biztonság**elemre. Ha nem látja a hivatkozást, akkor előfordulhat, hogy a képernyő alján található **további vezérlők** menü alatt rejtve van.

    ![Kattintson a Security (Biztonság) elemre.][10]

3. A **Biztonság** lapon kattintson az **egyszeri bejelentkezés beállítása (SSO)** lehetőségre.

    ![Kattintson az SSO elemre.][11]

4. Hajtsa végre a következő konfigurációs módosításokat:

    ![Egyszeri bejelentkezés konfigurálása][12]

    a. Válassza **a beállítás egyszeri bejelentkezés külső szolgáltatótól**lehetőséget.

    b. A G Suite **bejelentkezési oldal URL-címe** mezőjébe illessze be azt a **bejelentkezési URL-címet** , amelyet a Azure Portalból másolt.

    c. A G Suite kijelentkezési **oldalának URL-címe** mezőjébe illessze be a **KIJELENTKEZÉSI URL-cím** értékét, amelyet a Azure Portal másolt.

    d. A G Suite **Password URL-cím módosítása** mezőjébe illessze be a **jelszó módosítása URL-cím** értékét, amelyet a Azure Portal másolt.

    e. A G Suite-ban az **ellenőrző tanúsítványhoz**töltse fel a Azure Portalról letöltött tanúsítványt.

    f. Jelölje be/törölje a **tartományi specifikus kiállítói lehetőség használata** lehetőséget az Azure ad fenti **alapszintű SAML-konfiguráció** szakaszában említett Megjegyzés alapján.

    g. Kattintson a **módosítások mentése**gombra.

### <a name="create-g-suite-test-user"></a>G Suite tesztelési felhasználó létrehozása

Ennek a szakasznak a célja, hogy a "B. Simon" nevű [felhasználót hozzon létre a G Suite-ben](https://support.google.com/a/answer/33310?hl=en) . Miután a felhasználó manuálisan létrejött a G Suite-ban, a felhasználó már bejelentkezhet az Office 365 bejelentkezési hitelesítő adataival.

A G Suite támogatja az automatikus felhasználó-kiépítés használatát is. Az automatikus felhasználó-kiépítés konfigurálásához először [a G Suite-t kell konfigurálnia a felhasználók automatikus kiépítési felállításához](https://docs.microsoft.com/en-us/azure/active-directory/saas-apps/google-apps-provisioning-tutorial).

> [!NOTE]
> Győződjön meg arról, hogy a felhasználó már létezik a G Suite-ban, ha az Azure AD-ben való kiépítés nincs bekapcsolva az egyszeri bejelentkezés tesztelése előtt.

> [!NOTE]
> Ha manuálisan kell létrehoznia egy felhasználót, lépjen kapcsolatba a [Google támogatási csoportjával](https://www.google.com/contact/).

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a hozzáférési panel használatával.

Ha a hozzáférési panelen a G Suite csempére kattint, automatikusan be kell jelentkeznie arra a G csomagba, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
- [A felhasználók üzembe helyezésének konfigurálása](https://docs.microsoft.com/azure/active-directory/saas-apps/google-apps-provisioning-tutorial)
- [A G Suite kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

<!--Image references-->

[10]: ./media/google-apps-tutorial/gapps-security.png
[11]: ./media/google-apps-tutorial/security-gapps.png
[12]: ./media/google-apps-tutorial/gapps-sso-config.png
