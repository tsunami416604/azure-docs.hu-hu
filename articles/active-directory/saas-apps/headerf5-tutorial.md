---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az F5-tel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az F5 között.
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
ms.openlocfilehash: 352f52a2a6d84d352bb46e09f104efde303307f5
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478046"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az F5-tel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az F5-öt az Azure Active Directoryval (Azure AD). Ha integrálja az F5-öt az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az F5-höz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve f5 az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD egyszeri bejelentkezéssel való SaaS-alkalmazások integrációjáról, olvassa [el az Azure Active Directory alkalmazásokra való egyszeri bejelentkezés című témakört.](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on)

## <a name="prerequisites"></a>Előfeltételek

A kezdéshez a következő elemekre van szükség:

* Egy Azure AD-előfizetés. Ha nem rendelkezik előfizetéssel, ingyenes [fiókot](https://azure.microsoft.com/free/)kaphat.

* F5 egyszeri bejelentkezés (SSO) engedélyezett előfizetés.

* A közös megoldás üzembe helyezése a következő licenccel rendelkezik:

    * F5 BIG-IP® Legjobb csomag (vagy) 

    * F5 BIG-IP access policy manager™ (APM) önálló licenc 

    * F5 BIG-IP Access Policy Manager™ (APM) bővítménylicenc egy meglévő BIG-IP F5 BIG-IP-n® Helyi Forgalomkezelő™ (LTM).

    * A fenti licencen kívül az F5 rendszer a következő licencekkel is licencelhető: 

        * URL-szűrő előfizetés az URL-kategória adatbázisának használatához

        * F5 IP Intelligence-előfizetés az ismert támadók és a rosszindulatú forgalom észlelésére és blokkolására
     
        * Hálózati hardverbiztonsági modul (HSM) a digitális kulcsok védelmére és kezelésére az erős hitelesítéshez

* Az F5 BIG-IP rendszer APM modulokkal van kiépítve (az LTM nem kötelező)

* Bár nem kötelező, erősen ajánlott az F5-rendszerek telepítése egy [szinkronizálási/feladatátvételi eszközcsoportban](https://techdocs.f5.com/content/techdocs/en-us/bigip-14-1-0/big-ip-device-service-clustering-administration-14-1-0.html) (S/F DG), amely tartalmazza az aktív készenléti pár, egy lebegő IP-címet a magas rendelkezésre állás (HA). A kapcsolatösszesítés-ellenőrzési protokoll (LACP) használatával további illesztőredundancia érhető el. Az LACP egyetlen virtuális kapcsolatként (összesítő csoportként) kezeli a csatlakoztatott fizikai felületeket, és észleli a csoporton belüli kapcsolathibákat.

* Kerberos-alkalmazások esetén a korlátozott delegáláshoz helyszíni AD-szolgáltatásfiók.  Az AD delegálási fiók létrehozásáról az [F5 dokumentációban](https://support.f5.com/csp/article/K43063049) olvashat.

## <a name="access-guided-configuration"></a>Irányított konfiguráció elérése

* Hozzáférés vezérelt konfigurációja" az F5 TMOS 13.1.0.8-as és újabb verzióiban támogatott. Ha a BIG-IP rendszer 13.1.0.8 alatti verziót futtat, olvassa el a **Speciális konfiguráció című** részt.

* Az Access irányított konfiguráció teljesen új és áramvonalas felhasználói élményt nyújt. Ez a munkafolyamat-alapú architektúra intuitív, újrabelépő konfigurációs lépéseket biztosít a kiválasztott topológiához igazítva.

* Mielőtt továbblépne a konfigurációra, frissítse az irányított konfigurációt a legújabb használati esetcsomag letöltésével [downloads.f5.com.](https://login.f5.com/resource/login.jsp?ctx=719748) A frissítéshez kövesse az alábbi eljárást.

    >[!NOTE]
    >Az alábbi képernyőképek a legújabb verzióra (BIG-IP 15.0 az AGC 5.0-s verziójával). Az alábbi konfigurációs lépések a 13.1.0.8-tól a legújabb BIG-IP verzióig érvényesek.

1. Az F5 BIG-IP web felhasználói felületén kattintson **az Access >> útmutató konfigurációjára**.

1. Az **Irányított konfiguráció lapon** kattintson az **Irányított konfiguráció frissítése** elemre a bal felső sarokban.

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure14.png) 

1. A Frissítési útmutató konfigurációja előugró képernyőn válassza a **Fájl kiválasztása lehetőséget** a letöltött használati esetcsomag feltöltéséhez, majd kattintson a **Feltöltés és telepítés** gombra.

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure15.png) 

1. Amikor a frissítés befejeződött, kattintson a **Folytatás** gombra.

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az F5 SSO három különböző módon konfigurálható.

- [Az F5 egyszeri bejelentkezésének konfigurálása fejlécalapú alkalmazáshoz](#configure-f5-single-sign-on-for-header-based-application)

- [Az F5 egyszeri bejelentkezéskonfigurálása a Kerberos-alkalmazáshoz](kerbf5-tutorial.md)

- [Az F5 egyszeri bejelentkezéskonfigurálása speciális Kerberos-alkalmazáshoz](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Kulcshitelesítési forgatókönyvek

* Az Azure Active Directory natív integrációs támogatása mellett az Azure Active Directory natív integrációs támogatása a modern hitelesítési protokollok, például az Open ID Connect, SAML és WS-Fed, F5 kiterjeszti a biztonságos hozzáférést az örökölt alapú hitelesítési alkalmazások mind a belső és külső hozzáférés az Azure AD, amely lehetővé teszi a modern forgatókönyvek (pl. jelszó nélküli hozzáférés) ezeket az alkalmazásokat. Ez a következőket foglalja magában:

* Fejlécalapú hitelesítési alkalmazások

* Kerberos-hitelesítési alkalmazások

* Névtelen hitelesítés vagy beépített hitelesítési alkalmazások nélkül

* NTLM-hitelesítési alkalmazások (védelem kettős kérdéssel a felhasználó számára)

* Űrlapalapú alkalmazás (védelem kettős kérdéssel a felhasználó számára)

## <a name="adding-f5-from-the-gallery"></a>F5 hozzáadása a galériából

Az F5 Azure AD-be való integrációjának konfigurálásához hozzá kell adnia az F5-öt a katalógusból a felügyelt SaaS-alkalmazások listájához.

1. Jelentkezzen be az [Azure Portalra](https://portal.azure.com) munkahelyi vagy iskolai fiókkal vagy személyes Microsoft-fiókkal.
1. A bal oldali navigációs ablakban válassza ki az **Azure Active Directory** szolgáltatást.
1. Nyissa meg a **Vállalati alkalmazások elemet,** és válassza **a Minden alkalmazás lehetőséget.**
1. Új alkalmazás hozzáadásához válassza az **Új alkalmazás**lehetőséget.
1. A **hozzáadás a gyűjteményből szakaszban** írja be az **F5** értéket a keresőmezőbe.
1. Válassza az **F5** lehetőséget az eredménypanelen, majd adja hozzá az alkalmazást. Várjon néhány másodpercet, amíg az alkalmazás hozzáadódik a bérlőhöz.

## <a name="configure-and-test-azure-ad-single-sign-on-for-f5"></a>Az Azure AD egyszeri bejelentkezéskonfigurálása és tesztelése az F5-hez

Konfigurálja és tesztelje az Azure AD SSO-t az F5-tel egy **B.Simon**nevű tesztfelhasználó használatával. Ahhoz, hogy az SSO működjön, létre kell hoznia egy kapcsolat kapcsolatot egy Azure AD-felhasználó és a kapcsolódó felhasználó között az F5-ben.

Az Azure AD SSO f5-tel való konfigurálásához és teszteléséhez hajtsa végre a következő építőelemeket:

1. **[Konfigurálja az Azure AD egyszeri szolgáltatást](#configure-azure-ad-sso)** – lehetővé teszi a felhasználók számára a funkció használatát.
    1. **[Hozzon létre egy Azure AD-teszt felhasználó](#create-an-azure-ad-test-user)** – az Azure AD egyszeri bejelentkezés b.Simon teszteléséhez.
    1. **[Rendelje hozzá az Azure AD-teszt felhasználó](#assign-the-azure-ad-test-user)** – lehetővé teszi b.Simon azure AD egyszeri bejelentkezés.
1. **[Konfigurálja az F5 Egyszeri bejelentkezést](#configure-f5-sso)** – az egyszeri bejelentkezési beállítások konfigurálásához az alkalmazás oldalon.
    1. **[Hozzon létre F5 tesztfelhasználót](#create-f5-test-user)** – b.Simon megfelelője az F5-ben, amely a felhasználó Azure AD-megjelenítéséhez kapcsolódik.
1. **[SSO tesztelése](#test-sso)** - annak ellenőrzéséhez, hogy a konfiguráció működik-e.

## <a name="configure-azure-ad-sso"></a>Az Azure AD SSO konfigurálása

Kövesse az alábbi lépéseket az Azure AD SSO engedélyezéséhez az Azure Portalon.

1. Az [Azure Portalon](https://portal.azure.com/)az **F5** alkalmazásintegrációs lapon keresse meg a **Kezelés szakaszt,** és válassza az **egyszeri bejelentkezés**lehetőséget.
1. Az **Egyetlen bejelentkezési módszer kiválasztása** lapon válassza az **SAML**lehetőséget.
1. A **Beállítások beállítása SAML-lel** lapon kattintson az **egyszerű SAML-konfiguráció** szerkesztési/tollikonjára a beállítások szerkesztéséhez.

   ![Egyszerű SAML-konfiguráció szerkesztése](common/edit-urls.png)

1. Az **Egyszerű SAML-konfiguráció** szakaszban, ha az alkalmazást **IDP** által kezdeményezett módban szeretné konfigurálni, adja meg a következő mezők értékeit:

    a. Az **Azonosító** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<YourCustomFQDN>.f5.com/`

    b. A **Válasz URL-cím** mezőjébe írjon be egy URL-címet a következő minta használatával:`https://<YourCustomFQDN>.f5.com/`

1. Kattintson **a További URL-címek beállítása** elemre, és hajtsa végre a következő lépést, ha az alkalmazást **SP** által kezdeményezett módban kívánja konfigurálni:

    A **Bejelentkezési URL-cím** mezőbe írjon be egy URL-címet a következő minta használatával:`https://<YourCustomFQDN>.f5.com/`

    > [!NOTE]
    > Ezek az értékek nem valósak. Frissítse ezeket az értékeket a tényleges azonosítóval, a válasz URL-címével és a bejelentkezési URL-címmel. Lépjen kapcsolatba [az F5 ügyféltámogatási csapatával](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) az értékek lefelvételéhez. Az Azure Portal **alapszintű SAML-konfigurációs** szakaszában látható mintákat is hivatkozhat.

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadat-XML** és **tanúsítvány (Base64)** lapot, és válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

    ![A tanúsítvány letöltési hivatkozása](common/metadataxml.png)

1. Az **F5 beállítása** szakaszban másolja a megfelelő URL-cím(eke)t a követelmény alapján.

    ![Konfigurációs URL-címek másolása](common/copy-configuration-urls.png)

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

Ebben a szakaszban engedélyezi b.Simon azure egyszeri bejelentkezés t az F5-höz való hozzáférés biztosításával.

1. Az Azure Portalon válassza a **Vállalati alkalmazások**lehetőséget, majd az **Összes alkalmazás**lehetőséget.
1. Az alkalmazások listájában válassza az **F5**lehetőséget.
1. Az alkalmazás áttekintő lapján keresse meg a **Kezelés szakaszt,** és válassza a **Felhasználók és csoportok**lehetőséget.

   ![A "Felhasználók és csoportok" hivatkozás](common/users-groups-blade.png)

1. Válassza **a Felhasználó hozzáadása**lehetőséget, majd a Hozzárendelés **hozzáadása** párbeszédpanelen válassza a Felhasználók **és csoportok** lehetőséget.

    ![A Felhasználó hozzáadása hivatkozás](common/add-assign-user.png)

1. A **Felhasználók és csoportok** párbeszédpanelen válassza a **B.Simon** elemet a Felhasználók listában, majd kattintson **a** kijelölés gombra a képernyő alján.
1. Ha az SAML-helyességben szerepkörértéket vár, a **Szerepkör kiválasztása** párbeszédpanelen válassza ki a felhasználó számára megfelelő szerepkört a listából, majd kattintson **a** kijelölés gombra a képernyő alján.
1. A **Hozzárendelés hozzáadása** párbeszédpanelen kattintson a **Hozzárendelés** gombra.
1. Kattintson a **Feltételes hozzáférés gombra.**
1. Kattintson az **Új házirend gombra.**
1. Most már láthatja az F5-ös alkalmazást a hitelesítésvédelmi szabályzat egyik erőforrásaként, és alkalmazhat bármilyen feltételes hozzáférést, beleértve a Multifactor Auth-ot, az eszközalapú hozzáférés-vezérlést vagy az identitásvédelmi szabályzatot.

## <a name="configure-f5-sso"></a>F5 sso konfigurálása

- [Az F5 egyszeri bejelentkezéskonfigurálása a Kerberos-alkalmazáshoz](kerbf5-tutorial.md)

- [Az F5 egyszeri bejelentkezéskonfigurálása speciális Kerberos-alkalmazáshoz](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-header-based-application"></a>Az F5 egyszeri bejelentkezésének konfigurálása fejlécalapú alkalmazáshoz

### <a name="guided-configuration"></a>Irányított konfiguráció

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként az F5 (Fejlécalapú) vállalati webhelyre, és hajtsa végre a következő lépéseket:

1. Nyissa meg **a Rendszer> tanúsítványkezelés > forgalmi tanúsítványkezelés > SSL-tanúsítványlista .** Válassza az **Importálás lehetőséget** a jobb oldali sarokban. Adja meg **a tanúsítvány nevét** (a konfiguráció ban később hivatkozni fog). A **tanúsítványforrásban**válassza a Fájl feltöltése lehetőséget, adja meg az Azure-ból letöltött tanúsítványt az SAML Single Sign on konfigurálása közben. Kattintson **az Importálás gombra.**

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure12.png)
 
1. Ezenkívül **az alkalmazás állomásnevéhez SSL-tanúsítványszükséges. Nyissa meg a Rendszer> tanúsítványkezelés > a forgalomvédelem > az SSL-tanúsítványlista.** Válassza az **Importálás lehetőséget** a jobb oldali sarokban. **Az importálás típusa** **PKCS 12(IIS)** lesz. Adja meg **a kulcs nevét** (a rendszer később a konfigurációban hivatkozik), és adja meg a PFX-fájlt. Adja meg a PFX **jelszavát.** Kattintson **az Importálás gombra.**

    >[!NOTE]
    >A példában az `Headerapp.superdemo.live`alkalmazásunk neve , mi használ egy `WildCard-SuperDemo.live`Wild Card tanúsítvány a kulcsnév.

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure13.png)

1. Az Irányított élményt az Azure AD-összevonás és az alkalmazáshozzáférés beállításához használjuk. Ugrás – F5 BIG-IP **Main,** és válassza **az Access > irányított konfigurációs > összevonási > AZ SAML-szolgáltató lehetőséget.** Kattintson a **Tovább** gombra, majd a **Tovább** gombra a konfiguráció megkezdéséhez.

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure01.png)

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure02.png)
 
1. Adja meg **a konfiguráció nevét**. Adja meg az **entitásazonosítót** (ugyanaz, mint amit az Azure AD alkalmazás konfigurációja). Adja meg az **állomásnevét**. Adjon hozzá **egy ismertetési leírást** a hivatkozáshoz. Fogadja el a többi alapértelmezett bejegyzést, és jelölje ki **& Mentés a Tovább**gombot.

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure03.png) 

1. Ebben a példában egy új virtuális kiszolgálót hozunk létre 192.168.30.20 néven a 443-as porttal. Adja meg a virtuális kiszolgáló IP-címét a **célcímben.** Válassza az Ügyfél **SSL-profilját,** és válassza az Új létrehozása lehetőséget. Adja meg a korábban feltöltött alkalmazástanúsítványt (a példában szereplő helyettesítő kártya tanúsítványát) és a társított kulcsot, majd kattintson **a Mentés & Tovább gombra.**

    >[!NOTE]
    >ebben a példában a belső webszerver fut port 888 és szeretnénk közzétenni azt a 443.

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure04.png) 

1. Az **IdP-összekötő konfigurálásához a Módszer kiválasztása csoportban**adja meg a metaadatokat, kattintson a Fájl kiválasztása elemre, és töltse fel az Azure AD-ből korábban letöltött metaadat-XML-fájlt. Adja meg az SAML IDP-összekötő egyedi **nevét.** Válassza ki a korábban feltöltött **metaadat-aláíró tanúsítványt.** Kattintson **& Tovább mentés gombra.**

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure05.png)
 
1. A **Készlet kiválasztása csoportban**adja meg az **Új létrehozása lehetőséget** (másik lehetőségként jelöljön ki egy már létező készletet). Hagyja, hogy más érték legyen az alapértelmezett érték. A Készletkiszolgálók csoportban írja be az IP-címet az **IP-cím/csomópont név mezőbe.** Adja meg a **portot**. Kattintson **& Tovább mentés gombra.**

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure06.png)

1. Az Egyszeri bejelentkezés beállításai képernyőn válassza az **Egyszeri bejelentkezés engedélyezése**lehetőséget. A Kijelölt egyszeri bejelentkezési típus csoportban válassza a **HTTP fejlécalapú**lehetőséget. Cserélje **le a session.saml.last.Identity-t** **a session.saml.last.attr.name.Identity** kifejezésre a Felhasználónév forrás csoportban (ez a változó az Azure AD-ben a jogcímleképezés használatával beállított). Az SSO-fejlécek csoportban.

    * **Fejlécneve : Sajatengedélyez**

    * **Fejlécértéke : %{session.saml.last.attr.name.Identity}**

    * Kattintson **a Mentés & Tovább gombra**

    A változók és értékek teljes listájához lásd a függeléket. Szükség szerint további fejléceket is hozzáadhat.

    >[!NOTE]
    >A fióknév az F5 delegálási fiók létrehozása (Ellenőrizze az F5 dokumentációt).

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure07.png) 

1. Ennek az útmutatónak az alkalmazásában kihagyjuk a végpont-ellenőrzéseket.  A részleteket az F5 dokumentációjában találja. Válassza **a Mentés & Tovább**lehetőséget.

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure08.png)

1. Fogadja el az alapértelmezett beállításokat, és kattintson **a Mentés & Tovább gombra**. Az SAML-munkamenet-kezelési beállításokkal kapcsolatos részletekért tekintse meg az F5 dokumentációját.

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure09.png)

1. Tekintse át az összefoglaló képernyőt, és válassza **a Telepítés lehetőséget** a BIG-IP konfigurálásához. kattintson a **Befejezés gombra.**

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure10.png)

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure11.png)

