---
title: 'Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az F5-tel | Microsoft dokumentumok'
description: Ismerje meg, hogyan konfigurálhatja az egyszeri bejelentkezést az Azure Active Directory és az F5 között.
services: active-directory
documentationCenter: na
author: jeevansd
manager: mtillman
ms.reviewer: barbkess
ms.assetid: 39382eab-05fe-4dc2-8792-62d742dfb4e1
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 11/19/2019
ms.author: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: a1f06b0b5aa59328d2fe39d501cfdf3ad7524427
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75431462"
---
# <a name="tutorial-azure-active-directory-single-sign-on-sso-integration-with-f5"></a>Oktatóanyag: Az Azure Active Directory egyszeri bejelentkezési (SSO) integrációja az F5-tel

Ebben az oktatóanyagban megtudhatja, hogyan integrálhatja az F5-öt az Azure Active Directoryval (Azure AD). Ha integrálja az F5-öt az Azure AD-vel, a következőket teheti:

* Szabályozhatja az Azure AD-ben, aki hozzáfér az F5-höz.
* Lehetővé teszi a felhasználók számára, hogy automatikusan bejelentkezve f5 az Azure AD-fiókok.
* Kezelje fiókjait egyetlen központi helyen – az Azure Portalon.

Ha többet szeretne megtudni az Azure AD-vel való SaaS-alkalmazások integrációjáról, olvassa el [a Mi az alkalmazás-hozzáférés és az egyszeri bejelentkezés az Azure Active Directoryval című témakörben.](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

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

2. Az **Irányított konfiguráció lapon** kattintson az **Irányított konfiguráció frissítése** elemre a bal felső sarokban.

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure14.png) 

3. A Frissítési útmutató konfigurációja előugró képernyőn válassza a **Fájl kiválasztása lehetőséget** a letöltött használati esetcsomag feltöltéséhez, majd kattintson a **Feltöltés és telepítés** gombra.

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure15.png) 

4. Amikor a frissítés befejeződött, kattintson a **Folytatás** gombra.

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure16.png)

## <a name="scenario-description"></a>Forgatókönyv leírása

Ebben az oktatóanyagban konfigurálja és teszteli az Azure AD SSO-t egy tesztkörnyezetben.

* Az F5 támogatja az **SP és az IDP** által kezdeményezett sso-t
* Az F5 SSO három különböző módon konfigurálható.

- [Az F5 egyszeri bejelentkezéskonfigurálása a Kerberos-alkalmazáshoz](#configure-f5-single-sign-on-for-kerberos-application)

- [Az F5 egyszeri bejelentkezésének konfigurálása fejlécalapú alkalmazáshoz](headerf5-tutorial.md)

- [Az F5 egyszeri bejelentkezéskonfigurálása speciális Kerberos-alkalmazáshoz](advance-kerbf5-tutorial.md)

### <a name="key-authentication-scenarios"></a>Kulcshitelesítési forgatókönyvek

Az Azure Active Directory natív integrációs támogatása mellett az Olyan modern hitelesítési protokollok, mint az Open ID Connect, az SAML és a WS-Fed protokollok esetében az F5 kiterjeszti a biztonságos hozzáférést az örökölt alapú hitelesítési alkalmazásokhoz mind a belső, mind a külső hozzáféréshez az Azure AD-vel, lehetővé téve a forgatókönyvek (pl. jelszó nélküli hozzáférés) ezen alkalmazásokhoz. Ez a következőket foglalja magában:

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

1. Az **Egyszeri bejelentkezés beállítása SAML-lel** lapon az SAML aláíró tanúsítvány szakaszban keresse meg az **összevonási** **metaadat-XML** és **tanúsítvány (Base64)** lapot, majd válassza a **Letöltés** lehetőséget a tanúsítvány letöltéséhez és a számítógépre mentéséhez.

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

- [Az F5 egyszeri bejelentkezésének konfigurálása fejlécalapú alkalmazáshoz](headerf5-tutorial.md)

- [Az F5 egyszeri bejelentkezéskonfigurálása speciális Kerberos-alkalmazáshoz](advance-kerbf5-tutorial.md)

### <a name="configure-f5-single-sign-on-for-kerberos-application"></a>Az F5 egyszeri bejelentkezéskonfigurálása a Kerberos-alkalmazáshoz

### <a name="guided-configuration"></a>Irányított konfiguráció

1. Nyisson meg egy új böngészőablakot, és jelentkezzen be rendszergazdaként az F5 (Kerberos) vállalati webhelyére, és hajtsa végre az alábbi lépéseket:

1. A metaadat-tanúsítványt importálnia kell az F5-be, amelyet a beállítási folyamat későbbi részében fog használni.

1. Nyissa meg **a Rendszer> tanúsítványkezelés > forgalmi tanúsítványkezelés > SSL-tanúsítványlista .** Válassza az **Importálás lehetőséget** a jobb oldali sarokban. Adja meg **a tanúsítvány nevét** (a konfiguráció ban később hivatkozni fog). A **tanúsítványforrásban**válassza a Fájl feltöltése lehetőséget, adja meg az Azure-ból letöltött tanúsítványt az SAML Single Sign on konfigurálása közben. Kattintson **az Importálás gombra.**

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure01.png) 

