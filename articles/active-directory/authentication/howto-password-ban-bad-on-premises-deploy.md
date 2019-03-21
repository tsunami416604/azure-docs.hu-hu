---
title: Azure AD jelszóvédelem üzembe helyezése
description: Az Azure AD jelszóvédelem hozzá tartozó rossz jelszavak helyszíni üzembe helyezése
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: article
ms.date: 02/01/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jsimmons
ms.collection: M365-identity-device-management
ms.openlocfilehash: 54d2d600771316b0a88ea0a2486c0dedd0f84594
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/20/2019
ms.locfileid: "58286533"
---
# <a name="deploy-azure-ad-password-protection"></a>Azure AD jelszóvédelem üzembe helyezése

Most, hogy megismerkedett [jelszavas védelem az Azure AD a Windows Server Active Directory a kényszerítés módja](concept-password-ban-bad-on-premises.md), és hajtsa végre az üzemelő példány a következő lépéssel.

## <a name="deployment-strategy"></a>Üzembe helyezési stratégiához

Azt javasoljuk, hogy központi telepítések rendszervizsgálati módban indítsa el. Vizsgálati üzemmód az alapértelmezett kezdeti beállítása, ahol a jelszavak továbbra is kell beállítani. Hogy le lesz tiltva, az eseménynaplóban vannak rögzítve. Üzembe helyezés után a proxykiszolgálók és a tartományvezérlő ügynökök rendszervizsgálati módban, célszerű figyelemmel kísérni a hatást, amelyet a jelszóházirend lesz a felhasználók és a környezet, ha a házirend van érvényben.

Az ellenőrzési szakaszban számos szervezet keresse meg, hogy:

* A meglévő operatív folyamatok használata biztonságosabb, jelszavak javítása van szükségük.
* Felhasználók gyakran használjon biztonságos jelszavakat.
* Szükségük van a felhasználók tájékoztatása a közelgő változást végpontbiztonság kényszerítése, azokat a lehetséges hatás és biztonságosabb, jelszavak kiválasztása.

Után a szolgáltatás ésszerű rendszervizsgálati módban futott, válthat a konfigurációt az *naplózási* való *érvényesítése* , amelyek több biztonságos jelszót. Ez idő alatt célirányos figyelése, célszerű.

## <a name="deployment-requirements"></a>Központi telepítésére vonatkozó követelmények