## <a name="advanced-configuration"></a>Speciális konfiguráció

Ez a szakasz akkor használható, ha nem tudja használni az irányított konfigurációt, vagy további paramétereket szeretne hozzáadni/módosítani. Az alkalmazásállomásnevéhez TLS/SSL-tanúsítvány szükséges.

1. Nyissa meg **a Rendszer> tanúsítványkezelés > forgalmi tanúsítványkezelés > SSL-tanúsítványlista .** Válassza az **Importálás lehetőséget** a jobb oldali sarokban. **Az importálás típusa** **PKCS 12(IIS)** lesz. Adja meg **a kulcs nevét** (a rendszer később a konfigurációban hivatkozik), és adja meg a PFX-fájlt. Adja meg a PFX **jelszavát.** Kattintson **az Importálás gombra.**

    >[!NOTE]
    >A példában az `Headerapp.superdemo.live`alkalmazásunk neve , mi használ egy `WildCard-SuperDemo.live`Wild Card tanúsítvány a kulcsnév.
  
    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure17.png)

### <a name="adding-a-new-web-server-to-bigip-f5"></a>Új webkiszolgáló hozzáadása a BigIP-F5-höz

1. Kattintson **> az IApps > Az Alkalmazásszolgáltatások > az alkalmazás > létrehozása > fő hivatkozási alapja iApps >.**