1. Ezenkívül **az alkalmazás állomásnevéhez SSL-tanúsítványszükséges. Nyissa meg a Rendszer> tanúsítványkezelés > a forgalomvédelem > az SSL-tanúsítványlista.** Válassza az **Importálás lehetőséget** a jobb oldali sarokban. **Az importálás típusa** **PKCS 12(IIS)** lesz. Adja meg **a kulcs nevét** (a rendszer később a konfigurációban hivatkozik), és adja meg a PFX-fájlt. Adja meg a PFX **jelszavát.** Kattintson **az Importálás gombra.**

    >[!NOTE]
    >A példában az `Kerbapp.superdemo.live`alkalmazásunk neve , wild card tanúsítványt használunk, a kulcsnevünk`WildCard-SuperDemo.live`

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure02.png) 
 
1. Az Irányított élményt az Azure AD-összevonás és az alkalmazáshozzáférés beállításához használjuk. Ugrás – F5 BIG-IP **Main,** és válassza **az Access > irányított konfigurációs > összevonási > AZ SAML-szolgáltató lehetőséget.** Kattintson a **Tovább** gombra, majd a **Tovább** gombra a konfiguráció megkezdéséhez.

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure03.png) 

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure04.png)

1. Adja meg **a konfiguráció nevét**. Adja meg az **entitásazonosítót** (ugyanaz, mint amit az Azure AD alkalmazás konfigurációja). Adja meg az **állomásnevét**. Adjon hozzá **egy ismertetési leírást** a hivatkozáshoz. Fogadja el a többi alapértelmezett bejegyzést, és jelölje ki **& Mentés a Tovább**gombot.

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure05.png) 

1. Ebben a példában egy új virtuális kiszolgálót hozunk létre 192.168.30.200-as 443-as porttal. Adja meg a virtuális kiszolgáló IP-címét a **célcímben.** Válassza az Ügyfél **SSL-profilját,** és válassza az Új létrehozása lehetőséget. Adja meg a korábban feltöltött alkalmazástanúsítványt (a példában szereplő helyettesítő kártya tanúsítványát) és a társított kulcsot, majd kattintson **a Mentés & Tovább gombra.**

    >[!NOTE]
    >ebben a példában a belső webszerver fut port 80 és szeretnénk közzétenni azt a 443.

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure06.png)

1. Az **IdP-összekötő konfigurálásához a Módszer kiválasztása csoportban**adja meg a metaadatokat, kattintson a Fájl kiválasztása elemre, és töltse fel az Azure AD-ből korábban letöltött metaadat-XML-fájlt. Adja meg az SAML IDP-összekötő egyedi **nevét.** Válassza ki a korábban feltöltött **metaadat-aláíró tanúsítványt.** Kattintson **& Tovább mentés gombra.**

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure07.png)  

1. A **Készlet kiválasztása csoportban**adja meg az **Új létrehozása lehetőséget** (másik lehetőségként jelöljön ki egy már létező készletet). Hagyja, hogy más érték legyen az alapértelmezett érték. A Készletkiszolgálók csoportban írja be az IP-címet az **IP-cím/csomópont név mezőbe.** Adja meg a **portot**. Kattintson **& Tovább mentés gombra.**
 
    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure08.png)

