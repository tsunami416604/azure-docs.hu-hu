---
title: 'Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az F5-mel | Microsoft Docs'
description: Megtudhatja, hogyan konfigurálhat egyszeri bejelentkezést Azure Active Directory és F5 között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 59a87abb-1ec1-4438-be07-5b115676115f
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7ad6b7150a43a286a4bec39a0482e08f50d95c06
ms.sourcegitcommit: 57669c5ae1abdb6bac3b1e816ea822e3dbf5b3e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2020
ms.locfileid: "77048067"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Oktatóanyag: Azure Active Directory egyszeri bejelentkezéses (SSO) integráció az F5-mel

Ebből az oktatóanyagból megtudhatja, hogyan integrálhatja az F5-et Azure Active Directory (Azure AD) használatával. Az F5 és az Azure AD integrálásával a következőket teheti:

* Az F5-hez hozzáférő Azure AD-beli vezérlés.
* Engedélyezze, hogy a felhasználók automatikusan bejelentkezzenek az F5-be az Azure AD-fiókjával.
* A fiókokat egyetlen központi helyen kezelheti – a Azure Portal.

Ha többet szeretne megtudni az Azure AD-ban való egyszeri bejelentkezéssel való SaaS-alkalmazások integrálásáról, tekintse meg az [egyszeri bejelentkezést a Azure Active Directory alkalmazásaihoz](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)című témakört.

## <a name="prerequisites"></a>Előfeltételek

