---
title: Az Azure AD alkalmazásproxy-összekötők ismertetése | Microsoft dokumentumok
description: Ismerteti az Azure AD alkalmazásproxy-összekötők alapjait.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: mimart
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7f1b8b9af8f90629d087246edf0cb3426bd9b66c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406832"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Az Azure AD alkalmazásproxy-összekötők megismerése

Az összekötők teszik lehetővé az Azure AD alkalmazásproxyt. Egyszerűek, könnyen telepíthetők és karbantarthatók, és rendkívül erősek. Ez a cikk ismerteti, mi összekötők, hogyan működnek, és néhány javaslatot, hogyan optimalizálhatja a központi telepítést.

## <a name="what-is-an-application-proxy-connector"></a>Mi az alkalmazásproxy-összekötő?

Az összekötők olyan könnyű ügynökök, amelyek a helyszínen ülnek, és megkönnyítik az alkalmazásproxy-szolgáltatáshoz való kimenő kapcsolatot. Az összekötőket olyan Windows Server rendszerre kell telepíteni, amely hozzáfér a háttéralkalmazáshoz. Összekötők összekötőcsoportokba rendezheti, és minden csoport kezeli a forgalmat adott alkalmazásokhoz.

## <a name="requirements-and-deployment"></a>Követelmények és telepítés

Az alkalmazásproxy sikeres üzembe helyezéséhez legalább egy összekötőre van szükség, de a nagyobb rugalmasság érdekében kettő vagy több szükséges. Telepítse az összekötőt Windows Server 2012 R2 vagy újabb rendszert futtató számítógépre. Az összekötőnek kommunikálnia kell az alkalmazásproxy-szolgáltatással és a közzétett helyszíni alkalmazásokkal.

### <a name="windows-server"></a>Windows Server
Olyan Windows Server 2012 R2 vagy újabb rendszert futtató kiszolgálóra van szükség, amelyre telepítheti az alkalmazásproxy-összekötőt. A kiszolgálónak csatlakoznia kell az Azure-beli alkalmazásproxy-szolgáltatásokhoz és a közzéteszendő helyszíni alkalmazásokhoz.

Az alkalmazásproxy-összekötő telepítése előtt engedélyezni kell a Windows-kiszolgálón a TLS 1.2-es rendszert. A TLS 1.2 engedélyezése a kiszolgálón:

1. Állítsa be a következő beállításkulcsokat:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Kiszolgáló újraindítása

Az összekötő kiszolgáló hálózati követelményeiről az [Alkalmazásproxy használata és az összekötő telepítése](application-proxy-add-on-premises-application.md)című témakörben talál további információt.

## <a name="maintenance"></a>Karbantartás

Az összekötők és a szolgáltatás gondoskodik a magas rendelkezésre állási feladatok. Ezek dinamikusan hozzáadhatók vagy eltávolíthatók. Minden alkalommal, amikor egy új kérelem érkezik, a rendszer az egyik jelenleg elérhető összekötők egyikére irányítja. Ha egy összekötő átmenetileg nem érhető el, nem válaszol erre a forgalomra.

Az összekötők állapot nélküliek, és nincsenek konfigurációs adatok a számítógépen. Az általuk tárolt egyetlen adat a szolgáltatás és a hitelesítési tanúsítvány csatlakoztatásának beállításai. Amikor csatlakoznak a szolgáltatáshoz, lekérik az összes szükséges konfigurációs adatot, és néhány percenként frissítik.

Az összekötők lekérdezik a kiszolgálót is, hogy megtudják, van-e újabb verzió az összekötőnek. Ha talál egyet, az összekötők frissítik magukat.

Az eseménynapló és a teljesítményszámlálók segítségével figyelheti az összekötőket attól a készüléktől, amelyen futnak. Vagy megtekintheti az állapotukat az Azure Portal Alkalmazásproxy lapján:

![Példa: Azure AD alkalmazásproxy-összekötők](./media/application-proxy-connectors/app-proxy-connectors.png)

