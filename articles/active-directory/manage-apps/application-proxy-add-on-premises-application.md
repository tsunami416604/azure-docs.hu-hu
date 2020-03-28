---
title: Oktatóanyag – Helyszíni alkalmazás hozzáadása – Alkalmazásproxy az Azure AD-ben
description: Az Azure Active Directory (Azure AD) rendelkezik egy alkalmazásproxy-szolgáltatással, amely lehetővé teszi a felhasználók számára a helyszíni alkalmazások elérését az Azure AD-fiókjukkal való bejelentkezéssel. Ez az oktatóanyag bemutatja, hogyan készítheti elő a környezetet az alkalmazásproxyval való használatra. Ezután az Azure Portal on-premises alkalmazás hozzáadása az Azure AD-bérlő.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: tutorial
ms.date: 10/24/2019
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 73aa01ea08c8bab1395516c31bb46dbfd88045db
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "79481415"
---
# <a name="tutorial-add-an-on-premises-application-for-remote-access-through-application-proxy-in-azure-active-directory"></a>Oktatóanyag: Az Azure Active Directory alkalmazásproxyn keresztüli távoli eléréshez helyszíni alkalmazás hozzáadása

Az Azure Active Directory (Azure AD) rendelkezik egy alkalmazásproxy-szolgáltatással, amely lehetővé teszi a felhasználók számára a helyszíni alkalmazások elérését az Azure AD-fiókjukkal való bejelentkezéssel. Ez az oktatóanyag előkészíti a környezetet az alkalmazásproxyval való használatra. Miután a környezet készen áll, az Azure Portal használatával egy helyszíni alkalmazást adhat hozzá az Azure AD-bérlőhöz.

Ez az oktatóanyag:

> [!div class="checklist"]
> * Megnyitja a bejövő forgalmat, és hozzáférést biztosít az adott URL-ekhez
> * Telepíti az összekötőt a Windows kiszolgálóra, és regisztrálja az alkalmazásproxyval
> * Megfelelően ellenőrzi a telepített és regisztrált csatlakozót
> * Helyszíni alkalmazást ad hozzá az Azure AD-bérlőhöz
> * Ellenőrzi, hogy egy tesztfelhasználó bejelentkezhet-e az alkalmazásba egy Azure AD-fiók használatával

## <a name="before-you-begin"></a>Előkészületek

Ha egy helyszíni alkalmazást szeretne hozzáadni az Azure AD-hez, a következőkre van szüksége:

* [Microsoft Azure AD prémium előfizetés](https://azure.microsoft.com/pricing/details/active-directory)
* Alkalmazás-rendszergazdai fiók
* A felhasználói identitásokat egy helyszíni címtárból kell szinkronizálni, vagy közvetlenül az Azure AD-bérlőkön belül kell létrehozni. Az identitás-szinkronizálás lehetővé teszi az Azure AD számára, hogy előzetesen hitelesítse a felhasználókat, mielőtt hozzáférést biztosítana számukra az App Proxy által közzétett alkalmazásokhoz, és hogy rendelkezzenek az egyszeri bejelentkezéshez szükséges felhasználói azonosító adatokkal.

### <a name="windows-server"></a>Windows Server

Az alkalmazásproxy használatához Windows Server 2012 R2 vagy újabb rendszert futtató Windows-kiszolgálóra van szükség. Az alkalmazásproxy-összekötőt a kiszolgálóra telepíti. Az összekötő kiszolgálónak csatlakoznia kell az Azure-beli alkalmazásproxy-szolgáltatásokhoz és a közzétételre kívánt helyszíni alkalmazásokhoz.

Az éles környezetben való magas rendelkezésre állás érdekében azt javasoljuk, hogy egynél több Windows-kiszolgálóval legyen több. Ebben az oktatóanyagban elegendő egy Windows-kiszolgáló.

> [!IMPORTANT]
> Ha az összekötőt Windows Server 2019-re telepíti, http2-korlátozás van. A verzió összekötőjének használatára kerülő megoldás a következő beállításkulcs hozzáadása és a kiszolgáló újraindítása. Megjegyzés, ez egy gép rendszerleíró széles kulcs. 
    ```
    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Internet Settings\WinHttp\EnableDefaultHttp2 (DWORD) Value: 0 
    ```

#### <a name="recommendations-for-the-connector-server"></a>Javaslatok az összekötő kiszolgálóhoz

1. Fizikailag keresse meg az összekötő kiszolgálót az alkalmazáskiszolgálók közelében az összekötő és az alkalmazás közötti teljesítmény optimalizálása érdekében. További információt a [Hálózati topológia szempontjai című témakörben talál.](application-proxy-network-topology.md)
1. Az összekötő kiszolgálónak és a webalkalmazások kiszolgálóinak ugyanahhoz az Active Directory tartományhoz kell tartozniuk, vagy át kell nyúlniuk a megbízható tartományokhoz. Ha a kiszolgálók ugyanabban a tartományban vannak, vagy megbízó tartományokban vannak, az integrált Windows-hitelesítéssel (IWA) és a Kerberos korlátozott delegálással (KCD) való egyszeri bejelentkezés (SSO) használata követelmény. Ha az összekötő kiszolgáló és a webalkalmazás-kiszolgálók különböző Active Directory-tartományokban találhatók, erőforrás-alapú delegálást kell használnia egyszeri bejelentkezéshez. További információ: [KCD for single sign-on with Application Proxy](application-proxy-configure-single-sign-on-with-kcd.md).

> [!WARNING]
> Ha telepítette az Azure AD password protection proxy, ne telepítse az Azure AD alkalmazásproxy és az Azure AD password protection proxy együtt ugyanazon a gépen. Az Azure AD alkalmazásproxy és az Azure AD password protection proxy az Azure AD Connect Agent Updater szolgáltatás különböző verzióit telepíti. Ezek a különböző verziók nem kompatibilisek, ha együtt vannak telepítve ugyanazon a gépen.

#### <a name="tls-requirements"></a>TLS-követelmények

Az alkalmazásproxy-összekötő telepítése előtt engedélyezni kell a Windows összekötő kiszolgálón a TLS 1.2-es rendszert.

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
> Annak érdekében, hogy a kategóriájában legjobb titkosítást biztosítsa ügyfeleinknek, az Alkalmazásproxy szolgáltatás csak a TLS 1.2 protokollokra korlátozza a hozzáférést. Ezeket a változásokat 2019. augusztus 31-e óta fokozatosan vezették be és léptették hatályba. Győződjön meg arról, hogy az összes ügyfél-kiszolgáló és böngésző-kiszolgáló kombináció frissül, hogy a TLS 1.2-t használja az alkalmazásproxy szolgáltatással való kapcsolat fenntartásához. Ezek közé tartoznak azok az ügyfelek, amelyeket a felhasználók az alkalmazásproxyn keresztül közzétett alkalmazások eléréséhez használnak. A hasznos hivatkozásokat és erőforrásokat az Előkészítés az [Office 365-ben a TLS 1.2-es számra](https://support.microsoft.com/help/4057306/preparing-for-tls-1-2-in-office-365) való felkészülés e-gényére részben található.

## <a name="prepare-your-on-premises-environment"></a>A helyszíni környezet előkészítése

Kezdje azzal, hogy engedélyezi az Azure-adatközpontokkal való kommunikációt, hogy előkészítse a környezetet az Azure AD alkalmazásproxyhoz. Ha tűzfal van az elérési úton, győződjön meg róla, hogy nyitva van. A nyílt tűzfal lehetővé teszi, hogy az összekötő HTTPS (TCP) kérelmeket küldjön az alkalmazásproxynak.

### <a name="open-ports"></a>Portok megnyitása

Nyissa meg a következő portokat a **kimenő** forgalomhoz.

   | Portszám | Hogyan használják |
   | --- | --- |
   | 80 | Tanúsítvány-visszavonási listák (CRL-ek) letöltése a TLS/SSL tanúsítvány érvényesítése közben |
   | 443 | Az alkalmazásproxy-szolgáltatással folytatott összes kimenő kommunikáció |

Ha a tűzfal az eredeti felhasználók szerint kényszeríti a forgalmat, nyissa meg a 80-as és 443-as portokat a hálózati szolgáltatásként futó Windows-szolgáltatásokból érkező forgalom számára is.

### <a name="allow-access-to-urls"></a>Url-ekhez való hozzáférés engedélyezése

Hozzáférés engedélyezése a következő URL-ekhez:

| URL-cím | Hogyan használják |
| --- | --- |
| \*msappproxy.net.<br>\*servicebus.windows.net | Az összekötő és az alkalmazásproxy felhőszolgáltatása közötti kommunikáció |
| mscrl.microsoft.com:80<br>crl.microsoft.com:80<br>ocsp.msocsp.com:80<br>www.microsoft.com:80 | Az összekötő ezeket az URL-címeket használja a tanúsítványok ellenőrzéséhez. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>\*.microsoftonline.com<br>\*microsoftonline-p.com<br>\*msauth.net<br>\*msauthimages.net<br>\*msecnd.net<br>\*msftauth.net<br>\*msftauthimages.net<br>\*phonefactor.net.<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctdl.windowsupdate.com:80 | Az összekötő ezeket az URL-címeket használja a regisztrációs folyamat során. |

Engedélyezheti a kapcsolatot \*.msappproxy.net \*és .servicebus.windows.net, ha a tűzfal vagy proxy lehetővé teszi a DNS engedélyezési listák konfigurálását. Ha nem, engedélyeznie kell a hozzáférést az [Azure IP-tartományokhoz és a szolgáltatáscímkékhez – nyilvános felhő.](https://www.microsoft.com/download/details.aspx?id=56519) Az IP-tartományok hetente frissülnek.

## <a name="install-and-register-a-connector"></a>Összekötő telepítése és regisztrálása

Az alkalmazásproxy használatához telepítsen egy összekötőt minden olyan Windows-kiszolgálóra, amelyet az alkalmazásproxy szolgáltatással használ. Az összekötő egy ügynök, amely kezeli a kimenő kapcsolatot a helyszíni alkalmazáskiszolgálók alkalmazásproxy az Azure AD-ben. Telepíthet egy összekötőt olyan kiszolgálókra, amelyek más hitelesítési ügynököket is telepítettek, például az Azure AD Connectet.

Az összekötő telepítése:

1. Jelentkezzen be az [Azure Portalon](https://portal.azure.com/) az alkalmazásproxyt használó könyvtár alkalmazás-rendszergazdájaként. Ha például a bérlői tartomány contoso.com, admin@contoso.com a rendszergazda kell, vagy bármely más rendszergazdai alias az adott tartományban.
1. Válassza ki a felhasználónevét a jobb felső sarokban. Ellenőrizze, hogy be van-e jelentkezve egy alkalmazásproxyt használó könyvtárba. Ha könyvtárakat kell módosítania, válassza a **Könyvtárváltás** lehetőséget, és válasszon egy alkalmazásproxyt használó könyvtárat.
1. A bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.
1. A **Kezelés csoportban**válassza **az Alkalmazásproxy lehetőséget.**
1. Válassza **az Összekötő szolgáltatás letöltése lehetőséget.**

    ![Az összekötő szolgáltatás letöltése a Szolgáltatási feltételek megtekintéséhez](./media/application-proxy-add-on-premises-application/application-proxy-download-connector-service.png)

1. Olvassa el a Szolgáltatási feltételeket. Ha készen áll, válassza **a Feltételek elfogadása & letöltése**lehetőséget.
1. Az összekötő telepítéséhez válassza a **Futtatás** lehetőséget az ablak alján. Megnyílik egy telepítővarázsló.
1. A szolgáltatás telepítéséhez kövesse a varázsló utasításait. Amikor a rendszer kéri, hogy regisztrálja az összekötőt az Azure AD-bérlő alkalmazásproxyjával, adja meg az alkalmazás rendszergazdai hitelesítő adatait.
    - Az Internet Explorer (IE) esetében, ha az **IE fokozott biztonsági konfigurációja** **Be**értékre van állítva, előfordulhat, hogy nem jelenik meg a regisztrációs képernyő. A hozzáféréshez kövesse a hibaüzenet utasításait. Győződjön meg arról, hogy **az Internet Explorer fokozott biztonsági beállításai** nak beállítása **Ki**.

### <a name="general-remarks"></a>Általános megjegyzések

Ha korábban már telepített egy összekötőt, telepítse újra a legújabb verzió t. A korábban kiadott verziókról és azok módosításairól az [Alkalmazásproxy: Verziókiadási előzmények](application-proxy-release-version-history.md)című témakörben talál információt.

Ha úgy dönt, hogy egynél több Windows-kiszolgálót szeretne a helyszíni alkalmazásokhoz, telepítenie kell és regisztrálnia kell az összekötőt minden kiszolgálón. Az összekötők összekötőcsoportokba rendezheti. További információ: [Connector groups](application-proxy-connector-groups.md).

Ha a szervezet proxykiszolgálókat használ az internethez való csatlakozáshoz, konfigurálnia kell őket az alkalmazásproxyhoz.  További információt a [Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)című témakörben talál. 

Az összekötőkről, a kapacitástervezésről és a naprakészségükről az [Azure AD alkalmazásproxy-összekötők ismertetése](application-proxy-connectors.md)című témakörben talál további információt.

## <a name="verify-the-connector-installed-and-registered-correctly"></a>A megfelelően telepített és regisztrált összekötő ellenőrzése

Az Azure Portalon vagy a Windows-kiszolgálón ellenőrizheti, hogy egy új összekötő megfelelően van-e telepítve.

### <a name="verify-the-installation-through-azure-portal"></a>A telepítés ellenőrzése az Azure Portalon keresztül

A megfelelően telepített és regisztrált csatlakozó ellenőrzése:

1. Jelentkezzen be a bérlői könyvtárba az [Azure Portalon.](https://portal.azure.com)
1. A bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget, majd a **Kezelés** szakaszban válassza az **Alkalmazásproxy** lehetőséget. Ezen az oldalon az összes összekötő és összekötőcsoport megjelenik.
1. Az összekötő megtekintésével ellenőrizheti annak részleteit. Az összekötőket alapértelmezés szerint ki kell bővíteni. Ha a megtekinteni kívánt összekötő nincs kibontva, bontsa ki az összekötőt a részletek megtekintéséhez. Az aktív zöld címke azt jelzi, hogy az összekötő csatlakozhat a szolgáltatáshoz. Annak ellenére azonban, hogy a címke zöld, a hálózati probléma továbbra is blokkolhatja az összekötő üzenetek fogadását.

    ![Azure AD alkalmazásproxy-összekötők](./media/application-proxy-add-on-premises-application/app-proxy-connectors.png)

Az összekötők telepítésével kapcsolatos további segítségért olvassa el [az Alkalmazásproxy-összekötő telepítése a probléma című témakört.](application-proxy-connector-installation-problem.md)

### <a name="verify-the-installation-through-your-windows-server"></a>A telepítés ellenőrzése a Windows-kiszolgálón keresztül

A megfelelően telepített és regisztrált csatlakozó ellenőrzése:

1. Nyissa meg a Windows-szolgáltatások **Windows** kezelőjét a Windows-kulcsra kattintva és írja be a *services.msc*.
1. Ellenőrizze, hogy a következő két szolgáltatás **Running**állapota fut-e.
   - **A Microsoft AAD alkalmazásproxy-összekötő** lehetővé teszi a kapcsolatot.
   - **A Microsoft AAD Alkalmazásproxy-összekötő frissítő** automatikus frissítési szolgáltatás. A frissítő ellenőrzi az összekötő új verzióit, és szükség szerint frissíti az összekötőt.

     ![Az alkalmazásproxy összekötőjének szolgáltatásai – képernyőfelvétel](./media/application-proxy-add-on-premises-application/app_proxy_services.png)

1. Ha a szolgáltatások állapota nem **fut,** kattintson a jobb gombbal az egyes szolgáltatások kijelöléséhez, és válassza a **Start parancsot.**

## <a name="add-an-on-premises-app-to-azure-ad"></a>Helyszíni alkalmazás hozzáadása az Azure AD-hez

Most, hogy előkészítette a környezetet, és telepített egy összekötőt, készen áll a helyszíni alkalmazások hozzáadására az Azure AD-hez.  

1. Jelentkezzen be rendszergazdaként az [Azure Portalon.](https://portal.azure.com/)
2. A bal oldali navigációs panelen válassza az **Azure Active Directory**lehetőséget.
3. Válassza **a Vállalati alkalmazások**lehetőséget, majd az Új **alkalmazás**lehetőséget.
4. A **helyszíni alkalmazások** szakaszban válassza **a Helyszíni alkalmazás hozzáadása**lehetőséget.
5. A **Saját helyszíni alkalmazás hozzáadása** szakaszban adja meg a következő információkat az alkalmazásról:

    | Mező | Leírás |
    | :---- | :---------- |
    | **Név** | Az alkalmazás neve, amely megjelenik a hozzáférési panelen és az Azure Portalon. |
    | **Belső URL-cím** | Az alkalmazás elérésének URL-címe a magánhálózaton belülről. Megadhat egyedi elérési utat a háttérkiszolgálón a közzétételhez, míg a kiszolgáló további része nem lesz közzétéve. Ily módon különböző webhelyeket tehet közzé ugyanazon a kiszolgálón, mint a különböző alkalmazásokat, és mindegyiknek saját nevet és hozzáférési szabályokat adhat.<br><br>Ha közzétesz egy útvonalat, győződjön meg róla, hogy az tartalmaz minden szükséges lemezképet, szkriptet és stíluslapot az alkalmazásához. Ha például az alkalmazás https:\//yourapp/app, és https:\//yourapp/media helyen található\/képeket használ, akkor közzé kell tennie a https: /yourapp/ elérési utat. Ennek a belső URL-nek nem kell a felhasználók által látott céloldalnak lennie. További információt a [Közzétett alkalmazások egyéni kezdőlapjának beállítása](application-proxy-configure-custom-home-page.md)című témakörben talál. |
    | **Külső URL-cím** | Az alkalmazás hálózaton kívüli eléréséhez a felhasználók által imitátrát használó cím. Ha nem szeretné használni az alapértelmezett alkalmazásproxy-tartományt, olvassa el az [egyéni tartományokat az Azure AD alkalmazásproxyban.](application-proxy-configure-custom-domain.md)|
    | **Hitelesítés előtti hitelesítés** | Hogyan ellenőrzi az alkalmazásproxy a felhasználókat, mielőtt hozzáférést biztosítana számukra az alkalmazáshoz.<br><br>**Azure Active Directory** – Az alkalmazásproxy átirányítja a felhasználókat, hogy jelentkezzenek be az Azure AD-vel, amely hitelesíti a címtárra és az alkalmazásra vonatkozó engedélyeiket. Azt javasoljuk, hogy tartsa meg ezt a beállítást alapértelmezettként, hogy kihasználhassa az Azure AD biztonsági funkcióit, például a feltételes hozzáférést és a többtényezős hitelesítést. **Az Azure Active Directory** szükséges az alkalmazás figyeléséhez a Microsoft Cloud Application Security.<br><br>**Áteresztő)** A felhasználóknak nem kell hitelesíteniük az Azure AD-t az alkalmazás eléréséhez. Továbbra is beállíthat hitelesítési követelményeket a háttérrendszeren. |
    | **Összekötő csoport** | Az összekötők feldolgozzák az alkalmazás hoz való távoli hozzáférést, és az összekötőcsoportok segítségével régió, hálózat vagy cél szerint rendszerezheti az összekötőket és alkalmazásokat. Ha még nem hozott létre összekötőcsoportokat, az alkalmazás az **Alapértelmezett**értékhez van rendelve.<br><br>Ha az alkalmazás WebSockets segítségével csatlakozik, a csoport összes összekötőjének 1.5.612.0-s vagy újabb verziónak kell lennie.|

6. Szükség esetén adja meg a **További beállításokat.** A legtöbb alkalmazás esetében ezeket a beállításokat az alapértelmezett állapotban kell tartani. 

    | Mező | Leírás |
    | :---- | :---------- |
    | **Háttéralkalmazás időmegmaradása** | Állítsa ezt az értéket **hosszú** értékre, ha az alkalmazás hitelesítése és csatlakozása lassú. Alapértelmezés szerint a háttéralkalmazás időkimenő ideje 85 másodperc. Ha hosszúra van állítva, a háttéridő-időköz 180 másodpercre nő. |
    | **Csak HTTP-s cookie használata** | Állítsa ezt az értéket **Igen** értékre, ha azt szeretné, hogy az alkalmazásproxy-cookie-k tartalmazzák a HTTPOnly jelzőt a HTTP-válasz fejlécében. Ha a Távoli asztali szolgáltatások szolgáltatást használja, állítsa ezt az értéket **Nem**értékre.|
    | **Biztonságos cookie használata**| Állítsa ezt az értéket **Igen** értékre, ha cookie-kat továbbít biztonságos csatornán, például titkosított HTTPS-kérelemen keresztül.
    | **Állandó cookie használata**| Tartsa ezt az értéket **Nem**értékre. Ezt a beállítást csak olyan alkalmazásokhoz használja, amelyek nem tudják megosztani a cookie-kat a folyamatok között. A cookie-beállításokról további információt a [Cookie-beállítások a helyszíni alkalmazások Azure Active Directoryban való eléréséhez című témakörben talál.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-configure-cookie-settings)
    | **URL-ek fordítása a fejlécekben** | Tartsa meg ezt az értéket **Igen** formában, kivéve, ha az alkalmazás a hitelesítési kérelemben az eredeti állomásfejlécet igényelt. |
    | **URL-ek fordítása az alkalmazástörzsben** | Tartsa meg ezt az értéket **nemként,** kivéve, ha más helyszíni alkalmazásokra mutató html-hivatkozásokat használ, és nem használ egyéni tartományokat. További információ: [Fordítás csatolása alkalmazásproxyval](application-proxy-configure-hard-coded-link-translation.md).<br><br>Állítsa ezt az értéket **Igen** értékre, ha az alkalmazást a Microsoft Cloud App Security (MCAS) segítségével kívánja figyelni. További információt a [Valós idejű alkalmazáshozzáférés-figyelés konfigurálása a Microsoft Cloud App Security és az Azure Active Directory segítségével című témakörben talál.](application-proxy-integrate-with-microsoft-cloud-application-security.md) |

7. Válassza a **Hozzáadás** lehetőséget.

## <a name="test-the-application"></a>Az alkalmazás tesztelése

Készen áll az alkalmazás tesztelésére. A következő lépésekben hozzákell adnia egy felhasználói fiókot az alkalmazáshoz, és megpróbál bejelentkezni.

### <a name="add-a-user-for-testing"></a>Felhasználó hozzáadása teszteléshez

Mielőtt hozzáadná a felhasználót az alkalmazáshoz, ellenőrizze, hogy a felhasználói fiók már rendelkezik-e engedéllyel az alkalmazás vállalati hálózaton belüli eléréséhez.

Tesztfelhasználó hozzáadása:

1. Válassza **a Vállalati alkalmazások**lehetőséget, majd válassza ki a tesztelni kívánt alkalmazást.
2. Válassza **az Első lépések**lehetőséget, majd a Felhasználó **hozzárendelése teszteléshez**lehetőséget.
3. A **Felhasználók és csoportok**csoportban válassza a Felhasználó hozzáadása **lehetőséget.**
4. A **Hozzárendelés hozzáadása**csoportban válassza a Felhasználók és **csoportok**lehetőséget. Megjelenik **a Felhasználó és csoportok** szakasz.
5. Válassza ki a hozzáadni kívánt fiókot.
6. Válassza **a Kijelölés**lehetőséget, majd a **Hozzárendelés**lehetőséget.

### <a name="test-the-sign-on"></a>A bejelentkezés tesztelése

Az alkalmazásba való bejelentkezés tesztelése:

1. A tesztelni kívánt alkalmazásból válassza az **Alkalmazásproxy**lehetőséget.
2. A lap tetején válassza az **Alkalmazás tesztelése** lehetőséget az alkalmazáson futtatandó teszt futtatásához, és a konfigurációs problémák ellenőrzéséhez.
3. Győződjön meg arról, hogy először indítsa el az alkalmazást az alkalmazásba való bejelentkezés teszteléséhez, majd töltse le a diagnosztikai jelentést az észlelt problémák megoldási útmutatójának áttekintéséhez.

A hibaelhárításról az [Alkalmazásproxyval kapcsolatos problémák és hibaüzenetek elhárítása](application-proxy-troubleshoot.md)című témakörben nyújt unk témakört.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban előkészítette a helyszíni környezetet az alkalmazásproxyval való együttműködésre, majd telepítette és regisztrálta az alkalmazásproxy-összekötőt. Ezután hozzáadott egy alkalmazást az Azure AD-bérlőhöz. Igazolta, hogy a felhasználó egy Azure AD-fiók használatával tud-e bejelentkezni az alkalmazásba.

A következőket hajtotta végre:
> [!div class="checklist"]
> * Megnyitott portok a kimenő forgalomszámára, és adott URL-címekhez való hozzáférés engedélyezett
> * Telepítette az összekötőt a Windows-kiszolgálóra, és regisztrálta az alkalmazásproxyval
> * Ellenőrizte a megfelelően telepített és regisztrált csatlakozót
> * Hozzáadott egy helyszíni alkalmazást az Azure AD-bérlőhöz
> * Ellenőrzött egy teszt felhasználó tud jel-ra az alkalmazás segítségével egy Azure AD-fiók

Készen áll az alkalmazás egyszeri bejelentkezésre való konfigurálására. Az alábbi hivatkozással egyetlen bejelentkezési módszert választhat, és megkeresheti az egyszeri bejelentkezési útmutatókat.

> [!div class="nextstepaction"]
> [Egyszeri bejelentkezés konfigurálása](what-is-single-sign-on.md#choosing-a-single-sign-on-method)