1. Adja meg a **Nevet,** és a **Sablon csoportban** válassza **az f5.http**lehetőséget.
 
    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure18.png)

1. Ebben az esetben a HeaderApp2-t külsőleg https-ként tesszük közzé, **hogyan kezelje a BIG-IP rendszer az SSL forgalmat?** az **SSL leállítása az ügyféltől, az egyszerű szöveg a kiszolgálókhoz (SSL kiszervezés)** adható meg. Adja meg a tanúsítványt és a kulcsot a Melyik **Which SSL private key do you want to use?** **SSL-tanúsítvány használata csoportban?** Adja meg a virtuális kiszolgáló IP-címét **a Virtuális kiszolgálóhoz használni kívánt IP-cím csoportban.**. 

    * **Egyéb részletek megadása**

        * FQDN  

        * Adja meg a kilépő alkalmazáskészletből való kilépést, vagy hozzon létre egy újat.

        * Ha új App Server rendszert hoz létre, adja meg **a belső IP-címet** és **a portszámot**.

        ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure19.png) 

1. Kattintson a **Kész gombra.**

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure20.png) 

1. Győződjön meg arról, hogy az alkalmazás tulajdonságai módosíthatók. Kattintson **a Fő > IApps > Application Services: Applications >> HeaderApp2**elemre. Törölje a jelet **a Szigorú frissítések jelölőnégyzetből** (a gui-n kívüli beállításokat módosítjuk). Kattintson **a Frissítés** gombra.

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure21.png) 