Nem kell manuálisan törölnie a nem használt összekötőket. Amikor egy összekötő fut, aktív marad, ahogy csatlakozik a szolgáltatáshoz. A nem használt összekötők _inaktívként_ vannak címkézve, és 10 nap inaktivitás után törlődnek. Ha azonban el szeretne távolítani egy összekötőt, távolítsa el az összekötő és az Frissítő szolgáltatást is a kiszolgálóról. Indítsa újra a számítógépet a szolgáltatás teljes eltávolításához.

## <a name="automatic-updates"></a>Automatikus frissítések

Az Azure AD automatikus frissítéseket biztosít az összes üzembe helyezett összekötőhez. Mindaddig, amíg az Application Proxy Connector Updater szolgáltatás fut, az összekötők automatikusan frissülnek. Ha nem látja az Összekötő frissítő szolgáltatást a kiszolgálón, újra kell [telepítenie az összekötőt a](application-proxy-add-on-premises-application.md) frissítések lekéréséhez.

Ha nem szeretné megvárni, amíg egy automatikus frissítés érkezik az összekötőhöz, manuális frissítést tehet. Nyissa meg az [összekötő letöltési lapját](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) azon a kiszolgálón, amelyen az összekötő található, és válassza a **Letöltés lehetőséget.** Ez a folyamat elindítja a helyi összekötő frissítését.

A több összekötővel rendelkező bérlők esetében az automatikus frissítések minden csoportban egyszerre egy összekötőt céloznak meg, hogy megakadályozzák az állásidőt a környezetben.

Az összekötő frissítésekor állásidő léphet fel, ha:
  
- Csak egy összekötővel rendelkezik, javasoljuk, hogy telepítsen egy második összekötőt, és [hozzon létre egy összekötőcsoportot.](application-proxy-connector-groups.md) Ezzel elkerülhető az állásidő, és nagyobb rendelkezésre állást biztosít.  
- Egy összekötő volt a tranzakció közepén, amikor a frissítés megkezdődött. Bár a kezdeti tranzakció elvész, a böngészőnek automatikusan újra meg kell próbálnia a műveletet, vagy frissítheti az oldalt. Amikor a kérelem újraküldésre kerül, a forgalom egy tartalék összekötőre lesz irányítva.

A korábban kiadott verziókról és azok módosításairól az [Alkalmazásproxy- verziókiadási előzmények című témakörben](application-proxy-release-version-history.md)talál információt.

## <a name="creating-connector-groups"></a>Összekötőcsoportok létrehozása

Az összekötőcsoportok lehetővé teszik, hogy adott összekötőket rendeljen hozzá az adott alkalmazások kiszolgálásához. Több összekötőt csoportosíthat, majd minden alkalmazást hozzárendelhet egy csoporthoz.

Az összekötőcsoportok megkönnyítik a nagy üzemelő példányok kezelését. Emellett javítják a késést a különböző régiókban üzemeltetett alkalmazásokkal rendelkező bérlők számára, mivel helyalapú összekötőcsoportokat hozhat létre, hogy csak a helyi alkalmazásokat szolgálhassa ki.

Az összekötőcsoportokról az [Alkalmazások közzététele külön hálózatokon és helyeken összekötőcsoportok használatával című](application-proxy-connector-groups.md)témakörben olvashat bővebben.

## <a name="capacity-planning"></a>Kapacitástervezés

Fontos, hogy győződjön meg arról, hogy a tervezett elegendő kapacitás összekötők között a várható forgalom mennyiségének kezeléséhez. Azt javasoljuk, hogy minden összekötő csoport rendelkezik legalább két összekötők magas rendelkezésre állás és a méretezés. A három csatlakozó optimális abban az esetben, ha bármikor szervizelnie kell egy gépet.