Első lépésként a következő elemeket kell megadnia:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, [ingyenes fiókot](https://azure.microsoft.com/free/)kérhet.

* F5 egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

* A közös megoldás üzembe helyezéséhez a következő licenc szükséges:

    * F5 BIG-IP® legjobb csomag (vagy) 

    * F5 BIG-IP Access Policy Manager™ (APM) önálló licenc 

    * F5 BIG-IP Access Policy Manager™ (APM) bővítmény licence egy meglévő BIG-IP F5 BIG-IP® helyi Traffic Manager™ (LTM).

    * A fenti licencen kívül az F5-es verziójú rendszerek is licenccel rendelkezhetnek a következővel: 

        * URL-szűrési előfizetés az URL-kategória adatbázisának használatára

        * F5 IP Intelligence-előfizetés az ismert támadók és a rosszindulatú forgalom észleléséhez és blokkolásához
     
        * Hálózati hardveres biztonsági modul (HSM) a digitális kulcsok erős hitelesítéshez való védelméhez és kezeléséhez

* Az F5 BIG-IP rendszer APM-modulokkal van kiépítve (a LTM nem kötelező)

* Bár nem kötelező, erősen ajánlott az F5 rendszer üzembe helyezése egy [szinkronizálási/feladatátvételi eszköz csoportjában](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), amely magában foglalja az aktív készenléti párokat, valamint a magas rendelkezésre állású, LEBEGŐPONTOS IP-címet. A további illesztőfelület-redundancia a link aggregációs vezérlő protokoll (LACP) használatával érhető el. A LACP egyetlen virtuális csatolóként (összesítő csoportként) kezeli a csatlakoztatott fizikai adaptereket, és észleli a csoporton belüli összes illesztőfelületi hibát.

* Kerberos-alkalmazások esetén egy helyszíni AD-szolgáltatásfiók a korlátozott delegáláshoz.  Az AD delegálási fiók létrehozásához tekintse meg az [F5 dokumentációt](https://support.f5.com/csp/article/K43063049) .

## <a name="access-guided-configuration"></a>Hozzáférés az irányított konfigurációhoz

* A hozzáférés-vezérelt konfiguráció az F5 TMOS 13.1.0.8 vagy újabb verzióban támogatott. Ha a BIG-IP rendszer a 13.1.0.8 alábbi verzióját futtatja, tekintse meg a **Speciális konfiguráció** szakaszt.

* A hozzáférés-vezérelt konfiguráció egy teljesen új és áramvonalas felhasználói élményt nyújt. Ez a munkafolyamat-alapú architektúra a kiválasztott topológiához igazított, intuitív és újra belépő konfigurációs lépéseket biztosít.

* Mielőtt folytatná a konfigurálást, frissítse az irányított konfigurációt úgy, hogy letölti a legújabb használati eset csomagot a [downloads.F5.com](https://login.f5.com/resource/login.jsp?ctx=719748)webhelyről. A frissítéshez kövesse az alábbi eljárást.

    >[!NOTE]
    >Az alábbi Képernyőképek a legújabb verzióra vonatkoznak (BIG-IP 15,0, AGC-verzió: 5,0). Az alábbi konfigurációs lépések érvényesek erre a használati esetre a 13.1.0.8 és a legújabb BIG-IP-verzió között.

1. Az F5 BIG-IP webes felületen kattintson a **hozzáférés > > útmutató konfigurálása**elemre.

1. Az **irányított konfiguráció** lapon kattintson az **irányított konfiguráció frissítése** elemre a bal felső sarokban.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure14.png) 

1. A frissítési útmutató konfigurációjának pop képernyőjén válassza a **fájl kiválasztása** elemet a letöltött használati eset csomag feltöltéséhez, majd kattintson a **feltöltés és telepítés** gombra.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure15.png) 

1. A frissítés befejezésekor kattintson a **Continue (folytatás** ) gombra.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban az Azure AD SSO konfigurálását és tesztelését teszteli a tesztkörnyezetben.

* Az F5 SSO három különböző módon konfigurálható.

- [Az F5 egyszeri bejelentkezés konfigurálása a fejléc-alapú alkalmazáshoz](#configure-f5-single-sign-on-for-header-based-application)

- [Az F5 egyszeri bejelentkezés konfigurálása a Kerberos-alkalmazáshoz](kerbf5-tutorial.md)

- [Az F5 egyszeri bejelentkezés konfigurálása a speciális Kerberos-alkalmazáshoz](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Legfontosabb hitelesítési forgatókönyvek

* A modern hitelesítési protokollok (például az Open ID csatlakozás, az SAML és a WS-Fed) natív integrációs támogatása Azure Active Directory mellett az F5 az Azure AD-vel való belső és külső hozzáféréshez is biztonságos hozzáférést nyújt a régi alapú hitelesítési alkalmazásokhoz, és lehetővé teszi a modern forgatókönyvek (például jelszó nélküli hozzáférés) ezekre az alkalmazásokra. Ilyenek például a következők:

* Fejléc alapú hitelesítési alkalmazások

* Kerberos-hitelesítési alkalmazások

* Névtelen hitelesítés vagy nem beépített hitelesítési alkalmazások

* NTLM hitelesítési alkalmazások (védelem kettős kéréssel a felhasználó számára)

* Űrlap-alapú alkalmazás (védelem kettős rákérdezéssel a felhasználó számára)

## <a name="adding-f5-from-the-gallery"></a>Az F5 hozzáadása a gyűjteményből

Az F5 Azure AD-integrációjának konfigurálásához az F5 billentyűt kell hozzáadnia a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be egy munkahelyi vagy iskolai fiókkal vagy a személyes Microsoft-fiókjával az [Azure Portalra](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory** szolgáltatást.
1. Navigáljon a **vállalati alkalmazások** elemre, majd válassza a **minden alkalmazás**lehetőséget.
1. Új alkalmazás hozzáadásához válassza az **új alkalmazás**lehetőséget.
1. A **Hozzáadás a** katalógusból szakaszban írja be a keresőmezőbe az **F5** kifejezést.
1. Válassza az **F5** lehetőséget az eredmények panelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás bekerül a bérlőbe.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Az Azure AD egyszeri bejelentkezés konfigurálása és tesztelése F5 rendszerhez

Konfigurálja és tesztelje az Azure AD SSO-t az F5 használatával egy **B. Simon**nevű tesztelési felhasználóval. Az egyszeri bejelentkezés működéséhez létre kell hoznia egy kapcsolati kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az F5 billentyűvel.

Az Azure AD SSO az F5 használatával történő konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. Az **[Azure ad SSO konfigurálása](#configure-azure-ad-sso)** – a funkció használatának engedélyezése a felhasználók számára.
    1. **[Azure ad-felhasználó létrehozása](#create-an-azure-ad-test-user)** – az Azure ad egyszeri bejelentkezés teszteléséhez B. Simon használatával.
    1. **[Rendelje hozzá az Azure ad-teszt felhasználót](#assign-the-azure-ad-test-user)** – ezzel lehetővé teszi, hogy B. Simon engedélyezze az Azure ad egyszeri bejelentkezést.
1. Az **[F5 SSO konfigurálása](#configure-f5-sso)** – az egyszeri bejelentkezés beállításainak konfigurálása az alkalmazás oldalán.
    1. **[Hozzon létre F5 test User](#create-f5-test-user)** – ha a felhasználó Azure ad-képviseletéhez társított B
1. **[SSO tesztelése](#test-sso)** – annak ellenőrzése, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Az alábbi lépéseket követve engedélyezheti az Azure AD SSO használatát a Azure Portalban.

1. A [Azure Portal](https://portal.azure.com/)az **F5** Application Integration oldalon keresse meg a **kezelés** szakaszt, és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **egyszeri bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. Az **egyszeri bejelentkezés SAML-vel való beállítása** lapon kattintson az **ALAPszintű SAML-konfiguráció** szerkesztés/toll ikonjára a beállítások szerkesztéséhez.

   ![Alapszintű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Ha a **identitásszolgáltató** által kezdeményezett módban szeretné konfigurálni az alkalmazást, az **ALAPszintű SAML-konfiguráció** szakaszban adja meg a következő mezők értékeit:

    a. Az **azonosító** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<YourCustomFQDN>.f5.com/`

    b. A **Válasz URL-címe** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<YourCustomFQDN>.f5.com/`

1. Kattintson a **további URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** -ben kezdeményezett módban szeretné konfigurálni:

    A **bejelentkezési URL** szövegmezőbe írja be az URL-címet a következő minta használatával: `https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. A következő értékek megszerzéséhez lépjen kapcsolatba az [F5 ügyfél-támogatási csapattal](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) . Az Azure Portal **alapszintű SAML-konfiguráció** szakaszában látható mintázatokat is megtekintheti.

1. Az **egyszeri bejelentkezés az SAML-vel** lapon az **SAML aláíró tanúsítvány** szakaszban keresse meg az **összevonási metaadatok XML** és **tanúsítvány (Base64)** elemet, majd a **Letöltés** gombra kattintva töltse le a tanúsítványt, és mentse a számítógépre.

    ![A tanúsítvány letöltési hivatkozás](common/metadataxml.png)

1. Az **F5 beállítása** szakaszban másolja a megfelelő URL-címeket a követelmények alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

### <a name="create-an-azure-ad-test-user"></a>Hozzon létre egy Azure ad-ben tesztfelhasználó számára

Ebben a szakaszban egy tesztelési felhasználót hoz létre a Azure Portal B. Simon néven.

1. A Azure Portal bal oldali paneljén válassza a **Azure Active Directory**lehetőséget, válassza a **felhasználók**, majd a **minden felhasználó**lehetőséget.
1. Válassza az **új felhasználó** lehetőséget a képernyő tetején.
1. A **felhasználó** tulajdonságaiban hajtsa végre az alábbi lépéseket:
   1. A **Név** mezőbe írja a következőt: `B.Simon`.  
   1. A **Felhasználónév** mezőbe írja be a username@companydomain.extension. Például: `B.Simon@contoso.com`.
   1. Jelölje be a **jelszó megjelenítése** jelölőnégyzetet, majd írja le a **jelszó** mezőben megjelenő értéket.
   1. Kattintson a **Létrehozás** gombra.

### <a name="assign-the-azure-ad-test-user"></a>Az Azure ad-ben tesztfelhasználó hozzárendelése

Ebben a szakaszban a B. Simon segítségével engedélyezheti az Azure egyszeri bejelentkezést az F5 elérésének biztosításával.

1. A Azure Portal válassza a **vállalati alkalmazások**lehetőséget, majd válassza a **minden alkalmazás**lehetőséget.
1. Az alkalmazások listában válassza az **F5**elemet.
1. Az alkalmazás áttekintés lapján keresse meg a **kezelés** szakaszt, és válassza a **felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozásra](common/users-groups-blade.png)

1. Válassza a **felhasználó hozzáadása**lehetőséget, majd a **hozzárendelés hozzáadása** párbeszédpanelen válassza a **felhasználók és csoportok** lehetőséget.

    ![A felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **felhasználók és csoportok** párbeszédpanelen válassza a felhasználók listából a **B. Simon** lehetőséget, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. Ha az SAML-állításban bármilyen szerepkörre számíthat, a **szerepkör kiválasztása** párbeszédpanelen válassza ki a megfelelő szerepkört a felhasználó számára a listából, majd kattintson a képernyő alján található **kiválasztás** gombra.
1. A **hozzárendelés hozzáadása** párbeszédpanelen kattintson a **hozzárendelés** gombra.
1. Kattintson a **feltételes hozzáférés** lehetőségre.
1. Kattintson az **új házirend**elemre.
1. Mostantól megtekintheti az F5 alkalmazás erőforrásként a HITELESÍTÉSSZOLGÁLTATÓI házirendet, és alkalmazhatja a feltételes hozzáférést, beleértve a többtényezős hitelesítést, az eszközön alapuló hozzáférés-vezérlést vagy az Identity Protection-házirendet.

## <a name="configure-f5-sso"></a>Az F5 SSO konfigurálása

- [Az F5 egyszeri bejelentkezés konfigurálása a Kerberos-alkalmazáshoz](kerbf5-tutorial.md)

- [Az F5 egyszeri bejelentkezés konfigurálása a speciális Kerberos-alkalmazáshoz](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Az F5 egyszeri bejelentkezés konfigurálása a fejléc-alapú alkalmazáshoz

### <a name="guided-configuration"></a>Irányított konfiguráció

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be az F5 (fejléc alapú) vállalati webhelyre rendszergazdaként, és hajtsa végre a következő lépéseket:

1. Navigáljon a **System > tanúsítványkezelő > adatforgalom-tanúsítvány kezelése > SSL-tanúsítvány listára**. Válassza az **Importálás** lehetőséget a jobb oldali sarokban. Adja meg a **tanúsítvány nevét** (a konfiguráció későbbi részében lesz hivatkozva). A **tanúsítvány forrása**területen válassza a fájl feltöltése lehetőséget, hogy az SAML egyszeri bejelentkezés konfigurálásakor az Azure-ból letöltött tanúsítványt adja meg. Kattintson az **Importálás** gombra.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure12.png)
 
1. Emellett **SSL-tanúsítványra lesz szüksége az alkalmazás állomásneve számára. Navigáljon a System > tanúsítványkezelő > adatforgalom-tanúsítvány kezelése > SSL-tanúsítvány listára**. Válassza az **Importálás** lehetőséget a jobb oldali sarokban. AZ **Importálás típusa** **PKCS 12 (IIS)** lesz. Adja meg a **kulcs nevét** (a konfiguráció későbbi részében lesz hivatkozva), majd adja meg a pfx-fájlt. A PFX-fájl **jelszavának** megadása. Kattintson az **Importálás** gombra.

    >[!NOTE]
    >A példában az alkalmazás neve `Headerapp.superdemo.live`, egy wild card-tanúsítványt használunk a kulcsnév `WildCard-SuperDemo.live`.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure13.png)

1. Az Azure AD-összevonás és az alkalmazás-hozzáférés beállításához az interaktív élményt fogjuk használni. Ugrás az – F5 BIG-IP **Main** elemre, és válassza a **hozzáférés > irányított konfiguráció > összevonási > SAML-szolgáltató**elemet. Kattintson a **tovább** gombra, majd a konfigurálás megkezdéséhez kattintson a **tovább** gombra.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure01.png)

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure02.png)
 
1. Adja meg a **konfiguráció nevét**. Határozza meg az **entitás azonosítóját** (ugyanaz, mint amit az Azure ad-alkalmazás konfigurációjában konfigurált). Adja meg az **állomásnév nevét**. Adja meg a hivatkozás **leírását** . Fogadja el a fennmaradó alapértelmezett bejegyzéseket, és válassza ki, majd kattintson a **mentés & tovább**gombra.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure03.png) 

1. Ebben a példában egy új virtuális kiszolgálót hozunk létre a 443-as port 192.168.30.20. A **célként megadott címben**válassza a virtuális kiszolgáló IP-címét. Válassza ki az ügyfél **SSL-profilját**, majd válassza az új létrehozása lehetőséget. Adja meg a korábban feltöltött alkalmazás tanúsítványát (a példában szereplő Wild Card-tanúsítványt) és a társított kulcsot, majd kattintson a **mentés & tovább**gombra.

    >[!NOTE]
    >Ebben a példában a belső webkiszolgáló a 888-as porton fut, és a 443-as verzióban szeretné közzétenni.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure04.png) 

1. A **identitásszolgáltató-összekötő konfigurálásához válassza a módszer lehetőséget**, majd adja meg a metaadatokat, kattintson a fájl kiválasztása lehetőségre, és töltse fel az Azure ad-ből korábban letöltött metaadat-XML-fájlt. Adja meg az SAML IDENTITÁSSZOLGÁLTATÓ-összekötő egyedi **nevét** . Válassza ki a korábban feltöltött **metaadat-aláíró tanúsítványt** . Kattintson a **mentés & tovább**gombra.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure05.png)
 
1. A **készlet kiválasztása**területen adja meg az **új létrehozása** elemet (másik lehetőségként válasszon egy már létező készletet). Hagyja, hogy a többi érték legyen alapértelmezett. A készlet-kiszolgálók területen írja be az IP-címet az **IP-cím/csomópont neve**mezőbe. A **portot**határozza meg. Kattintson a **mentés & tovább**gombra.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure06.png)

1. Az egyszeri bejelentkezés beállításai képernyőn válassza az **egyszeri bejelentkezés engedélyezése**lehetőséget. A kiválasztott egyszeri bejelentkezés típusnál válassza a **http-fejléc-alapú**lehetőséget. Cserélje le a **Session. SAML. Last. Identity** nevű munkamenetet. **SAML. Last. attr. name. Identity** nevet a Felhasználónév forrása mezőben (ez a változó az Azure ad-ben a jogcímek leképezésével van beállítva). Az SSO-fejlécek területen.

    * **HeaderName : MyAuthorization**

    * **Fejléc értéke:% {Session. SAML. Last. attr. name. Identity}**

    * Kattintson a **mentés & tovább** gombra

    A változók és értékek teljes listáját a függelékben találja. Szükség szerint további fejléceket is hozzáadhat.

    >[!NOTE]
    >A fiók neve az F5 delegálási fiók lett létrehozva (az F5 dokumentációjának bejelölése).

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure07.png) 

1. Ebben az útmutatóban kihagyjuk a végponti ellenőrzéseket.  További részletekért tekintse meg az F5 dokumentációját. Válassza a **mentés & tovább**lehetőséget.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure08.png)

1. Fogadja el az alapértelmezett beállításokat, majd kattintson a **mentés & tovább**gombra. Az SAML-munkamenet-kezelési beállításokkal kapcsolatos részletekért tekintse meg az F5 dokumentációját.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure09.png)

1. Tekintse át az összegző képernyőt, és válassza a **telepítés** lehetőséget a Big-IP konfigurálásához. kattintson a **Befejezés**gombra.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure10.png)

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Speciális konfiguráció

Ez a szakasz akkor használható, ha nem tudja használni az irányított konfigurációt, vagy további paramétereket szeretne hozzáadni/módosítani. Az alkalmazási állomásnévhez SSL-tanúsítványt kell megkövetelni.

1. Navigáljon a **System > tanúsítványkezelő > adatforgalom-tanúsítvány kezelése > SSL-tanúsítvány listára**. Válassza az **Importálás** lehetőséget a jobb oldali sarokban. AZ **Importálás típusa** **PKCS 12 (IIS)** lesz. Adja meg a **kulcs nevét** (a konfiguráció későbbi részében lesz hivatkozva), majd adja meg a pfx-fájlt. A PFX-fájl **jelszavának** megadása. Kattintson az **Importálás** gombra.

    >[!NOTE]
    >A példában az alkalmazás neve `Headerapp.superdemo.live`, egy wild card-tanúsítványt használunk a kulcsnév `WildCard-SuperDemo.live`.
  
    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Új webkiszolgáló hozzáadása a BigIP-F5-hez

1. Kattintson a **Main > IApps > alkalmazásszolgáltatás > alkalmazás > létrehozás**elemre.

1. Adja meg a **nevet** és a **sablon** területen válassza az **F5. http**lehetőséget.
 
    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure18.png)

1. Ebben az esetben a HeaderApp2 külsőleg is HTTPS-ként tesszük közzé, **hogyan kell kezelni a Big-IP-rendszer az SSL-forgalmat**? megadjuk az SSL leállítását **az ügyfélről, az egyszerű szöveges kiszolgálókat (SSL-kiszervezést)** . Adja meg a tanúsítványt és a kulcsot, amely alatt az SSL-tanúsítványt használni szeretné? és **melyik SSL-titkos kulcsot szeretné használni?** Megadhatja a virtuális kiszolgáló IP- **címét, hogy milyen IP-címet szeretne használni a virtuális kiszolgálóhoz?** . 

    * **További részletek megadása**

        * TELJES TARTOMÁNYNÉV  

        * Válasszon ki egy kilépő alkalmazáskészletet, vagy hozzon létre egy újat.

        * Új alkalmazáskiszolgáló létrehozásakor **belső IP-címet** és **portszámot**kell megadni.

        ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure19.png) 

1. Kattintson a **kész**gombra.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure20.png) 

1. Győződjön meg arról, hogy az alkalmazás tulajdonságai módosíthatók. Kattintson a **Main > IApps > alkalmazásszolgáltatás: alkalmazások > > HeaderApp2**elemre. Törölje a **szigorú frissítéseket** (a grafikus felhasználói felületen kívül is módosítjuk a beállításokat). Kattintson a **frissítés** gombra.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure21.png) 

1. Ezen a ponton böngészheti a virtuális kiszolgálót.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>Az F5 konfigurálása SP-ként és Azure-ként IDENTITÁSSZOLGÁLTATÓ-ként

1.  Kattintson a **hozzáférés > összevonás > SAML-szolgáltató > helyi SP szolgáltatás elemre > kattintson a létrehozás vagy a + jel gombra**.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure22.png)

1. A szolgáltatói szolgáltatás részleteinek megadása. Adja meg az F5 SP konfigurációt jelképező **nevet** . Az **entitás azonosítójának** megadása (általában ugyanaz, mint az alkalmazás URL-címe).

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure23.png)

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure24.png)

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure25.png)

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure26.png)

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure27.png)

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>Identitásszolgáltató-összekötő létrehozása

1. Kattintson a **identitásszolgáltató-összekötők kötése/** megszüntetése gombra, válassza az **új identitásszolgáltató-összekötő létrehozása** elemet, és válassza a **metaadatokból** lehetőséget, majd hajtsa végre a következő lépéseket:
 
    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure29.png)

    a. Tallózással keresse meg az Azure AD-ből letöltött metadata. xml fájlt, és adjon meg egy **identitás-szolgáltató nevét**.

    b. Kattintson **az OK**gombra.

    c. Létrejön az összekötő, és a tanúsítvány automatikusan készen áll a metaadatok XML-fájljából.
    
    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure30.png)

    d. Konfigurálja a F5BIG-IP-t az összes kérelem az Azure AD-be való küldéséhez.

    e. Kattintson az **új sor hozzáadása**elemre, válassza a **AzureIDP** lehetőséget (az előző lépések során létrehozott módon adja meg a következőt: 

    f. **Egyező forrás =% {Session. Server. landinguri}** 

    g. **Egyező érték =/** *

    h. Kattintson a **frissítés** gombra

    i. Kattintson az **OK** gombra

    j. **Az SAML-IDENTITÁSSZOLGÁLTATÓ telepítése befejeződött**
    
    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>Az F5-házirend konfigurálása a felhasználók Azure SAML-IDENTITÁSSZOLGÁLTATÓ való átirányításához

1. Az F5-házirend konfigurálásához a felhasználókat az Azure SAML-IDENTITÁSSZOLGÁLTATÓ való átirányításához hajtsa végre a következő lépéseket:

    a. Kattintson a **fő > hozzáférés > profil/szabályzatok > hozzáférési profilok**elemre.

    b. Kattintson a **Létrehozás** gombra.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure32.png)
 
    c. Adja meg a **nevet** (a példában a HeaderAppAzureSAMLPolicy).

    d. Egyéb beállításokat is testreszabhat, lásd az F5 dokumentációját.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure33.png)

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure34.png) 

    e. Kattintson a **kész**gombra.

    f. A szabályzat létrehozása után kattintson a házirendre, és válassza a **hozzáférési házirend** lapot.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure35.png)
 
    g. Kattintson a **vizualizációs házirend-szerkesztőre**, és módosítsa a **hozzáférési szabályzatot a profil** hivatkozáshoz.

    h. Kattintson a + jelre a vizualizációs házirend-szerkesztőben, majd válassza az **SAML-hitelesítés**lehetőséget.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure36.png)

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure37.png)
 
    i. Kattintson az **elem hozzáadása**gombra.

    j. A **Tulajdonságok** területen adja meg a **nevet** , és az **AAA-kiszolgáló** területen válassza ki a korábban konfigurált SP-t, kattintson a **Mentés**gombra.
 
    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure38.png)

    k. Az alapszintű szabályzat készen áll a szabályzat testreszabására további források/attribútumok tárolására.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure39.png)
 
    l. Kattintson a felül található **hozzáférési házirend alkalmazása** hivatkozásra.

### <a name="apply-access-profile-to-the-virtual-server"></a>Hozzáférési profil alkalmazása a virtuális kiszolgálóra

1. Rendelje hozzá a hozzáférési profilt a virtuális kiszolgálóhoz, hogy az F5 BIG-IP APM alkalmazza a profil beállításait a bejövő forgalomra, és futtassa a korábban meghatározott hozzáférési szabályzatot.

    a. Kattintson a **fő** > **helyi forgalom** > **virtuális kiszolgálók**elemre.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure40.png)
 
    b. Kattintson a virtuális kiszolgálóra, görgessen a **hozzáférési házirend** szakaszhoz, a **hozzáférési profil** legördülő menüben válassza ki a létrehozott SAML-szabályzatot (a példában HeaderAppAzureSAMLPolicy)

    c. Kattintson a **frissítés** gombra
 
    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure41.png)

    d. hozzon létre egy F5 BIG-IP iRule®, hogy kinyerje az egyéni SAML-attribútumokat a bejövő állításból, és HTTP-fejlécként adja át őket a háttér-tesztelési alkalmazásnak. Kattintson a **fő > helyi forgalom > iRules > IRule listára > kattintson a Létrehozás gombra** .

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure42.png)
 
    e. Illessze be az F5 BIG-IP iRule szöveget alább a definíciós ablakba.

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure43.png)
 
    Ha RULE_INIT {set static::d ebug 0}, ha ACCESS_ACL_ALLOWED {

    Állítsa be AZUREAD_USERNAME [hozzáférés:: munkamenet-adatelérés "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name"] Ha {$static::d ebug} {log local0. "AZUREAD_USERNAME = $AZUREAD _USERNAME"} ha {! ( [HTTP:: a fejléc létezik "AZUREAD_USERNAME"]) } {HTTP:: fejléc beszúrása "AZUREAD_USERNAME" $AZUREAD _USERNAME}

    Állítsa be AZUREAD_DISPLAYNAME [hozzáférés:: munkamenet-adatelérés "session.saml.last.attr.name. http://schemas.microsoft.com/identity/claims/displayname"] Ha {$static::d ebug} {log local0. "AZUREAD_DISPLAYNAME = $AZUREAD _DISPLAYNAME"} ha {! ( [HTTP:: a fejléc létezik "AZUREAD_DISPLAYNAME"]) } {HTTP:: fejléc beszúrása "AZUREAD_DISPLAYNAME" $AZUREAD _DISPLAYNAME}

    Állítsa be AZUREAD_EMAILADDRESS [hozzáférés:: munkamenet-adatelérés "session.saml.last.attr.name. http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress"] Ha {$static::d ebug} {log local0. "AZUREAD_EMAILADDRESS = $AZUREAD _EMAILADDRESS"} ha {! ( [HTTP:: a fejléc létezik "AZUREAD_EMAILADDRESS"]) } {HTTP:: fejléc beszúrása "AZUREAD_EMAILADDRESS" $AZUREAD _EMAILADDRESS}}

    **Példa a kimenetre**

    ![F5 (fejléc alapú) konfiguráció](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>F5 test User létrehozása

Ebben a szakaszban egy B. Simon nevű felhasználót hoz létre az F5 billentyűvel. A felhasználók az F5 platformon való hozzáadásához az [F5 ügyfél-támogatási csapat](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) használható. Felhasználók kell létrehozni és egyszeri bejelentkezés használata előtt aktiválva. 

## <a name="test-sso"></a>Egyszeri bejelentkezés tesztelése 

Ebben a szakaszban tesztelni az Azure AD egyszeri bejelentkezés beállításai a hozzáférési panelen.

Amikor a hozzáférési panelen az F5 csempére kattint, automatikusan be kell jelentkeznie az F5-be, amelyhez be kell állítania az egyszeri bejelentkezést. További információ a hozzáférési panelről: [Bevezetés a hozzáférési panelre](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction).

## <a name="additional-resources"></a>További források

- [Az SaaS-alkalmazások Azure Active Directory-nal való integrálásával kapcsolatos oktatóanyagok listája](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi a feltételes hozzáférés a Azure Active Directory?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Az F5 kipróbálása az Azure AD-vel](https://aad.portal.azure.com/)

- [Az F5 egyszeri bejelentkezés konfigurálása a Kerberos-alkalmazáshoz](kerbf5-tutorial.md)

- [Az F5 egyszeri bejelentkezés konfigurálása a speciális Kerberos-alkalmazáshoz](advance-kerbf5-tutorial.md)

