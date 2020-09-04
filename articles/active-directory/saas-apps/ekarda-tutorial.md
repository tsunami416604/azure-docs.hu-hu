---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a ekarda | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és ekarda között.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.reviewer: celested
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 06/15/2020
ms.author: jeedes
ms.openlocfilehash: cfd7a007c8f26a96d929026e231cb412cdc70279
ms.sourcegitcommit: bf1340bb706cf31bb002128e272b8322f37d53dd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2020
ms.locfileid: "89440864"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-ekarda"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció a ekarda

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja a ekarda a Azure Active Directory (Azure AD) szolgáltatással. Ha integrálja az ekarda-t az Azure AD-vel, a következőket teheti:

* A ekarda-hez hozzáférő Azure AD-beli vezérlés.
* Lehetővé teheti, hogy a felhasználók Azure AD-fiókjaik használatával automatikusan bejelentkezzenek a ekarda.
* A fiókokat egyetlen központi helyen kezelheti: a Azure Portal.

Ha többet szeretne megtudni a szolgáltatott szoftver (SaaS) alkalmazás Azure AD-integrációval kapcsolatban, tekintse meg a [Mi az egyszeri bejelentkezés (SSO)?](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.
* Ekarda-előfizetés engedélyezve az egyszeri bejelentkezéshez (SSO).

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* a ekarda támogatja az SP által kezdeményezett és a IDENTITÁSSZOLGÁLTATÓ által kezdeményezett egyszeri bejelentkezést.
* a ekarda támogatja az igény szerinti felhasználói üzembe helyezést.
* A ekarda konfigurálása után kényszerítheti a munkamenet-vezérlést. Ez a figyelmeztetés védelmet nyújt a szervezete bizalmas adatainak valós idejű kiszűrése és beszivárgása ellen. A munkamenet-vezérlő kiterjeszthető feltételes hozzáférést biztosító alkalmazás-vezérlőról. [Megtudhatja, hogyan kényszerítheti ki a munkamenet-vezérlést Microsoft Cloud app Security használatával](https://docs.microsoft.com/cloud-app-security/proxy-deployment-any-app).

## <a name="add-ekarda-from-the-gallery"></a>Ekarda hozzáadása a gyűjteményből

A ekarda Azure AD-be való integrálásának konfigurálásához adja hozzá a ekarda a katalógusból a felügyelt SaaS-alkalmazások listájához:

1. Jelentkezzen be a [Azure Portal](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiók használatával.

1. A bal oldali panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Lépjen a **vállalati alkalmazások**elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Az új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a **ekarda** kifejezést a keresőmezőbe.
1. Válassza ki a **ekarda** az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-ekarda"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése a ekarda

Konfigurálja és tesztelje az Azure AD SSO-t a ekarda egy **B. Simon**nevű teszt felhasználó használatával. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy összekapcsolt kapcsolatot az Azure AD-felhasználó és a kapcsolódó felhasználó között a ekarda-ben.

Az Azure AD SSO és a ekarda konfigurálásához és teszteléséhez hajtsa végre a következő lépéseket:

1. [Konfigurálja az Azure ad SSO](#configure-azure-ad-sso) -t, hogy a felhasználók használhatják ezt a funkciót.

    1. [Hozzon létre egy Azure ad-tesztelési felhasználót](#create-an-azure-ad-test-user) az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. [Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user) , hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. [Konfigurálja a EKARDA SSO](#configure-ekarda-sso) -t az egyszeri bejelentkezés beállításainak konfigurálásához az alkalmazás oldalán.
    * [Hozzon létre egy ekarda-tesztelési felhasználót](#create-an-ekarda-test-user) , amely a felhasználó Azure ad-képviseletéhez kapcsolódó B. Simon ekarda rendelkezik.
1. Ellenőrizze az [SSO](#test-sso) -t annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az Azure AD SSO engedélyezéséhez kövesse az Azure Portal alábbi lépéseit:

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).
1. A **ekarda** alkalmazás-integráció lapon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon válassza a ceruza ikont az **alapszintű SAML konfigurációs** beállítások szerkesztéséhez.

   ![Képernyőfelvétel az egyszeri bejelentkezés SAML-oldallal való beállításáról: a ceruza ikon kiemelve.](common/edit-urls.png)

1. Az **alapszintű SAML-konfiguráció** szakaszban, ha a **szolgáltatói metaadatokat tartalmazó fájlt**látja, kövesse az alábbi lépéseket:
    1. Válassza a **metaadat-fájl feltöltése**lehetőséget.
    1. Válassza a mappa ikont a metaadat-fájl kiválasztásához, majd válassza a **feltöltés**lehetőséget.
    1. A metaadat-fájl feltöltése után az **azonosító** és a **Válasz URL-címe** automatikusan megjelenik a ekarda szakasz szövegmezőben.

    > [!Note]
    > Ha az **azonosító** és a **Válasz URL-címe** nem jelenik meg automatikusan, adja meg manuálisan az értékeket az igényeinek megfelelően.

1. Ha nem látja a szolgáltatói **metaadatokat tartalmazó fájlt** az **ALAPszintű SAML-konfiguráció** szakaszban, és az alkalmazást identitásszolgáltató módban szeretné konfigurálni, adja meg az alábbi mezők értékeit:

    1. Az **azonosító** szövegmezőbe írja be azt az URL-címet, amely a következő mintát követi: `https://my.ekarda.com/users/saml_metadata/<COMPANY_ID>`
    1. A **Válasz URL-címe** szövegmezőbe írja be azt az URL-címet, amely a következő mintát követi: `https://my.ekarda.com/users/saml_acs/<COMPANY_ID>`

1. Ha az alkalmazást SP-kezdeményezésű módban szeretné konfigurálni, válassza a **további URL-címek beállítása** lehetőséget, és tegye a következőket:

    * A **bejelentkezési URL** szövegmezőbe írja be azt az URL-címet, amely a következő mintát követi: `https://my.ekarda.com/users/saml_sso/<COMPANY_ID>`

    > [!NOTE]
    > Az előző két lépés értékei nem valósak. Frissítse őket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címek értékével. Az értékek lekéréséhez forduljon a ekarda ügyfélszolgálati [csapatához](mailto:contact@ekarda.com) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon az **SAML aláíró tanúsítvány** szakaszban válassza a **Letöltés** lehetőséget a **tanúsítvány (Base64)** mentéséhez a számítógépen.

    ![Képernyőfelvétel az egyszeri bejelentkezés az SAML-vel való beállításáról című részben, a Base64 tanúsítványra Kiemelt letöltési hivatkozással.](common/certificatebase64.png)

1. A **Ekarda beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Képernyőkép az egyszeri bejelentkezés beállítása az SAML-vel lapon az ekarda beállítása című szakaszban, az URL-másolási hivatkozások kiemelve.](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Azure AD-tesztkörnyezet létrehozása

Ebben a szakaszban a Azure Portal használatával hozzon létre egy B. Simon nevű teszt felhasználót.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**  >  **felhasználók**  >  **minden felhasználó**lehetőséget.

1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A Felhasználónév mezőben adja meg a **nevet** username@companydomain.extension . Adja meg például a következőt: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, és jegyezze fel a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure AD-teszt felhasználójának kiosztása

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést, ha hozzáférést biztosít a ekarda.

1. A Azure Portal válassza a **vállalati alkalmazások**  >  **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza a **ekarda**lehetőséget.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![Képernyőfelvétel a kezelés szakaszról, a Kiemelt felhasználók és csoportok lehetőséggel.](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználók és csoportok szakasz képernyőképe, a felhasználó hozzáadása Kiemelt színnel.](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza ki a felhasználók listájából a **B. Simon** elemet. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.
1. Ha az SAML-kijelentésben bármilyen szerepkörre számíthat, válassza ki a megfelelő szerepkört a felhasználó számára a **szerepkör kiválasztása** párbeszédpanel listájában. Ezután válassza a **kijelölés** lehetőséget a képernyő alján.
1. A **hozzárendelés hozzáadása** párbeszédpanelen válassza a **hozzárendelés**lehetőséget.

## <a name="configure-ekarda-sso"></a>Ekarda SSO konfigurálása

1. Egy másik webböngésző-ablakban jelentkezzen be a ekarda vállalati webhelyre rendszergazdaként.
1. Válassza **Admin**  >  **a rendszergazda saját fiók**lehetőséget.

    ![Képernyőkép a ekarda-hely felhasználói felületéről a fiókom kiemelve a felügyeleti menüben.](./media/ekarda-tutorial/ekarda.png)

1. A lap alján keresse meg az SAML- **Beállítások** szakaszt. Ez a szakasz az SAML-integráció konfigurálását ismerteti.
1. Az **SAML-beállítások** szakaszban kövesse az alábbi lépéseket:

    ![Képernyőkép a ekarda SAML-beállítások oldalról, ahol az SAML-konfigurációs mezők ki vannak emelve.](./media/ekarda-tutorial/ekarda1.png)

    1. Válassza ki a **szolgáltatói metaadatok** hivatkozást, és mentse fájlba a számítógépén.
    1. Jelölje be az **SAML engedélyezése** jelölőnégyzetet.
    1. Illessze be a korábban a Azure Portalból másolt **Azure ad-azonosító** értékét a **IDENTITÁSSZOLGÁLTATÓ-entitás azonosítója** szövegmezőbe.
    1. A **identitásszolgáltató bejelentkezési URL-címe** szövegmezőbe illessze be a korábban a Azure Portalból másolt **bejelentkezési URL-** értéket.
    1. A **identitásszolgáltató kijelentkezési URL-címe** szövegmezőbe illessze be a korábban a Azure Portalból másolt **kijelentkezési URL-** értéket.
    1. A Jegyzettömb segítségével nyissa meg a Azure Portal letöltött **tanúsítvány-(Base64-)** fájlt. Illessze be a tartalmat a **identitásszolgáltató x509-tanúsítvány** szövegmezőbe.
    1. Jelölje be az **slo engedélyezése** jelölőnégyzetet a **Beállítások** szakaszban.
    1. Válassza a **Frissítés** lehetőséget.

### <a name="create-an-ekarda-test-user"></a>Ekarda-teszt felhasználó létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználó jön létre a ekarda-ben. a ekarda támogatja az igény szerinti felhasználói üzembe helyezést, amely alapértelmezés szerint engedélyezve van. Ebben a szakaszban nem hajtható végre művelet. Ha egy B. Simon nevű felhasználó már nem létezik a ekarda-ben, akkor a hitelesítés után létrejön egy újat.

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját teszteli a saját alkalmazások portál használatával.

Amikor kiválasztja a ekarda csempét a saját alkalmazások portálon, automatikusan be kell jelentkeznie arra a ekarda-helyre, amelyhez be szeretné állítani az egyszeri bejelentkezést. További információ a saját alkalmazások portálján: [Bevezetés a My apps portálra](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

* [Az SaaS-alkalmazások Azure Active Directory-vel való integrálására szolgáló oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)
* [Mi az az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)
* [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)
* [A ekarda kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)
* A [ekarda vállalati ecard-megoldásával](https://ekarda.com/ecards-ecards-with-logo-for-business-corporate-enterprise) tetszőleges számú munkatárs elküldheti a képeslapokat, a márkát a céges emblémával, az ügyfeleivel és kollégáival. További információ az [ekarda egyszeri bejelentkezési megoldásként való üzembe](https://support.ekarda.com/#SSO-Implementation)helyezéséről.
* [Mi a munkamenet-vezérlő a Microsoft Cloud App Securityban?](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
* [A ekarda és a speciális láthatóság és vezérlők elleni védelem](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad)