Általánosságban elmondható, hogy minél több felhasználóval rendelkezik, annál nagyobb gépre lesz szüksége. Az alábbi táblázat felvázolja a kötet és a várható késés különböző gépek kezelni. Vegye figyelembe, hogy minden a várt másodpercenkénti tranzakciókon (TPS) alapul, nem pedig a felhasználó szerint, mivel a használati minták eltérőek, és nem használhatók a terhelés előrejelzésére. A válaszok mérete és a háttéralkalmazás válaszideje alapján is lesznek különbségek – a nagyobb válaszméretek és a lassabb válaszidő alacsonyabb Maximális TPS-t eredményez. Azt is javasoljuk, hogy további gépek, hogy az elosztott terhelés a gépek között mindig elegendő puffert biztosít. Az extra kapacitás biztosítja, hogy magas rendelkezésre állás és rugalmasság.

|Cores|RAM|Várt késés (MS)-P99|Maximális TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\*Ez a gép egyéni beállítással emelt néhány alapértelmezett kapcsolati korlátot a .NET által ajánlott beállításokon túlra. Javasoljuk, hogy futtasson egy tesztet az alapértelmezett beállításokkal, mielőtt kapcsolatba lépne az ügyfélszolgálattal, hogy ezt a korlátot a bérlő számára módosítsák.

> [!NOTE]
> Nincs sok különbség a maximális TPS között 4, 8, és 16 mag gépek. A fő különbség ezek között a várható késés.
>
> Ez a táblázat az összekötő kondigép típusa alapján az összekötő várható teljesítményére is összpontosít. Ez elkülönül az alkalmazásproxy-szolgáltatás szabályozási korlátaitól, lásd: [Szolgáltatáskorlátok és korlátozások.](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions)

## <a name="security-and-networking"></a>Biztonság és hálózatépítés

Az összekötők bárhol telepíthetők a hálózaton, amely lehetővé teszi számukra, hogy kéréseket küldjenek az alkalmazásproxy szolgáltatásnak. A fontos, hogy az összekötőt futtató számítógép is hozzáfér az alkalmazásokhoz. Összekötők a vállalati hálózaton belül vagy egy virtuális gépen, amely fut a felhőben. Az összekötők futtathatók egy peremhálózaton, más néven demilitarizált zónán (DMZ), de ez nem szükséges, mert az összes forgalom kimenő, így a hálózat biztonsága továbbra is biztonságos marad.

Az összekötők csak kimenő kérelmeket küldenek. A kimenő forgalmat a rendszer elküldi az alkalmazásproxy-szolgáltatásnak és a közzétett alkalmazásoknak. Nem kell megnyitni a bejövő portokat, mert a forgalom mindkét irányban áramlik, ha egy munkamenet létrejött. A tűzfalakon keresztül immár bejövő hozzáférést sem kell konfigurálnia.

A kimenő tűzfalszabályok konfigurálásáról a [Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)című témakörben talál további információt.

## <a name="performance-and-scalability"></a>Teljesítmény és méretezhetőség

Az alkalmazásproxy-szolgáltatás méretezése transzparens, de az összekötők egyik tényezője. Elegendő csatlakozóval kell rendelkeznie a csúcsforgalom kezeléséhez. Mivel az összekötők állapot nélküliek, a felhasználók vagy a munkamenetek száma nem befolyásolja őket. Ehelyett a kérelmek számára és a hasznos teher méretére válaszolnak. A normál webes forgalom, egy átlagos gép képes kezelni egy pár ezer kérelmek másodpercenként. Az adott kapacitás a gép pontos jellemzőitől függ.

Az összekötő teljesítményét a processzor és a hálózat köti. A TLS-titkosításhoz és -visszafejtéshez processzorteljesítményre van szükség, a hálózatkezelés fontos az alkalmazások és az azure-beli online szolgáltatás gyors kapcsolatának leküzdése érdekében.

Ezzel szemben a memória kevésbé jelent problémát a csatlakozók esetében. Az online szolgáltatás gondoskodik a feldolgozás nagy részéről és az összes nem hitelesített forgalomról. Minden, amit a felhőben lehet megtenni, a felhőben történik.