1. Ezen a ponton képesnek kell lennie arra, hogy böngésszen a virtuális szerver.

### <a name="configuring-f5-as-sp-and-azure-as-idp"></a>Az F5 konfigurálása SP-ként és Azure-ként IDP-ként

1.  Kattintson **az Access > összevonási> saml-szolgáltató > helyi SP-szolgáltatás > kattintson a Create or + sign gombra.**

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure22.png)

1. Adja meg a szolgáltatószolgáltatás részleteit. Adja meg az F5 SP konfigurációját jelölő **nevet.** Adja meg **az entitásazonosítót** (általában megegyezik az alkalmazás URL-címével).

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure23.png)

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure24.png)

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure25.png)

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure26.png)

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure27.png)

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure28.png)

### <a name="create-idp-connector"></a>Idp-összekötő létrehozása

1. Kattintson **a Kötés/kötés IdP-összekötők** gombjára, válassza **az Új IdP-összekötő létrehozása lehetőséget,** majd válassza a **Metaadatok** lehetőséget, majd hajtsa végre az alábbi lépéseket:
 
    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure29.png)

    a. Tallózással keresse meg az Azure AD-ből letöltött metadata.xml fájlt, és adja meg **az identitásszolgáltató nevét.**

    b. Kattintson **az OK gombra.**

    c. Az összekötő létrejön, és a tanúsítvány automatikusan készen áll a metaadat-XML-fájlból.
    
    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure30.png)

    d. Állítsa be az F5BIG-IP-t úgy, hogy az összes kérést az Azure AD-nek küldje el.

    e. Kattintson **az Új sor hozzáadása**gombra, válassza az **AzureIDP** lehetőséget (az előző lépésekben létrehozott módon adja meg 

    f. **Megfelelő forrás = %{session.server.landinguri}** 

    g. **Egyező érték = /***

    h. Kattintson a **frissítés gombra**

    i. Kattintson **az OK gombra**

    j. **Az SAML IDP beállítása befejeződött**
    
    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure31.png)

### <a name="configure-f5-policy-to-redirect-users-to-azure-saml-idp"></a>Az F5-szabályzat konfigurálása a felhasználók azure-saml IDP-re való átirányításához

1. Ha az F5 szabályzatot úgy szeretné beállítani, hogy a felhasználókat átirányítsa az Azure SAML IDP-be, hajtsa végre a következő lépéseket:

    a. Kattintson **a Fő > Access > a profil-/házirendek > a hozzáférési profilok elemre.**

    b. Kattintson a **Létrehozás** gombra.

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure32.png)
 
    c. Adja meg **a nevét** (HeaderAppAzureSAMLPolicy a példában).

    d. Más beállításokat is testreszabhat, kérjük, olvassa el az F5 dokumentációját.

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure33.png)

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure34.png) 

    e. Kattintson a **Kész gombra.**

    f. A házirend létrehozása után kattintson a Szabályzatra, és lépjen a **Hozzáférési házirend** lapra.

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure35.png)
 
    g. Kattintson a **Visual Policy szerkesztőre,** a **Hozzáférési házirend szerkesztése a Profilhoz** hivatkozásra.

    h. Kattintson a + Jelentkezzen be a Vizuális házirend szerkesztőre, és válassza az **SAML Auth**lehetőséget.

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure36.png)

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure37.png)
 
    i. Kattintson **az Elem hozzáadása gombra.**

    j. A **Tulajdonságok** megadása a Név **csoportban** a **Név** megadása csoportban válassza ki a korábban konfigurált SP **szervizszervizt,** és kattintson a MENTÉS gombra.
 
    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure38.png)

    k. Az alapszintű szabályzat készen áll, testreszabhatja a házirendet, hogy további forrásokat/attribútumtárolókat építsen be.

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure39.png)
 
    l. Győződjön meg róla, hogy a hozzáférési **házirend alkalmazása** hivatkozásra kattint a tetején.

### <a name="apply-access-profile-to-the-virtual-server"></a>Hozzáférési profil alkalmazása a virtuális kiszolgálóra

1. Rendelje hozzá a hozzáférési profilt a virtuális kiszolgálóhoz annak érdekében, hogy az F5 BIG-IP APM a profilbeállításokat a bejövő forgalomra alkalmazhassa, és futtassa a korábban definiált hozzáférési házirendet.

    a. Kattintson **a Fő** > **helyi forgalom** > **virtuális kiszolgálói elemre.**

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure40.png)
 
    b. Kattintson a virtuális kiszolgálóra, görgessen az **Access Policy** szakaszhoz, az Access Profile legördülő **menüben** válassza ki a létrehozott SAML-szabályzatot (a példa FejlécappAppAzureSAMLPolicy)

    c. Kattintson a **frissítés gombra**
 
    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure41.png)

    d. hozzon létre egy F5 BIG-IP iRule® az egyéni SAML-attribútumok kibontásához a bejövő állításból, és adja át őket HTTP-fejlécként a háttérszintű tesztalkalmazásnak. Kattintson **a Fő > helyi forgalmi > iRules > az iRule lista elemre, > kattintson a létrehozás gombra.**

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure42.png)
 
    e. Illessze be az F5 BIG-IP iRule szöveget a Definíció ablakba.

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure43.png)
 
    amikor RULE_INIT { set static::debug 0 } when ACCESS_ACL_ALLOWED {

    az [ACCESS::session data" AZUREAD_USERNAMEhttp://schemas.xmlsoap.org/ws/2005/05/identity/claims/namebeállítása "session.saml.last.attr.name. "] lesz, ha { $static::debug } { log local0. "AZUREAD_USERNAME = $AZUREAD_USERNAME" } ha { !( [HTTP::header létezik "AZUREAD_USERNAME"]) } { HTTP::header insert "AZUREAD_USERNAME" $AZUREAD_USERNAME }

    az [ACCESS::session data" AZUREAD_DISPLAYNAMEhttp://schemas.microsoft.com/identity/claims/displaynamebekövetkező "session.saml.last.attr.name. "] ha { $static::debug } { log local0 lesz. "AZUREAD_DISPLAYNAME = $AZUREAD_DISPLAYNAME" } ha { !( [HTTP::header létezik "AZUREAD_DISPLAYNAME"]) } { HTTP::header insert "AZUREAD_DISPLAYNAME" $AZUREAD_DISPLAYNAME }

    az [ACCESS::session data" AZUREAD_EMAILADDRESShttp://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddressbeállítása "session.saml.last.attr.name. "] lesz, ha { $static::debug } { log local0. "AZUREAD_EMAILADDRESS = $AZUREAD_EMAILADDRESS" } ha { !( [HTTP::header létezik "AZUREAD_EMAILADDRESS"]) } { HTTP::header insert "AZUREAD_EMAILADDRESS" $AZUREAD_EMAILADDRESS }}

    **Az alábbi mintakimenet**

    ![F5 (fejlécalapú) konfiguráció](./media/headerf5-tutorial/configure44.png)
 
### <a name="create-f5-test-user"></a>F5 tesztfelhasználó létrehozása

Ebben a szakaszban hozzon létre egy B.Simon nevű felhasználót az F5-ben. Az [F5 ügyféltámogatási csapatával](https://support.f5.com/csp/knowledge-center/software/BIG-IP?module=BIG-IP%20APM45) együttműködve adja hozzá a felhasználókat az F5 platformon. Az egyszeri bejelentkezés használata előtt létre kell hozni és aktiválni kell a felhasználókat. 

## <a name="test-sso"></a>SSO tesztelése 

Ebben a szakaszban az Azure AD egyszeri bejelentkezési konfigurációját a hozzáférési panelen teszteli.

Amikor a hozzáférési panelen az F5 csempére kattint, automatikusan be kell jelentkeznie arra az F5-be, amelyhez az SSO-t beállította. A Hozzáférési panelről további információt a [Hozzáférési panel – Bevezetés című témakörben talál.](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

## <a name="additional-resources"></a>További források

- [Útmutatók a SaaS-alkalmazások Azure Active Directoryval való integrálásáról](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)

- [Mi az alkalmazás-hozzáférés és egyszeri bejelentkezés az Azure Active Directoryval?](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

- [Mi az a feltételes hozzáférés az Azure Active Directoryban?](https://docs.microsoft.com/azure/active-directory/conditional-access/overview)

- [Próbálja ki az F5-öt az Azure AD-vel](https://aad.portal.azure.com/)

- [Az F5 egyszeri bejelentkezéskonfigurálása a Kerberos-alkalmazáshoz](kerbf5-tutorial.md)

- [Az F5 egyszeri bejelentkezéskonfigurálása speciális Kerberos-alkalmazáshoz](advance-kerbf5-tutorial.md)