* Minden tartományvezérlő, amely a tartományvezérlő-ügynök szolgáltatást a telepített Azure AD jelszóvédelem futtatnia kell a Windows Server 2012 vagy újabb verziója.
* Összes gép, amely a proxy szolgáltatást, a telepített Azure AD jelszóvédelem futtatnia kell a Windows Server 2012 R2 vagy újabb verziója.
* Minden olyan gép, amelyen telepíteni szeretné az Azure AD-jelszó védelme Proxy szolgáltatás telepítve van a .NET 4.7 kell rendelkeznie.
  .NET 4.7 egy teljesen új Windows Server már telepíthető. Ha nem ez a helyzet, töltse le és futtassa a telepítőt címen található [a .NET-keretrendszer 4.7 offline Windows installer](https://support.microsoft.com/en-us/help/3186497/the-net-framework-4-7-offline-installer-for-windows).
* Összes gép, a Universal C futásidejű telepített többek között a tartományvezérlők, amely az Azure AD jelszó-védelem összetevői telepítve kell rendelkeznie. A futtatókörnyezet azáltal, hogy meg kell, hogy a Windows Update frissítéseit kérheti le. Vagy egy operációsrendszer-specifikus frissítési csomag beszerezheti azt. További információkért lásd: [frissítése a Windows Universal C futásidejű](https://support.microsoft.com/help/2999226/update-for-uniersal-c-runtime-in-windows).
* Hálózati kapcsolat között léteznie kell legalább egy tartományvezérlő minden olyan tartományban, és legalább egy kiszolgálót üzemeltető a proxy szolgáltatás jelszavas védelmet. A kapcsolat lehetővé teszi a tartományvezérlő hozzáférés RPC végpont végpontleképező 135-ös port és a proxy szolgáltatást az RPC-kiszolgáló portja. Alapértelmezés szerint az RPC-kiszolgáló portjának egy dinamikus RPC-portot, de konfigurálható, hogy [statikus port használatára](#static).
* A proxy szolgáltatást üzemeltető összes gép a következő végpontok hálózati hozzáféréssel kell rendelkeznie:

    |**Végpont**|**Cél**|
    | --- | --- |
    |`https://login.microsoftonline.com`|Hitelesítési kérelmek|
    |`https://enterpriseregistration.windows.net`|Az Azure AD jelszó-védelmi funkciók|

* A jelszavas védelem proxy szolgáltatást üzemeltető összes gép kimenő TLS 1.2-es HTTP-forgalom engedélyezésére kell konfigurálni.
* A jelszavas védelem és az erdő proxy szolgáltatás regisztrálása az Azure AD egy globális rendszergazdai fiókkal.
* A Windows Server Active Directory-erdő regisztrálni az Azure AD-erdő szintű gyökértartomány az Active Directory tartományi rendszergazdai jogosultságokkal rendelkező fiókkal.
* Minden olyan Active Directory-tartomány a tartományvezérlő-ügynök szolgáltatás szoftvert futtató sysvol-replikáció Distributed File System replikációs (DFSR) kell használnia.
* A tartomány minden olyan tartományvezérlőre, amely a Windows Server 2012 futtatásához engedélyezni kell a kulcsszolgáltató szolgáltatás. Ez a szolgáltatás alapértelmezés szerint engedélyezve van a manuális eseményindító start-n keresztül.

## <a name="single-forest-deployment"></a>Egyerdős üzembe helyezés

Az alábbi ábrán látható, az Azure AD jelszóvédelem alapvető összetevői együttműködésének egy helyszíni Active Directory-környezetben.

![Hogyan működnek együtt az Azure AD jelszó-védelem összetevői](./media/concept-password-ban-bad-on-premises/azure-ad-password-protection.png)

Célszerű áttekinteni, hogyan működik a szoftver, a telepítése előtt. Lásd: [fogalmi áttekintése az Azure AD jelszóvédelem](concept-password-ban-bad-on-premises.md).

### <a name="download-the-software"></a>A szoftver letöltése

Nincsenek Azure AD jelszóvédelem két szükséges telepítők. Az elérhető a [Microsoft Download Center](https://www.microsoft.com/download/details.aspx?id=57071).

### <a name="install-and-configure-the-proxy-service-for-password-protection"></a>Telepítse és konfigurálja a proxykiszolgáló jelszavas védelmet

1. Válasszon egy vagy több kiszolgáló jelszavas védelmet a proxy szolgáltatás üzemeltetéséhez.
   * Minden ilyen szolgáltatás csak által biztosított jelszóházirendet egyetlen erdő. A gazdagép frissítéséből abban az erdőben egy tartományhoz kell csatlakoznia. Gyermek és a legfelső szintű tartományok támogatottak. Szüksége lesz az erdő minden tartományban legalább egy tartományvezérlő és a jelszó védelmi gép közötti hálózati kapcsolatot.
   * A proxy szolgáltatás egy tartományvezérlőn tesztelési futtathatja. Azonban, hogy tartományvezérlő ezután internetkapcsolattal, amely lehet az egyik biztonsági szempont. Ez a konfiguráció csak tesztelésre ajánlott.
   * Azt javasoljuk, hogy a redundancia biztosítása érdekében legalább két proxykiszolgáló. Lásd: [magas rendelkezésre állású](howto-password-ban-bad-on-premises-deploy.md#high-availability).

1. Telepítse az Azure AD jelszó-védelmi Proxy szolgáltatás használ a `AzureADPasswordProtectionProxySetup.exe` szoftver telepítője.
   * A Szoftvertelepítés nem kell újraindítani. A Szoftvertelepítés előfordulhat, hogy automatizálható a szokásos eljárásokkal MSI, például:

      `AzureADPasswordProtectionProxySetup.exe /quiet`

      > [!NOTE]
      > A Windows tűzfal szolgáltatást kell futnia, a telepítési hiba elkerülése érdekében AzureADPasswordProtectionProxySetup.msi csomag telepítése előtt. Windows tűzfal nem fut van konfigurálva, a megoldás-e ideiglenes engedélyezése és a tűzfal-szolgáltatás futtatásához a telepítés során. A proxy szoftver nincs konkrét függőségre rendelkezik a Windows tűzfalat a telepítés után. Ha egy külső tűzfalat használ, azt kell továbbra is konfigurálni megfelelnek a központi telepítés. Ezek közé tartozik, hogy a port: 135-ös és a proxy RPC-kiszolgáló port bejövő hozzáférést. Lásd: [központi telepítésére vonatkozó követelmények](howto-password-ban-bad-on-premises-deploy.md#deployment-requirements).

1. Nyisson meg egy PowerShell-ablakot rendszergazdaként.
   * A jelszó proxy szoftver tartalmaz egy új PowerShell-modul *AzureADPasswordProtection*. Különböző parancsmagok a PowerShell-modult futtassa az alábbi lépéseket. Importálja az új modult a következőképpen:

      ```PowerShell
      Import-Module AzureADPasswordProtection
      ```

   * Ellenőrizze, hogy a szolgáltatás fut, használja a következő PowerShell-parancsot:

      `Get-Service AzureADPasswordProtectionProxy | fl`.

     Az eredmény meg kell jelennie egy **állapot** "Üzemelni."

1. A proxy regisztrálása.
   * 3. lépés befejezése után a proxy szolgáltatás fut a gépen. De a szolgáltatás még nem rendelkezik a szükséges hitelesítő adatokat az Azure AD-kommunikációhoz. Az Azure AD-regisztrációs szükség:

     `Register-AzureADPasswordProtectionProxy`

     Ez a parancsmag az Azure-bérlő globális rendszergazdai hitelesítő adatokat igényel. A helyszíni Active Directory tartományi rendszergazdai jogosultságokkal az erdő gyökértartományában is szükséges. Miután ez a parancs sikeres lesz, miután egy proxy szolgáltatás azt további indítások sikeresek lesznek, de nem szükségesek.

      A `Register-AzureADPasswordProtectionProxy` parancsmag támogatja a következő három hitelesítési mód.

     * Interaktív hitelesítési mód:

        ```PowerShell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```
        > [!NOTE]
        > Ebben a módban a Server Core operációs rendszereken nem működik. Ehelyett használja a következő hitelesítési módok egyikét. Ebben a módban is sikertelen lehet, ha az Internet Explorer fokozott biztonsági beállításai engedélyezve van. A megoldás, hogy tiltsa le a konfigurációs, a proxy regisztrálása, majd újra engedélyeznie.

     * Eszköz-kód hitelesítési mód:

        ```PowerShell
        Register-AzureADPasswordProtectionProxy -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Majd egy másik eszközön megjelenő utasítások alapján a hitelesítés befejezéséhez.

     * Beavatkozás nélküli (jelszóalapú) hitelesítési mód:

        ```PowerShell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionProxy -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ebben a módban sikertelen lesz, ha az Azure multi-factor Authentication megadása kötelező. Ebben az esetben használja az előző két hitelesítési módok egyikét.

       Adja meg, hogy jelenleg nincs a *- ForestCredential* paramétert, amely a jövőbeni funkció számára van fenntartva.

   
   Regisztráció a proxy szolgáltatás jelszavas védelmet az szükséges csak egyszer a szolgáltatás élettartama során. Ezt követően a proxy szolgáltatás automatikusan végez minden egyéb szükséges karbantartás.

   > [!TIP]
   > Előfordulhat, hogy egy észrevehető késleltetés, hogy ez a parancsmag futtatása egy adott Azure-bérlő esetében először befejezése előtt. Jelentett hiba, hacsak nem kell aggódnia a késleltetés.

1. Regisztrálja az erdőben.
   * Az Azure használatával kommunikálnak a szükséges hitelesítő adatokat a helyszíni Active Directory-erdőben kell inicializálnia a `Register-AzureADPasswordProtectionForest` PowerShell-parancsmagot. A parancsmag az Azure-bérlőhöz tartozó globális rendszergazdai hitelesítő adatokat igényel. A helyszíni Active Directory tartományi rendszergazdai jogosultságok az erdő gyökértartományában is szükségesek. Ez a lépés minden erdőre egyszer fut.

      A `Register-AzureADPasswordProtectionForest` parancsmag támogatja a következő három hitelesítési mód.

     * Interaktív hitelesítési mód:

        ```PowerShell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com'
        ```
        > [!NOTE]
        > Ebben a módban a Server Core operációs rendszereken nem fog működni. Ehelyett használja a következő két hitelesítési módok egyikét. Ebben a módban is sikertelen lehet, ha az Internet Explorer fokozott biztonsági beállításai engedélyezve van. A megoldás, hogy tiltsa le a konfigurációs, a proxy regisztrálása, majd újra engedélyeznie.  

     * Eszköz-kód hitelesítési mód:

        ```PowerShell
        Register-AzureADPasswordProtectionForest -AccountUpn 'yourglobaladmin@yourtenant.onmicrosoft.com' -AuthenticateUsingDeviceCode
        To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code XYZABC123 to authenticate.
        ```

        Majd egy másik eszközön megjelenő utasítások alapján a hitelesítés befejezéséhez.

     * Beavatkozás nélküli (jelszóalapú) hitelesítési mód:
        ```PowerShell
        $globalAdminCredentials = Get-Credential
        Register-AzureADPasswordProtectionForest -AzureCredential $globalAdminCredentials
        ```

        > [!NOTE]
        > Ebben a módban sikertelen lesz, ha az Azure multi-factor Authentication megadása kötelező. Ebben az esetben használja az előző két hitelesítési módok egyikét.

       Ezekben a példákban csak akkor sikeres, ha az aktuálisan bejelentkezett felhasználó egyben a legfelső szintű tartomány Active Directory tartományi rendszergazda. Ha ez nem az eset, alternatív tartományi hitelesítő adatokat adhat meg a *- ForestCredential* paraméter.

   > [!NOTE]
   > Ha a proxykiszolgálók több vannak telepítve a környezetben, mely proxykiszolgálót, regisztrálhat az erdő nem számít.

   > [!TIP]
   > Előfordulhat, hogy egy észrevehető késleltetés, hogy ez a parancsmag futtatása egy adott Azure-bérlő esetében először befejezése előtt. Jelentett hiba, hacsak nem kell aggódnia a késleltetés.

   Regisztrálása az Active Directory-erdő nem szükséges csak egyszer az erdő élettartama során. Ezt követően a Domain Controller ügynökök az erdő automatikusan elvégzi minden egyéb szükséges karbantartás. Miután `Register-AzureADPasswordProtectionForest` erdő esetében sikeresen lefut, a parancsmag további indítások sikeres, de nem szükségesek.

   A `Register-AzureADPasswordProtectionForest` sikeres, legalább egy tartományvezérlő Windows Server 2012 vagy újabb rendszert futtató elérhetőnek kell lennie a proxykiszolgálót a tartományban. De a tartományvezérlő ügynökszoftver nem rendelkezik a tartományvezérlők, ez a lépés előtt kell telepíteni.

1. A proxy HTTP-proxyn keresztül kommunikálnak a jelszavas védelem szolgáltatás konfigurálása.

   Ha a környezet egy adott HTTP-proxy kommunikálni az Azure használatát igényli, ezt a módszert használja: Hozzon létre egy *AzureADPasswordProtectionProxy.exe.config* %ProgramFiles%\Azure Active jelszó védelmi Proxy\Service mappában található fájl. Tartalmazza az alábbi tartalommal:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Ha a HTTP-proxy hitelesítést igényel, adja hozzá a *useDefaultCredentials* címkét:

      ```xml
      <configuration>
        <system.net>
          <defaultProxy enabled="true" useDefaultCredentials="true">
           <proxy bypassonlocal="true"
               proxyaddress="http://yourhttpproxy.com:8080" />
          </defaultProxy>
        </system.net>
      </configuration>
      ```

   Mindkét esetben cserélje le a `http://yourhttpproxy.com:8080` -címmel és az adott HTTP-proxykiszolgáló portját.

   Ha a HTTP-proxy van konfigurálva velünk a kapcsolatot az engedélyezési házirend, hozzáférést kell biztosítania a gép, amely a proxy szolgáltatást a jelszavas védelem az Active Directory számítógépfiókjához.

   Javasoljuk, hogy állítsa le és indítsa újra a proxy szolgáltatás létrehozása vagy frissítése után a *AzureADPasswordProtectionProxy.exe.config* fájlt.

   A proxy szolgáltatás nem támogatja a megadott hitelesítő adatok használatát egy HTTP-proxy való kapcsolódáshoz.

1. Nem kötelező: Konfigurálja a proxykiszolgáló jelszavas védelmet egy adott portot figyeljen.
   * A tartományvezérlő ügynökszoftver jelszavas védelmet a tartományvezérlőkön RPC Protokollt használ TCP-n keresztül a proxy szolgáltatással való kommunikációra. Alapértelmezés szerint a proxy szolgáltatást figyel minden rendelkezésre álló dinamikus RPC-végpont. De be lehet állítani egy adott TCP-porton figyeljen a szolgáltatást, amennyiben ez szükséges hálózati topológiát, vagy a környezetében a tűzfalra vonatkozó követelmények miatt.
      * <a id="static" /></a>A szolgáltatás futtatásához egy statikus port megadásához használja a `Set-AzureADPasswordProtectionProxyConfiguration` parancsmagot.
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort <portnumber>
         ```

         > [!WARNING]
         > Állítsa le kell, és indítsa újra a szolgáltatást, a módosítások érvénybe léptetéséhez.

      * A szolgáltatás futtatásához egy dinamikus port megadásához ugyanazzal az eljárással, de beállítása *StaticPort* történő visszaállítás:
         ```PowerShell
         Set-AzureADPasswordProtectionProxyConfiguration –StaticPort 0
         ```

         > [!WARNING]
         > Állítsa le kell, és indítsa újra a szolgáltatást, a módosítások érvénybe léptetéséhez.

   > [!NOTE]
   > A jelszavas védelmet proxy szolgáltatás manuális újraindítását igényli, a portkonfigurációjának módosítását követően. Azonban nem kell újraindítani a tartományvezérlő szolgáltatás ügynökszoftver tartományvezérlőkön, konfigurációs módosítások végrehajtása után.

   * A szolgáltatás a jelenlegi konfiguráció lekérdezése, használja a `Get-AzureADPasswordProtectionProxyConfiguration` parancsmagot:

      ```PowerShell
      Get-AzureADPasswordProtectionProxyConfiguration | fl

      ServiceName : AzureADPasswordProtectionProxy
      DisplayName : Azure AD password protection Proxy
      StaticPort  : 0
      ```

### <a name="install-the-dc-agent-service"></a>A tartományvezérlő ügynökszolgáltatásának telepítése

   Telepítse a DC ügynökszolgáltatás jelszavas védelmet a `AzureADPasswordProtectionDCAgentSetup.msi` csomagot.

   A szoftver telepítése vagy kiszolgálón.%0, újra kell indítani. Ennek az az oka a jelszószűrő DLL-ek csak betöltve, vagy a számítógép újraindítását a memóriából.

   Telepítheti a DC-ügynökszolgáltatás olyan számítógépen, amelyen még nem tartományvezérlő. Ebben az esetben a szolgáltatás fogja indítása és futtatása, de inaktív marad mindaddig, amíg a gép egy tartományvezérlő hitelesítenie kelljen magát.

   A szoftver telepítését a szokásos az MSI-eljárások használatával automatizálható. Példa:

   `msiexec.exe /i AzureADPasswordProtectionDCAgentSetup.msi /quiet /qn`

   > [!WARNING]
   > A msiexec példaparancs itt egy azonnali újraindítását eredményezi. Amely elkerülése érdekében használja a `/norestart` jelzőt.

A telepítés befejeződött, a tartományvezérlő ügynök szoftver telepítve van a tartományvezérlőn, és a számítógép újraindítása után. További konfigurálást nem kötelező vagy nem lehetséges.

## <a name="multiple-forest-deployments"></a>Több erdő üzembe helyezés

Nem vonatkoznak további követelmények üzembe helyezéséhez az Azure AD jelszóvédelem több-erdőkön keresztüli. Minden olyan erdőben, egymástól függetlenül van konfigurálva, a "egyerdős üzembe helyezés" szakaszban leírtak szerint. Minden egyes jelszó védelme proxy csak támogatja az erdőhöz, amelyik van csatlakoztatva, a tartományvezérlőket. A jelszó szoftvert minden olyan erdőben nem észleli a jelszó szoftver, amelyet más erdőkben, függetlenül az Active Directory bizalmi kapcsolati konfigurációkat.

## <a name="read-only-domain-controllers"></a>Írásvédett tartományvezérlők

Jelszó-módosítások vagy beállítja nem feldolgozása, és megőrzi a írásvédett tartományvezérlők (RODC-k). Írható tartományvezérlők továbbítja azokat. Így nem kell a tartományvezérlő ügynök szoftver telepítéséhez az RODC-k.

## <a name="high-availability"></a>Magas rendelkezésre állás

A jelszavas védelem fő rendelkezésre állás miatt aggódnának proxy kiszolgálók rendelkezésre állásának esetén az erdőben lévő tartományvezérlők meg új szabályzatok vagy más adatokat letölteni az Azure-ból. Minden tartományvezérlő ügynök egyszerű ciklikus multiplexelés-stílusú algoritmust használ, mely proxykiszolgáló hívása során. Az ügynök kihagyja a proxykiszolgálók, amely nem válaszol. A legtöbb teljes csatlakoztatott Active Directory-környezetek könyvtárat és a sysvol mappa állapot kifogástalan állapotú replikáció rendelkezik a két proxykiszolgáló ez elegendő rendelkezésre állásának biztosításához. Ennek eredményeként az új szabályzatok időben történő letöltését és egyéb adatokat. Azonban telepíthet további proxykiszolgálót.

A kialakítás a tartományvezérlő ügynök szoftver csökkenti a szokásos problémák kapcsolódó, magas rendelkezésre állású. A tartományvezérlő-ügynök a legutóbb letöltött jelszóházirend tartalmazó helyi gyorsítótár tárolja. Akkor is, ha a proxykiszolgálók elérhetetlenné regisztrált, a tartományvezérlő-ügynökök továbbra is a gyorsítótárazott jelszóházirend kényszerítése. Egy ésszerű frissítési gyakoriság a jelszóházirendek nagy központi telepítés rendszer általában *nap*, nem óra vagy kevesebb. Így rövid valamilyen okból kimaradás lép a proxykiszolgálók nem jelentős hatással vannak az Azure AD jelszóvédelem.

## <a name="next-steps"></a>További lépések

Most, hogy telepítette a szolgáltatásokat, amelyek a helyszíni kiszolgálókon kell az Azure AD jelszóvédelem [hajtsa végre a telepítés utáni konfigurációs és információk jelentéséért összegyűjtési](howto-password-ban-bad-on-premises-operations.md) a telepítés befejezéséhez.

[Az Azure AD jelszóvédelem fogalmi áttekintése](concept-password-ban-bad-on-premises.md)