Ha bármilyen okból, hogy az összekötő vagy a gép elérhetetlenné válik, a forgalom elindul a csoport egy másik összekötője. Ez a rugalmasság is ezért javasoljuk, hogy több csatlakozók.

A teljesítményt befolyásoló másik tényező az összekötők közötti hálózat minősége, beleértve a következőket:

- **Az online szolgáltatás:** Lassú vagy nagy késésű kapcsolatok az Azure-beli alkalmazásproxy szolgáltatás befolyásolja az összekötő teljesítményét. A legjobb teljesítmény érdekében csatlakoztassa a szervezetet az Azure-hoz az Express Route segítségével. Ellenkező esetben a hálózati csapat győződjön meg arról, hogy az Azure-ba való kapcsolatok kezelése a lehető leghatékonyabban.
- **A háttéralkalmazások:** Bizonyos esetekben további proxyk vannak az összekötő és a háttéralkalmazások között, amelyek lelassíthatják vagy megakadályozhatják a kapcsolatokat. A hibaelhárításhoz nyisson meg egy böngészőt az összekötő kiszolgálóról, és próbálja meg elérni az alkalmazást. Ha az összekötők az Azure-ban, de az alkalmazások a helyszíni, a tapasztalat nem lehet, amit a felhasználók várnak.
- **A tartományvezérlők**: Ha az összekötők egyszeri bejelentkezést (SSO) hajtanak végre a Kerberos korlátozott delegálásával, kapcsolatba lépnek a tartományvezérlővel, mielőtt a kérelmet a háttérrendszernek küldenék. Az összekötők Kerberos-jegyek gyorsítótárával rendelkeznek, de forgalmas környezetben a tartományvezérlők válaszkészsége befolyásolhatja a teljesítményt. Ez a probléma gyakoribb az Azure-ban futó, de a helyszíni tartományvezérlőkkel kommunikáló összekötők esetében.

A hálózat optimalizálásáról az Azure [Active Directory alkalmazásproxy használatakor a Hálózati topológia szempontjai](application-proxy-network-topology.md)című témakörben talál további információt.

## <a name="domain-joining"></a>Tartományhoz való csatlakozás

