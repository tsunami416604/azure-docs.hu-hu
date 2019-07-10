---
title: Adja hozzá a helyszíni alkalmazás – az Azure Active Directory alkalmazásproxy |} A Microsoft Docs
description: Az Azure Active Directory (Azure AD) egy alkalmazásproxy-szolgáltatás, amely lehetővé teszi a felhasználók hozzáférhessenek a helyszíni alkalmazásokhoz bejelentkezik az Azure AD-fiókja van. Ez az oktatóanyag bemutatja, hogyan készítse elő a környezetét használatra az alkalmazásproxy használatával. Ezt követően használ az Azure Portalon az Azure AD-bérlő helyszíni alkalmazás hozzáadása.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: ecff60d1a1f808c4021476d136fe014175451672
ms.sourcegitcommit: 0ebc62257be0ab52f524235f8d8ef3353fdaf89e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67723962"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Oktatóanyag: A távoli hozzáféréshez alkalmazásproxyn keresztül a helyszíni alkalmazás hozzáadása az Azure Active Directoryban

Az Azure Active Directory (Azure AD) egy alkalmazásproxy-szolgáltatás, amely lehetővé teszi a felhasználók hozzáférhessenek a helyszíni alkalmazásokhoz bejelentkezik az Azure AD-fiókja van. Ebben az oktatóanyagban előkészíti a környezet használatra az alkalmazásproxy használatával. Ha a környezet készen áll, akkor az Azure portal segítségével az Azure AD-bérlő helyszíni alkalmazás hozzáadása.

Ez az oktatóanyag:

> [!div class="checklist"]
> * Megnyílik a portokat a kimenő forgalom számára, és lehetővé teszi a hozzáférést az adott URL-címek
> * A telepítő az összekötőt a Windows-kiszolgálón, és regisztrálja azt a proxyval
> * Ellenőrzi az összekötő telepítve van és megfelelően regisztrálva
> * Hozzáad egy helyszíni alkalmazást az Azure AD-bérlővel
> * Ellenőrzi, hogy egy tesztfelhasználót bejelentkezhetnek az alkalmazás egy Azure AD-fiókkal

## <a name="before-you-begin"></a>Előkészületek

Az Azure ad-hez a helyszíni alkalmazás hozzáadásához az alábbiak szükségesek:

* A [a Microsoft Azure AD prémium vagy alapszintű előfizetés](https://azure.microsoft.com/pricing/details/active-directory)
* Alkalmazás-rendszergazdai fiókkal
* Felhasználói identitásokat a helyszíni címtárból szinkronizált vagy közvetlenül az Azure AD-bérlőt belül létrehozni. Identitásszinkronizálási lehetővé teszi, hogy az Azure ad-ben előre hitelesíti a felhasználókat, mielőtt őket hozzáférés engedélyezése az alkalmazásproxyval közzétett alkalmazások és az egyszeri bejelentkezés (SSO) végrehajtásához szükséges felhasználói információk.

### <a name="windows-server"></a>Windows server

A Windows server, a Windows Server 2012 R2 rendszerű kell Application proxyt használ, vagy később. Az alkalmazásproxy-összekötő a kiszolgálón telepít. Az összekötő kiszolgáló csatlakoznia kell az Azure-ban az alkalmazásproxy-szolgáltatásokat, és a helyszíni alkalmazások közzétételének megtervezése.

Magas rendelkezésre álláshoz az éles környezetben javasoljuk, hogy egynél több Windows server. Ebben az oktatóanyagban egy Windows server is használhatók.

#### <a name="recommendations-for-the-connector-server"></a>Az összekötő kiszolgáló javaslatok

1. Fizikailag keresse meg az összekötő kiszolgáló közel az alkalmazáskiszolgálók között az összekötő és az alkalmazás teljesítményének optimalizálásához. További információkért lásd: [hálózati topológiai szempontok a](application-proxy-network-topology.md).
1. Az összekötő és a webes alkalmazások kiszolgáló kell az Active Directory-tartományhoz tartozik, vagy span megbízó tartományokban. A kiszolgálókat kellene ugyanabban a tartományban vagy megbízható tartományok esetében az integrált Windows-hitelesítés (IWA) és a Kerberos által korlátozott delegálás (KCD) egyszeri bejelentkezéses (SSO) használatára vonatkozó követelmény. Ha az összekötő kiszolgáló és a webalkalmazás-kiszolgálón is eltérő Active Directory-tartományok, delegálási erőforrás-alapú egyszeri bejelentkezést használni szeretne. További információkért lásd: [KCD az egyszeri bejelentkezés alkalmazásproxyval való](application-proxy-configure-single-sign-on-with-kcd.md).

#### <a name="tls-requirements"></a>TLS-követelményeknek

A TLS 1.2 engedélyezve van az alkalmazásproxy-összekötő telepítése előtt rendelkeznie kell a Windows összekötő kiszolgáló.

A TLS 1.2 engedélyezése:

1. Állítsa be a következő beállításkulcsokat:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Indítsa újra a kiszolgálót.

> [!IMPORTANT]
> Ügyfeleink kategóriaelső a titkosítás megadásához igyekszünk frissítések az alkalmazásproxy-szolgáltatás csak a TLS 1.2 protokoll való hozzáférés korlátozására. A változások fokozatosan bevezetjük az ügyfelek számára, akik csak a TLS 1.2 protokoll használata, és nem jelenik meg semmilyen hatással a változás ügyfél készültségi alapján. 2019. augusztus 31-ig befejezi a TLS 1.0 és 1.1 elavult, és ügyfelek fel a változásra előzetes értesítést fog kapni. Előkészítése e változás ügyeljen arra, hogy minden ügyfél-kiszolgáló és a böngésző-kiszolgáló kombinációját frissítve lett, hogy a TLS 1.2 használatával alkalmazásproxy-szolgáltatás kapcsolat fenntartását. Ezek közé tartozik az ügyfelek használják a felhasználók az alkalmazásproxyn keresztül közzétett alkalmazások eléréséhez. Tekintse meg a előkészítése [TLS 1.2-es Office 365-ben](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365) hasznos hivatkozások és erőforrások.

## <a name="prepare-your-on-premises-environment"></a>A helyszíni környezet előkészítése

Első lépésként az Azure-beli adatközpontok készítse elő a környezetét az Azure AD Application Proxy kommunikáció engedélyezése. Ha az elérési út van egy tűzfal, ellenőrizze meg nyitva. Egy megnyitott tűzfal engedélyezi az összekötőt, hogy a HTTPS (TCP) kéréseket az alkalmazásproxynak.

### <a name="open-ports"></a>Portok megnyitása

Nyissa meg a következő portokat **kimenő** forgalmat.

   | Portszám | Hogyan használja fel azokat |
   | --- | --- |
   | 80 | Letölti a tanúsítvány-visszavonási listákat (CRL) az SSL-tanúsítvány érvényesítése közben |
   | 443 | Minden kimenő kommunikációnak az alkalmazásproxy-szolgáltatással |

A tűzfal szabályozza az adatforgalmat a felhasználók helye szerint, 80-as és 443-as, a forgalmat is nyissa meg a hálózati szolgáltatásként futó Windows-szolgáltatások.

Előfordulhat, hogy a telepítve van, ha már használja az Application Proxy connector egy régebbi verzióját. Ez az oktatóanyag az összekötő legújabb verziójának telepítéséhez. Verziók 1.5.132.0 korábban is szükséges a következő portok megnyitásához: 5671, 8080, 9090-9091, 9350, 9352, 10100–10120.

### <a name="allow-access-to-urls"></a>URL-címek való hozzáférés engedélyezése

A következő URL-hozzáférés engedélyezése:

| URL | Hogyan használja fel azokat |
| --- | --- |
| \*.msappproxy.net<br>\*.servicebus.windows.net | Az összekötő és az alkalmazásproxy-felhőszolgáltatás közötti kommunikáció |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Az Azure az alábbi URL-címek segítségével tanúsítványok. |
| login.windows.net<br>login.microsoftonline.com<br>secure.aadcdn.microsoftonline-p.com  | Az összekötő URL-használ a regisztrációs folyamat során. |

Engedélyezheti a kapcsolatok \*. msappproxy.net és \*. servicebus.windows.net, ha a tűzfal vagy proxy engedélyezi, hogy a DNS konfigurálása listák engedélyezése. Ha nem, engedélyeznie kell a hozzáférést a [Azure DataCenter IP-címtartományok](https://www.microsoft.com/download/details.aspx?id=41653). Az IP-címtartományok minden héten frissülnek.

## <a name="install-and-register-a-connector"></a>Telepítés és a egy összekötő regisztrálása

Az alkalmazásproxy használatához telepítse a minden Windows server és az alkalmazásproxy-szolgáltatás használata. Az összekötő olyan ügynök, amely felügyeli a kimenő kapcsolatot a helyszíni alkalmazás kiszolgálókról-proxyra történő az Azure ad-ben. Egy összekötő telepíthető is rendelkező más hitelesítési ügynökkel, például az Azure AD Connect-kiszolgálók.

Az összekötő telepítéséhez:

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com/) annak a könyvtárnak az alkalmazásproxy használó alkalmazást rendszergazdaként. Ha például a bérlő tartománya a contoso.com, a rendszergazdának kell lennie admin@contoso.com vagy más felügyeleti alias abban a tartományban.
1. Kattintson a felhasználónevére a jobb felső sarokban. Ellenőrizze, hogy egy alkalmazásproxy használó könyvtárba van bejelentkezve. Ha módosítsa a könyvtárakat van szüksége, válassza ki a **címtár váltása** és a egy könyvtárat, amely használja az Application Proxy kiválasztása.
1. A bal oldali navigációs panelen válassza a **Azure Active Directory**.
1. A **kezelés**válassza **alkalmazásproxy**.
1. Válassza ki **összekötőszolgáltatás letöltése**.

    ![Töltse le az összekötő szolgáltatást, tekintse meg a szolgáltatási feltételek](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Olvassa el a szolgáltatási feltételeket. Amikor elkészült, válassza ki a **feltételek elfogadása és letöltés**.
1. Az ablak alján válassza **futtatása** az összekötő telepítéséhez. Megnyílik a telepítési varázsló.
1. Kövesse a varázsló utasításait, telepítse a szolgáltatást. Amikor a rendszer kéri, hogy az összekötő regisztrálására az Azure AD-bérlő a Proxy, adja meg az alkalmazás rendszergazdai hitelesítő adatait.
    - Az Internet Explorer (IE), ha **Internet Explorer fokozott biztonsági beállításai** értékre van állítva **a**, nem láthatók a regisztrációs képernyő. Érhet el, kövesse a hibaüzenetben. Győződjön meg arról, hogy **az Internet Explorer fokozott biztonsági beállításai** értékre van állítva **ki**.

### <a name="general-remarks"></a>Általános megjegyzéseket

Ha korábban telepítette egy összekötőt, akkor telepítse újra a legújabb verzió beszerzéséhez. Korábban kiadott verziók és milyen módosítások azok kapcsolatos információkat tartalmazza, lásd: [Application Proxy: Verziókiadások előzményei](application-proxy-release-version-history.md).

Ha egynél több Windows kiszolgálót a helyszíni alkalmazások számára, szüksége telepítéséhez, és mindegyik kiszolgálón az összekötő regisztrálására. Az összekötők összekötő csoportokba rendezhetők. További információkért lásd: [összekötőcsoportok](application-proxy-connector-groups.md).

Ha a szervezete proxykiszolgálóval csatlakozik az internethez, kell őket az Application Proxy konfigurálása.  További információkért lásd: [együttműködnek a meglévő helyszíni proxykiszolgálók](application-proxy-configure-connectors-with-proxy-servers.md). 

Összekötők, a kapacitástervezés és hogyan naprakész információk kapcsolatos információkért lásd: [megismerheti az Azure AD-alkalmazásproxy összekötőit](application-proxy-connectors.md).

## <a name="verify-the-connector-installed-and-registered-correctly"></a>Ellenőrizze az összekötő telepítve van és megfelelően regisztrálva

Az Azure portal vagy a Windows server használatával győződjön meg arról, hogy egy új összekötő megfelelően telepítve.

### <a name="verify-the-installation-through-azure-portal"></a>Az Azure Portalon keresztül telepítésének ellenőrzése

Az összekötő telepítve és regisztrálva megfelelően ellenőrzése:

1. Jelentkezzen be a bérlő címtárát a a [az Azure portal](https://portal.azure.com).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory**, majd válassza ki **alkalmazásproxy** alatt a **kezelés** szakaszban. Az összes összekötőcsoportok és összekötők ezen az oldalon jelennek meg.
1. Az összekötő ellenőrzéséhez a részletek megtekintéséhez. Az összekötők alapértelmezés szerint ki kell terjeszteni. Ha meg szeretné tekinteni az összekötő nincs kibontva, bontsa ki az összekötő részleteinek megtekintéséhez. Egy aktív zöld címke azt jelzi, hogy az összekötő képes-e csatlakozni a szolgáltatáshoz. Azonban annak ellenére, hogy a címke zöld, hálózati probléma továbbra is letilthatják az összekötő-üzenetek fogadása.

    ![Az Azure AD alkalmazásproxy-összekötők](./media/application-proxy-connectors/app-proxy-connectors.png)

További összekötő telepítésével kapcsolatos útmutatásért lásd: [az alkalmazásproxy-összekötő telepítése probléma](application-proxy-connector-installation-problem.md).

### <a name="verify-the-installation-through-your-windows-server"></a>Ellenőrizze a telepítési folyamatot a Windows server

Az összekötő telepítve és regisztrálva megfelelően ellenőrzése:

1. A Windows-szolgáltatások kezelője kattintva megnyithatja a **Windows** kulcsot, és írja be *services.msc*.
1. Ellenőrizze, hogy van-e a következő két szolgáltatás állapota **futó**.
   - **A Microsoft AAD alkalmazásproxy-összekötő** kapcsolatokat teszi lehetővé.
   - **A Microsoft AAD Alkalmazásproxyösszekötő** egy automatikus frissítési szolgáltatás. A frissítési ellenőrzi, hogy az összekötő újabb verziója, és szükség szerint frissíti.

     ![Az alkalmazásproxy összekötőjének szolgáltatásai – képernyőfelvétel](./media/application-proxy-enable/app_proxy_services.png)

1. Ha a szolgáltatás állapota nem **futtató**, kattintson a jobb gombbal az egyes szolgáltatásokhoz válasszon, majd válassza **Start**.

## <a name="add-an-on-premises-app-to-azure-ad"></a>Az Azure ad-hez a helyszíni alkalmazás hozzáadása

Most, hogy a környezet előkészítése és egy összekötőt, készen áll a helyszíni alkalmazások az Azure AD hozzá.  

1. Jelentkezzen be rendszergazdaként a a [az Azure portal](https://portal.azure.com/).
1. A bal oldali navigációs panelen válassza ki a **Azure Active Directory**.
1. Válassza ki **vállalati alkalmazások**, majd válassza ki **új alkalmazás**.
1. Válassza ki **helyszíni alkalmazás**.  
1. Az a **saját helyszíni alkalmazás hozzáadása** területén adja meg a következő információkat az alkalmazásról:

    | Mező | Leírás |
    | :---- | :---------- |
    | **Name** | Az alkalmazás a hozzáférési panelen és az Azure Portalon megjelenő nevére. |
    | **Belső URL-cím** | URL-cím a magánhálózaton belülről történő alkalmazás eléréséhez. Megadhat egyedi elérési utat a háttérkiszolgálón a közzétételhez, míg a kiszolgáló további része nem lesz közzétéve. Ily módon a közzététele ugyanarra a kiszolgálóra, mint a különböző alkalmazások különböző helyeken, és mindegyikhez adjon a saját nevet és hozzáférési szabályokat.<br><br>Ha közzétesz egy útvonalat, győződjön meg róla, hogy az tartalmaz minden szükséges lemezképet, szkriptet és stíluslapot az alkalmazásához. Például, ha az alkalmazás https:\//yourapp/alkalmazás és a használt rendszerképek https helyen található:\//yourapp/media, majd közzé kell tenni https:\//yourapp/, mert az elérési út. A belső URL-cím nem kell lennie a kezdőlapját, megjelennek a felhasználók számára. További információkért lásd: [beállítása egy egyéni kezdőlapja közzétett alkalmazások](application-proxy-configure-custom-home-page.md). |
    | **Külső URL-cím** | A felhasználók számára hozzáférést a hálózaton kívülről az alkalmazás címe. Ha nem szeretné az alapértelmezett alkalmazásproxy tartományát szeretné használni, olvassa el [egyéni tartományok az Azure AD-alkalmazásproxy](application-proxy-configure-custom-domain.md).|
    | **Előhitelesítés** | Az alkalmazásproxy hogyan az alkalmazás hozzáférés engedélyezése előtt ellenőrzi a felhasználót.<br><br>**Az Azure Active Directory** – az alkalmazásproxy átirányítja a felhasználókat az Azure ad-vel, amely hitelesíti a címtár és az alkalmazás vonatkozó való bejelentkezési. Azt javasoljuk, hogy ez a beállítás alapértelmezés szerint tartja, hogy az Azure AD biztonsági funkciókkal, például a feltételes hozzáférés és a multi-factor Authentication szolgáltatás előnyeit élvezheti. **Az Azure Active Directory** szükség az alkalmazások és a Microsoft Cloud Application Security figyelésére.<br><br>**Csatlakoztatott** -felhasználóknak nem kell elérni az alkalmazást az Azure AD hitelesítése. Továbbra is beállíthatja a háttérkiszolgálón hitelesítési követelmények. |
    | **Összekötőcsoport** | Összekötők dolgozza fel az alkalmazáshoz való távoli hozzáférést és összekötőcsoportok segít rendszerezni a összekötők és a régiót, hálózati vagy célú alkalmazások. Ha nincs még létrehozva összekötő csoportnak sem, az alkalmazás hozzá van rendelve **alapértelmezett**.<br><br>Ha az alkalmazás való csatlakozáshoz használja a websockets protokoll, a csoportban lévő összes összekötőt kell 1.5.612.0 verzió vagy újabb.|

1. Szükség esetén konfiguráljon **további beállítás**. A legtöbb alkalmazás esetén ezek a beállítások érdemes megtartani az alapértelmezett állapotra. 

    | Mező | Leírás |
    | :---- | :---------- |
    | **Háttéralkalmazás túllépte az időkorlátot** | Ez az érték **hosszú** csak akkor, ha az alkalmazás lassú hitelesítéshez és csatlakozáshoz. |
    | **Csak HTTP cookie-k használata** | Ez az érték **Igen** kell Application Proxy cookie-kat a HTTPOnly jelző bevonni a HTTP-válaszfejléc. Ha a távoli asztali szolgáltatásokat, állítsa be ezt az értéket **nem**.|
    | **Biztonságos cookie-k használata**| Ez az érték **Igen** cookie-k egy biztonságos csatornán, például egy titkosított HTTPS-kérést küldött.
    | **Állandó cookie-k használata**| Tartsa ezt az értéket állítsa **nem**. Ez a beállítás csak az alkalmazásokat, amelyek nem lehet megosztani a folyamatok közötti cookie-kat használja. Cookie-k beállításaival kapcsolatos további információkért lásd: [cookie-k beállításairól a helyszíni alkalmazások az Azure Active Directory eléréséhez](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings).
    | **A fejlécek URL-címek lefordítása** | Tartsa ezt az értéket **Igen** , kivéve, ha az alkalmazás által igényelt az eredeti állomásfejlécet a hitelesítési kérelmet. |
    | **A kérelem törzsében URL-címek lefordítása** | Tartsa ezt az értéket **nem** , ha rendelkezik szoftveresen kötött HTML-hivatkozások más helyszíni alkalmazásokhoz, és ne használja az egyéni tartományok. További információkért lásd: [hivatkozásra a proxyval fordítási](application-proxy-configure-hard-coded-link-translation.md).<br><br>Ez az érték **Igen** Ha azt tervezi, hogy az alkalmazás a Microsoft Cloud App Security (MCAS) figyelésére. További információkért lásd: [valós idejű hozzáférés az alkalmazásfigyelés konfigurálása a Microsoft Cloud App Security és az Azure Active Directory](application-proxy-integrate-with-microsoft-cloud-application-security.md). |

1. Válassza a **Hozzáadás** lehetőséget.

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Készen áll a teszt megfelelően kell hozzáadnia az alkalmazást. A következő lépésekben fog egy felhasználói fiókot hozzáadni az alkalmazáshoz, és próbáljon meg bejelentkezni.

### <a name="add-a-user-for-testing"></a>Felhasználó hozzáadása teszteléshez

Mielőtt egy felhasználó hozzáadása az alkalmazáshoz, győződjön meg arról, a felhasználói fiók már rendelkezik engedélyekkel a vállalati hálózaton belülről történő alkalmazás eléréséhez.

Tesztfelhasználó hozzáadása:

1. Válassza ki **vállalati alkalmazások**, és válassza ki a vizsgálni kívánt alkalmazást.
1. Válassza ki **bevezetés**, majd válassza ki **felhasználó hozzárendelése teszteléshez**.
1. A **felhasználók és csoportok**válassza **felhasználó hozzáadása**.
1. A **-hozzárendelés hozzáadása**válassza **felhasználók és csoportok**. A **felhasználók és csoportok** szakasz jelenik meg.
1. Válassza ki a hozzáadni kívánt fiókot.
1. Válasszon **kiválasztása**, majd válassza ki **hozzárendelése**.

### <a name="test-the-sign-on"></a>A bejelentkezés vizsgálata

A bejelentkezés az alkalmazás teszteléséhez:

1. A böngészőben lépjen a közzétételi lépés során konfigurált külső URL-CÍMÉT. A kezdőképernyőn kell megjelennie.
1. Jelentkezzen be az előző szakaszban létrehozott felhasználóként.

Című témakörben [problémák elhárítása az Application Proxy és hibaüzenetek](application-proxy-troubleshoot.md).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban előkészített a helyszíni környezetet a proxyval, és ezután telepítve és regisztrálva az Application Proxy connector. Következő lépésként hozzáadott egy alkalmazást az Azure AD-bérlővel. Ellenőrizte, hogy a felhasználók bejelentkezhetnek az alkalmazás egy Azure AD-fiók használatával.

A következőket hajtotta végre:
> [!div class="checklist"]
> * Megnyitott portokat a kimenő forgalom és engedélyezett hozzáférést adott URL-címek
> * Az összekötő a Windows-kiszolgálóra telepíthető, és regisztrálja azt a proxyval
> * Az összekötő telepítve és regisztrálva megfelelően ellenőrizve
> * Az Azure AD-bérlő helyszíni alkalmazás hozzáadása
> * Ellenőrizte a tesztfelhasználó bejelentkezhetnek az alkalmazás egy Azure AD-fiókkal

Készen áll az alkalmazás egyszeri bejelentkezés konfigurálása. Egyszeri bejelentkezés módjának kiválasztása és egyszeri bejelentkezést oktatóanyagok, használja a következő hivatkozást.

> [!div class="nextstepaction"]
> [Egyszeri bejelentkezés konfigurálása](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