1. Az Egyszeri bejelentkezés beállításai képernyőn válassza az **Egyszeri bejelentkezés engedélyezése**lehetőséget. A **Kijelölt egyszeri bejelentkezés típusa csoportban** válassza a **Kerberos**lehetőséget. Cserélje **le a session.saml.last.Identity-t** **a session.saml.last.attr.name.Identity** kifejezésre a **Felhasználónév forrás csoportban** (ez a változó az Azure AD-ben a jogcímleképezés használatával beállított). Válassza **a Speciális beállítás megjelenítése lehetőséget.** A **Kerberos Tartomány** mezőbe írja be a Tartománynevet. A **Fióknév/fiókjelszó csoportban** adja meg az APM delegálási fiókját és jelszavát. Adja meg a tartományvezérlő IP-címét a **KDC** mezőben. Kattintson **& Tovább mentés gombra.**

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure09.png)   

1. Ennek az útmutatónak az alkalmazásában kihagyjuk a végpont-ellenőrzéseket.  A részleteket az F5 dokumentációjában találja.  A képernyőn válassza **a Mentés & Tovább**gombot .

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure10.png) 

1. Fogadja el az alapértelmezett beállításokat, és kattintson **a Mentés & Tovább gombra**. Az SAML-munkamenet-kezelési beállításokkal kapcsolatos részletekért tekintse meg az F5 dokumentációját.


    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure11.png) 
 
1. Tekintse át az összefoglaló képernyőt, és válassza **a Telepítés lehetőséget** a BIG-IP konfigurálásához.
 
    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure12.png)

1. Az alkalmazás konfigurálása után kattintson a **Befejezés gombra.**

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure13.png)

## <a name="advanced-configuration"></a>Speciális konfiguráció

>[!NOTE]
>A referencia [ide](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html) kattintva

### <a name="configuring-an-active-directory-aaa-server"></a>Active Directory AAA-kiszolgáló konfigurálása

Az Access Policy Manager (APM) active directory AAA-kiszolgálójának konfigurálásához adja meg az APM számára a felhasználók hitelesítéséhez használt tartományvezérlőket és hitelesítő adatokat.

1.  A Fő lapon kattintson a **Hozzáférési házirend > az Active Directory > AAA-kiszolgálók**elemre. Megnyílik az Active Directory-kiszolgálók listaképernyője.

2.  Kattintson **a Létrehozás gombra.** Megnyílik az Új kiszolgáló tulajdonságai képernyő.

3.  A **Név** mezőbe írja be a hitelesítési kiszolgáló egyedi nevét.

4.  A **Tartománynév** mezőbe írja be a Windows tartomány nevét.

5.  A **Kiszolgálói kapcsolat** beállításhoz válasszon az alábbi lehetőségek közül:

    * Válassza **a Készlet használata lehetőséget** az AAA-kiszolgáló magas rendelkezésre állásának beállításához.

    * Válassza a **Közvetlen** lehetőséget az AAA-kiszolgáló önálló funkciókhoz való beállításához.

6.  Ha a **Közvetlen**lehetőséget választotta, írjon be egy nevet a **Tartományvezérlő** mezőbe.

7.  Ha a **Készlet**használata lehetőséget választotta, konfigurálja a készletet:

    * Írjon be egy nevet a **Tartományvezérlőkészlet neve** mezőbe.

    * Adja meg a **készlet tartományvezérlőit** úgy, hogy beírja mindegyik IP-címét és állomásnevét, majd a **Hozzáadás** gombra kattint.

    * Az AAA-kiszolgáló állapotának figyeléséhez lehetőség van egy állapotfigyelő kiválasztására: ebben az esetben csak a **gateway_icmp** monitor megfelelő; kiválaszthatja a **Kiszolgálókészlet-figyelő** listából.

8.  A **Rendszergazdai név** mezőbe írja be az Active Directory rendszergazdai engedélyekkel rendelkező rendszergazda kis- és nagybetűs nevét. Az APM az AD-lekérdezés **Rendszergazdai név** és **Rendszergazdai jelszó** mezőiben szereplő adatokat használja. Ha az Active Directory névtelen lekérdezésekhez van konfigurálva, nem kell rendszergazdai nevet megadnia. Ellenkező esetben az APM-nek megfelelő jogosultsággal rendelkező fiókra van szüksége ahhoz, hogy egy Active Directory-kiszolgálóhoz kötődjen, leolvassa a felhasználói csoport adatait, és lekéri az Active Directory jelszóházirendjeit a jelszóval kapcsolatos funkciók támogatásához. (Az APM-nek be kell olvasnia a jelszóházirendeket, például ha az AD-lekérdezésműveletben a Felhasználó nak a jelszó módosítása előtt a jelszó megváltoztatására lehetőséget választja.) Ha ebben a konfigurációban nem adja meg a rendszergazdai fiók adatait, az APM a felhasználói fiók segítségével olvassa le az adatokat. Ez akkor működik, ha a felhasználói fiók megfelelő jogosultsággal rendelkezik.