Az összekötők olyan számítógépen futtathatók, amely nem tartományhoz csatlakozik. Ha azonban azt szeretné, hogy az integrált Windows-hitelesítést (IWA) használó alkalmazások egyszeri bejelentkezéssel (SSO) jelentkezzenek ki, tartományhoz csatlakozó gépre van szükség. Ebben az esetben az összekötő gépeket olyan tartományhoz kell csatlakoztatni, amely képes [kerberos-korlátozott](https://web.mit.edu/kerberos) delegálást végrehajtani a közzétett alkalmazások felhasználói nak nevében.

Az összekötők részleges megbízhatósági kapcsolatban álló tartományokhoz vagy erdőkhöz, illetve írásvédett tartományvezérlőkhöz is csatlakoztathatók.

## <a name="connector-deployments-on-hardened-environments"></a>Összekötők telepítések megerősített környezetben

Általában összekötő központi telepítése egyszerű, és nem igényel speciális konfigurációt. Van azonban néhány egyedi feltétel, amelyet figyelembe kell venni:

- A kimenő forgalmat korlátozó szervezeteknek meg kell nyitniuk a [szükséges portokat.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment)
- Fips-kompatibilis gépek szükség lehet a konfiguráció megváltoztatására, hogy az összekötő folyamatok létrehozása és tárolása tanúsítványt.
- Azoknak a szervezeteknek, amelyek a hálózati kérelmeket kibocsátó folyamatok alapján zárolják a környezetüket, meg kell győződniük arról, hogy mindkét összekötő szolgáltatás engedélyezve van az összes szükséges port és IP eléréséhez.
- Bizonyos esetekben a kimenő továbbítási proxyk megszakíthatják a kétirányú tanúsítványhitelesítést, és a kommunikáció sikertelensítheti késedelmi beszerzését.

## <a name="connector-authentication"></a>Összekötő hitelesítése

A biztonságos szolgáltatás biztosításához az összekötőknek hitelesíteniük kell magukat a szolgáltatás felé, és a szolgáltatásnak az összekötő felé kell hitelesítenie magát. Ez a hitelesítés ügyfél- és kiszolgálói tanúsítványokkal történik, amikor az összekötők kezdeményezik a kapcsolatot. Így a rendszergazda felhasználóneve és jelszava nem tárolódik az összekötő gépen.

A használt tanúsítványok az alkalmazásproxy szolgáltatásra vonatkoznak. A kezdeti regisztráció során jönnek létre, és az összekötők néhány havonta automatikusan megújulnak.

Az első sikeres tanúsítványmegújítás után az Azure AD Application Proxy Connector szolgáltatás (Network Service) nem rendelkezik a régi tanúsítvány eltávolításához a helyi számítógép tárolójából. Ha a tanúsítvány lejárt, vagy a szolgáltatás már nem fogja használni, biztonságosan törölheti.

A tanúsítvány megújításával kapcsolatos problémák elkerülése érdekében győződjön meg arról, hogy az összekötő ről a [dokumentált célállomások](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-add-on-premises-application#prepare-your-on-premises-environment) felé irányuló hálózati kommunikáció engedélyezve van.

Ha egy összekötő több hónapig nem csatlakozik a szolgáltatáshoz, a tanúsítványok elavultak lehetnek. Ebben az esetben távolítsa el és telepítse újra az összekötőt a regisztráció elindításához. A következő PowerShell-parancsokat futtathatja:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```
A tanúsítvány ellenőrzéséről és a hibák elhárításáról a [Számítógép- és háttérösszetevők ellenőrzése az alkalmazásproxy megbízhatósági tanúsítványához című témakörben olvashat bővebben.](application-proxy-connector-installation-problem.md#verify-machine-and-backend-components-support-for-application-proxy-trust-certificate)

## <a name="under-the-hood"></a>technikai részletek

Az összekötők a Windows Server webalkalmazás-proxyn alapulnak, így a legtöbb kezelőeszközzel rendelkeznek, beleértve a Windows eseménynaplókat is

![Eseménynaplók kezelése az Eseménynaplóval](./media/application-proxy-connectors/event-view-window.png)

és a Windows teljesítményszámlálóit.

![Számlálók hozzáadása az összekötőhöz a Teljesítményfigyelővel](./media/application-proxy-connectors/performance-monitor.png)

Az összekötők rendszergazdai és munkamenet-naplókkal is rendelkeznek. A rendszergazdai naplók tartalmazzák a legfontosabb eseményeket és azok hibáit. A munkamenetnaplók tartalmazzák az összes tranzakciót és azok feldolgozási adatait.

A naplók megtekintéséhez nyissa meg az Eseménynaplót, nyissa meg a **Nézet** menüt, és engedélyezze **az Analitikus és hibakeresési naplók megjelenítése parancsot**. Ezután engedélyezze az események gyűjtésének megkezdését. Ezek a naplók nem jelennek meg a WebApplication Proxy rendszerben a Windows Server 2012 R2 rendszerben, mivel az összekötők egy újabb verzión alapulnak.

A szolgáltatás állapotát a Szolgáltatások ablakban vizsgálhatja meg. Az összekötő két Windows-szolgáltatásból áll: a tényleges összekötőből és a frissítőből. Mindkettőnek állandóan futnia kell.

 ![Példa: Szolgáltatások ablak, amely az Azure AD-szolgáltatásokat jeleníti meg helyi](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>További lépések

- [Alkalmazások közzététele különböző hálózatokon és helyeken összekötőcsoportok használatával](application-proxy-connector-groups.md)
- [Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)
- [Alkalmazásproxyval és csatlakozóval kapcsolatos hibák elhárítása](application-proxy-troubleshoot.md)
- [Az Azure AD alkalmazásproxy-összekötő csendes telepítése](application-proxy-register-connector-powershell.md)