9.  A **Rendszergazdai jelszó** mezőbe írja be a Tartománynévhez társított rendszergazdai jelszót.

10. A **Rendszergazdai jelszó ellenőrzése** mezőbe írja be újra a **Tartománynév** beállításhoz társított rendszergazdai jelszót.

11. A **Csoportgyorsítótár élettartama** mezőbe írja be a napok számát. Az alapértelmezett élettartam 30 nap.

12. A **Jelszóbiztonsági objektum gyorsítótárának élettartama** mezőbe írja be a napok számát. Az alapértelmezett élettartam 30 nap.

13. A **Kerberos előhitelesítési titkosítási típus** listájában válasszon ki egy titkosítási típust. Az alapértelmezett érték: **Nincs**. Ha titkosítási típust ad meg, a BIG-IP rendszer kerberos-előhitelesítési adatokat tartalmaz az első hitelesítési szolgáltatáskérelem (AS-REQ) csomagban.

14. Az **Időelés** mezőbe írja be az AAA-kiszolgáló időmegt/másodpercben iódésidő-időközét.In the Timeout field, type a timeout interval (in seconds) for the AAA server. (Ez a beállítás nem kötelező.)

15. Kattintson a **Kész gombra.** Az új kiszolgáló megjelenik a listán. Ezzel hozzáadja az új Active Directory-kiszolgálót az Active Directory-kiszolgálók listájához.

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure17.png)

### <a name="saml-configuration"></a>SAML konfiguráció

1. A metaadat-tanúsítványt importálnia kell az F5-be, amelyet a beállítási folyamat későbbi részében fog használni. Nyissa meg **a Rendszer> tanúsítványkezelés > forgalmi tanúsítványkezelés > SSL-tanúsítványlista .** Válassza az **Importálás lehetőséget** a jobb oldali sarokban.

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure18.png)

2. Az SAML IDP beállításához keresse meg az **Access > Összevonási > SAML: Külső Idp-összekötők > szolgáltató**ját, majd kattintson a > létrehozása **metaadatokból parancsra.**

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure19.png)

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure20.png)

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure21.png)

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure22.png)

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure23.png)

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure24.png)

1. Az SAML SP beállításához keresse meg az **Access > az Összevonás > az SAML-szolgáltató > helyi SP-szolgáltatások** elemre, és kattintson a **Létrehozás gombra.** Hajtsa végre az alábbi információkat, és kattintson az **OK gombra.**

    * Típus neve: KerbApp200SAML
    * Entitásazonosító*:https://kerbapp200.superdemo.live
    * SP névbeállításai
    * Séma: https
    * Házigazda: kerbapp200.superdemo.live
    * Leírás: kerbapp200.superdemo.live

     ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure25.png)

     b. Válassza ki az SP konfigurációt, a KerbApp200SAML elemet, és kattintson **a Kötés/UnBind IdP-összekötők**elemre.

     ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure26.png)

     ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure27.png)

     c. Kattintson az **Új sor hozzáadása** gombra, és válassza ki az előző lépésben létrehozott külső **IdP-összekötőt,** kattintson a **Frissítés**gombra, majd az **OK**gombra.

     ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure28.png)

1. A Kerberos egyszeri bejelentkezés konfigurálásához keresse meg **az Access > Single Sign-on > Kerberos (Egyszeres bejelentkezés) >** a Kerberos-t, és kattintson a **Kész**gombra.

    >[!Note]
    > Létre kell hoznia és meg kell adnia a Kerberos delegálási fiókot. KCD-szakasz hivatkozása (a függelék hivatkozása változó referenciákra)

    * **Felhasználónév forrása**: session.saml.last.attr.name.http:\//schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname

    * **Felhasználó tartomány forrása**: session.logon.last.domain

        ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure29.png)

1. Az Access-profil konfigurálásához keresse meg **az Access >-profil/házirendek > Access Profile (munkamenet-házirendenként)** elemre, kattintson a **Létrehozás**gombra, töltse ki az alábbi információkat, majd kattintson a **Kész**gombra.

    * Név: KerbApp200
    * Profil típusa: Minden
    * Profil hatóköre: Profil
    * Nyelvek: Angol

        ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure30.png)

1. Kattintson a KerbApp200 névre, töltse ki a következő információkat, és kattintson a **Frissítés gombra.**

    * Domain Cookie: superdemo.live
    * SSO-konfiguráció: KerAppSSO_sso

        ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure31.png)

1. Kattintson **a Hozzáférési szabályzat,** majd a "KerbApp200" profil **hozzáférési szabályzatának szerkesztése** elemre.

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure32.png)

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure33.png)

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure34.png)

    * **session.logon.last.usernameUPN expr {[mcget {session.saml.last.identity}]}**

    * **session.ad.lastactualdomain TEXT superdemo.live**

        ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure35.png)

    * **(userPrincipalName=%{session.logon.last.usernameUPN})**

        ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure36.png)

        ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure37.png)

    * **session.logon.last.username expr { "[mcget {session.ad.last.attr.sAMAccountName}]" }**

        ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure38.png)

    * **mcget {session.logon.last.username}**
    * **mcget {session.logon.last.password**

1. Új csomópont hozzáadásához keresse meg a Helyi forgalom > csomópontok **> csomópontlista elemre, kattintson a Létrehozás gombra,** töltse ki az alábbi adatokat, majd kattintson a **Kész**gombra.

    * Név: KerbApp200
    * Leírás: KerbApp200
    * Cím: 192.168.20.200

        ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure39.png)

1. Új készlet létrehozásához keresse meg **a Helyi forgalom > a készletek > készletlista elemre, kattintson a Létrehozás gombra,** töltse ki az alábbi információkat, majd kattintson a **Kész**gombra.

    * Név: KerbApp200-Pool
    * Leírás: KerbApp200-Pool
    * Egészségügyi monitorok: http
    * Cím: 192.168.20.200
    * Szolgálati port: 81

        ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure40.png)

1. A virtuális kiszolgáló létrehozásához keresse meg **a virtuális kiszolgálók helyi > a virtuális kiszolgálók listáját > > +**, töltse ki az alábbi információkat, majd kattintson a Kész **gombra.**

    * Név: KerbApp200
    * Cél címe/maszk: Állomás 192.168.30.200
    * Szolgáltatás portja: Port 443 HTTPS
    * Hozzáférési profil: KerbApp200
    * Az előző lépésben létrehozott hozzáférési profil megadása

        ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure41.png)

        ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure42.png)

### <a name="setting-up-kerberos-delegation"></a>A Kerberos-delegálás beállítása 

>[!NOTE]
>A referencia [ide](https://www.f5.com/pdf/deployment-guides/kerberos-constrained-delegation-dg.pdf) kattintva

*  **1. lépés:** Delegálási fiók létrehozása

    **Példa:**
    * Domain név: **superdemo.live**

    * Sam fiók neve: **big-ipuser**

    * New-ADUser -Name "APM delegálási host/big-ipuser.superdemo.live@superdemo.live fiók" -UserPrincipalName -SamAccountName "big-ipuser" -PasswordNeverExpires $true -Enabled $true -AccountPassword (Read-Host -AsSecureString "Password!1234")

* **2. lépés:** Az SPN beállítása (az APM delegálási fiókjában)

    **Példa:**
    * setspn -A **host/big-ipuser.superdemo.live** big-ipuser

* **3. lépés:** Az SPN-delegálás (az App Service-fiókhoz) állítsa be az F5 delegálási fiók megfelelő delegálását.
    Az alábbi példában az APM delegálási fiók konfigurálása folyamatban van a KCD-hez az FRP-App1.superdemo-hoz. élő alkalmazás.

    ![F5 (Kerberos) konfiguráció](./media/kerbf5-tutorial/configure43.png)

* Adja meg a fenti referenciadokumentumban [az e](https://techdocs.f5.com/kb/en-us/products/big-ip_apm/manuals/product/apm-authentication-single-sign-on-11-5-0/2.html)című dokumentumban említett részleteket.

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

- [Az F5 egyszeri bejelentkezésének konfigurálása fejlécalapú alkalmazáshoz](headerf5-tutorial.md)

- [Az F5 egyszeri bejelentkezéskonfigurálása speciális Kerberos-alkalmazáshoz](advance-kerbf5-tutorial.md)

