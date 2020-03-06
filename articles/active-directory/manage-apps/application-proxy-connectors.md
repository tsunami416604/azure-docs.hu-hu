---
title: Az Azure AD-alkalmazásproxy-összekötők ismertetése |} A Microsoft Docs
description: Az Azure AD-alkalmazásproxy összekötőit alapjait ismerteti.
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
ms.openlocfilehash: 1c2036bf9995725e4bbef44e4c039f8336eb81a0
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78375707"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Az Azure AD-alkalmazásproxy-összekötők ismertetése

Az összekötők olyan milyen Azure AD-alkalmazásproxy lehetővé teszi. Egyszerűen, egyszerűen üzembe helyezhetők és karbantarthatók, és rendkívül hatékonyak. Ez a cikk ismerteti, hogy mely összekötők, hogyan működnek, és néhány javaslatot az üzemelő példány optimalizálása a.

## <a name="what-is-an-application-proxy-connector"></a>Mi az Application Proxy connector?

Az összekötők olyan egyszerűsített ügynökök, amelyek a helyszínen található, és elősegítik a kimenő kapcsolat az alkalmazásproxy-szolgáltatás. Összekötők egy olyan Windows Serveren, amely hozzáfér a háttéralkalmazás telepítve kell lennie. Összekötők az egyes csoportok az egyes alkalmazások adatforgalmának összekötő csoportokba rendezhetők.

## <a name="requirements-and-deployment"></a>Követelmények és üzembe helyezés

Az alkalmazásproxy sikeres üzembe helyezéséhez legalább egy összekötő szükséges, de azt javasoljuk, hogy két vagy több, nagyobb rugalmasság. Telepítse az összekötőt egy Windows Server 2012 R2 vagy újabb rendszerű gépre. Az összekötőnek kommunikálnia kell az alkalmazásproxy szolgáltatással és a közzétenni kívánt helyszíni alkalmazásokkal.

### <a name="windows-server"></a>Windows server
A Windows Server 2012 R2 rendszerű kiszolgáló van szüksége, vagy később is telepíthető, amely az Application Proxy connector. A kiszolgálónak csatlakoznia kell az alkalmazásproxy-szolgáltatásokhoz az Azure-ban és a közzétenni kívánt helyszíni alkalmazásokhoz.

A windows server a TLS 1.2 engedélyezve van az alkalmazásproxy-összekötő telepítése előtt rendelkeznie kell. A TLS 1,2 engedélyezése a kiszolgálón:

1. Állítsa be a következő beállításkulcsokat:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

1. Kiszolgáló újraindítása

Az összekötő-kiszolgáló hálózati követelményeivel kapcsolatos további információkért lásd: az [alkalmazásproxy első lépései és az összekötő telepítése](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Karbantartás

Az összekötők és a szolgáltatás gondoskodik a magas rendelkezésre állású-feladatokat. Ezek hozzáadásának vagy eltávolításának dinamikusan is. Minden alkalommal, amikor új kérelem érkezik lesz irányítva a jelenleg elérhető összekötők egyikéhez. Ha egy összekötő átmenetileg nem érhető el, ez a forgalom nem válaszol.

Az összekötők állapot nélküli és konfigurációs adatok nem rendelkeznek a gépen. Kizárólag azokat az adatokat tárolnak a beállításokat a szolgáltatás és a hitelesítési tanúsítvány csatlakoztatására. A szolgáltatáshoz való csatlakozáskor, az összes szükséges konfigurációs adatok, és frissítse azt minden néhány perc alatt.

Összekötők is lekérdezi a kiszolgálót annak megállapításához, hogy az összekötő újabb verziója. Ha talál egyet, akkor az összekötők önmagukat frissítik.

Az összekötők a gépen, amelyen futnak az Eseménynapló és a teljesítményszámlálók használatával figyelheti. Vagy azok állapotát az Azure portal Application Proxy lapján tekintheti meg:

![Példa: Azure AD Application Proxy-összekötők](./media/application-proxy-connectors/app-proxy-connectors.png)

Nem, nem használt összekötők manuálisan törölni kell. Amikor fut egy összekötőt, addig marad aktív, csatlakozik a szolgáltatáshoz. A fel nem használt összekötők _inaktívként_ vannak megjelölve, és 10 napos inaktivitás után törlődnek. Ha szeretné távolítani egy összekötőt, távolítsa el az összekötő szolgáltatást és a frissítési szolgáltatást is a kiszolgálóról. Indítsa újra a számítógépet a szolgáltatás teljes eltávolításához.

## <a name="automatic-updates"></a>Automatikus frissítések

Az Azure AD összes telepített összekötő esetében az automatikus frissítések biztosít. Mindaddig, amíg a Alkalmazásproxyösszekötő szolgáltatás fut, automatikusan frissíti az összekötőkhöz. Ha nem látja a Connector Updater szolgáltatást a kiszolgálón, [újra kell telepítenie az összekötőt](application-proxy-add-on-premises-application.md) a frissítések beszerzéséhez.

Ha nem szeretné, hogy az automatikus frissítés megvárja az összekötőt, manuális frissítést végezhet. Nyissa meg az [összekötő letöltési lapját](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) azon a kiszolgálón, amelyen az összekötő található, és válassza a **Letöltés**lehetőséget. Ez a folyamat elindít egy frissítést a helyi összekötő.

A bérlők számára a több összekötőt az automatikus frissítések minden csoport a környezetében az állásidő elkerülése érdekében egyszerre egy összekötő célként.

Amikor frissíti az összekötőt, ha az állásidő fordulhatnak elő:
  
- Csak egy összekötővel rendelkezik, javasoljuk, hogy telepítsen egy második összekötőt, és [hozzon létre egy összekötő csoportot](application-proxy-connector-groups.md). Ezzel elkerülhető az állásidő, és magasabb rendelkezésre állást biztosít.  
- Egy összekötő történt egy tranzakció közepén a frissítés megkezdése. Bár a kezdeti tranzakció megszakad, a böngésző automatikusan érdemes újrapróbálkoznia a művelettel, vagy a lap frissítésével. Amikor a kérést a rendszer újraküldi, a forgalmat irányítja a rendszer egy biztonsági mentési összekötőt.

A korábban kiadott verziókról és a benne foglalt változásokról további információt az [alkalmazásproxy verziójának kiadási előzményei](application-proxy-release-version-history.md)című témakörben talál.

## <a name="creating-connector-groups"></a>Összekötő-csoportok létrehozása

Összekötőcsoportok engedélyezése rendelhet hozzá az egyedi összekötők adott alkalmazások kiszolgálására. Összekötőket csoportosíthatja, és rendelje hozzá a csoport mindegyik alkalmazás.

Összekötőcsoportok megkönnyítik a nagy méretű központi telepítések felügyeletéhez szükséges. Akkor is javíthatja késés a bérlők számára, amelyek különböző régiókban lévő üzemeltetett alkalmazások, mert csak a helyi alkalmazások kiszolgálására helyalapú összekötő csoportokat hozhat létre.

További információ az összekötő-csoportokról: [alkalmazások közzététele különálló hálózatokon és helyszíneken összekötő csoportok használatával](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Kapacitástervezés

Fontos, hogy gondoskodjon arról, hogy elegendő kapacitást tervezzen az összekötők között a várt forgalmi mennyiség kezeléséhez. Javasoljuk, hogy minden összekötő-csoportnak legalább két összekötője legyen, hogy magas rendelkezésre állást és méretezést biztosítson. Ha a három összekötő optimális, akkor előfordulhat, hogy egy gépet bármikor kell kiszolgálni.

Általánosságban elmondható, hogy minél több felhasználó van, annál nagyobb a gép, amelyre szüksége lesz. Alább látható egy táblázat, amely felvázolja a kötetet, és a várható késést különböző gépek képesek kezelni. Vegye figyelembe, hogy a másodpercenkénti várt tranzakciók (TPS) alapján, mivel a használati minták eltérnek, és nem használhatók a betöltés előrejelzésére. A válaszok méretétől és a háttérbeli alkalmazások válaszideje – nagyobb válaszokra és lassabb válaszidő-ra is – a rendszer alacsonyabb TPS eredményez. Azt javasoljuk továbbá, hogy további gépek legyenek, hogy az elosztott terhelés a gépek között mindig bőséges puffert biztosítson. A további kapacitás garantálja, hogy magas rendelkezésre állást és rugalmasságot biztosítson.

|Processzormagok|RAM|Várható késés (MS) – P99|Maximális TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* ez a gép egyéni beállítást használt, hogy a .NET által ajánlott beállításokon felül néhány alapértelmezett kapcsolódási korlátot felemeljen. Azt javasoljuk, hogy az alapértelmezett beállításokkal tesztjének futtatása előtt vegye fel a kapcsolatot a bérlőhöz tartozó módosítani ezt a korlátot az ügyfélszolgálathoz.

> [!NOTE]
> Mekkora a legnagyobb TPS 4, 8 és 16 mag gépek közötti különbség nem szerepel. Azokat a fő különbség a várható késés van.
>
> Ez a táblázat egy összekötő várt teljesítményére koncentrál, amely a telepített számítógép típusától függ. Ez eltér az alkalmazásproxy szolgáltatás szabályozási korlátaitól, lásd: [szolgáltatás korlátai és korlátozásai](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-service-limits-restrictions).

## <a name="security-and-networking"></a>Biztonsági és hálózatkezelési

Összekötők bárhol a hálózaton, amellyel kérést küld az alkalmazásproxy-szolgáltatás telepíthető. Fontos, hogy az összekötő is futtató számítógép rendelkezik-e a hozzáférés az alkalmazásokhoz. Az összekötők a vállalati hálózaton belül, vagy a felhőben futó virtuális gépre telepíthető. Az összekötők egy peremhálózati hálózaton, más néven vagy demilitarizált-zónában (DMZ) is futtathatók, de nem szükségesek, mert az összes forgalom kimenő, így a hálózat biztonságban marad.

Összekötők csak a kimenő kéréseket küld. A kimenő adatforgalom az alkalmazásproxy-szolgáltatás, és a közzétett alkalmazásokhoz. Nem kell bemenő portokat nyitni, mert a forgalom mindkét irányban keresztszűréssel egy munkamenet létrehozását követően. Emellett a tűzfalakon keresztül nem kell konfigurálnia a bejövő hozzáférést.

A kimenő tűzfalszabályok konfigurálásával kapcsolatos további információkért lásd: a [meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md).

## <a name="performance-and-scalability"></a>Teljesítmény és méretezhetőség

Az alkalmazásproxy-szolgáltatás nagy számú transzparens, de méretezési fontos tényező összekötők. Szüksége lesz elég összekötők túllépő forgalom kezelésére. Mivel az összekötők állapot nélküliek, nem befolyásolják a felhasználók vagy a munkamenetek számát. Ehelyett válaszoljanak kérések és a hasznos adatainak mérete. Szabványos webes forgalmat egy átlagos gép képes kezelni néhány ezer vonatkozó kérelmek másodpercenkénti száma. A megadott kapacitás gép pontos jellemzőitől függ.

Összekötő teljesítményét a CPU és a hálózat van kötve. CPU-teljesítményre van szükség az SSL-titkosítás és visszafejtés, hálózati fontos, hogy az Azure-ban az alkalmazások és az online szolgáltatás gyors kapcsolat beolvasása közben.

Ezzel szemben a memória mérete kisebb összekötőkkel kapcsolatos problémát. Az online szolgáltatás gondoskodik a feldolgozás jelentős részét, és minden hitelesítés nélküli forgalmat. Minden, a felhőben is elvégezhető a felhőben történik.

Ha bármilyen okból, hogy az összekötő vagy a gép elérhetetlenné válik, a forgalom indul el a csoport egy másik összekötőhöz is. Ez rugalmasságot miért javasoljuk, hogy több összekötőt is.

Teljesítményét befolyásoló másik tényező között az összekötők, többek között a hálózatkezelés minőségét:

- **Online szolgáltatás**: az Azure-ban az alkalmazásproxy szolgáltatás lassú vagy nagy késleltetésű kapcsolatai befolyásolják az összekötő teljesítményét. A legjobb teljesítmény érdekében csatlakozzon a szervezet Azure Express route-tal. Ellenkező esetben rendelkezik a hálózatkezelésért felelős csapat, győződjön meg arról, hogy a kapcsolatok az Azure-bA a lehető leghatékonyabb kezelik.
- **A háttérbeli alkalmazások**: bizonyos esetekben további proxyk vannak az összekötő és a háttérbeli alkalmazások között, amelyek lassú vagy meggátolják a kapcsolatokat. Ebben a forgatókönyvben a hiba elhárításához nyisson meg egy böngészőt, az összekötő-kiszolgálóról, és próbálja meg elérni az alkalmazást. Ha az Azure-ban futtatja az összekötők, de az alkalmazások a helyszínen, a tapasztalatok nem a felhasználók várható.
- **Tartományvezérlők**: Ha az összekötők egyszeri bejelentkezést (SSO) hajtanak végre a Kerberos által korlátozott delegálás használatával, a kérésnek a háttérbe való küldése előtt kapcsolatba léphetnek a tartományvezérlőkkel. Az összekötők rendelkezik egy Kerberos-jegyekhez gyorsítótárát, de foglalt környezetekben a tartományvezérlők válaszkészségét befolyásolhatja a teljesítményt. A probléma általában akkor használatos, az Azure-ban futtatva, de az kommunikálni a tartományvezérlők, amelyek a helyszíni összekötők esetében.

A hálózat optimalizálásával kapcsolatos további információkért tekintse meg a [hálózati topológia szempontjait a Azure Active Directory Application proxy használatakor](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Tartománycsatlakoztatás

Az összekötők olyan számítógépen, amelyen a nem tartományhoz csatlakoztatott futtathatja. Azonban ha szeretne egyszeri bejelentkezés (SSO) integrált Windows-hitelesítés (IWA) használó alkalmazások, akkor egy tartományhoz csatlakoztatott gép. Ebben az esetben az összekötő gépeket olyan tartományhoz kell csatlakoztatni, amely képes a felhasználók nevében [Kerberos](https://web.mit.edu/kerberos) által korlátozott delegálás végrehajtására a közzétett alkalmazásokhoz.

Összekötők is csatlakoztatható tartományokban vagy erdőkben találhatók, amelyek egy részleges megbízhatóságot, vagy az írásvédett tartományvezérlőre.

## <a name="connector-deployments-on-hardened-environments"></a>Összekötő telepítések a megerősített környezetben

Összekötő központi telepítés általában nagyon egyszerű, és nem igényel külön konfigurálást. Vannak azonban bizonyos egyedi feltételeket kell figyelembe venni:

- A kimenő forgalmat korlátozó szervezeteknek [meg kell nyitniuk a szükséges portokat](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
- A FIPS előírásainak megfelelő gépek szükség lehet módosítani a konfigurációt, hogy az összekötő folyamatokat létrehozni és menteni egy tanúsítványt.
- Győződjön meg arról, hogy mindkét összekötő-szolgáltatások engedélyezve vannak-e el az összes szükséges portok és IP-címek kell szervezeteknek, amelyek alapján a folyamatot, amelyet a hálózati kéréseket környezetükben zárolását.
- Bizonyos esetekben a kimenő előre proxyk felosztása a kétirányú Tanúsítványalapú hitelesítés, és hatására a kommunikáció meghiúsul.

## <a name="connector-authentication"></a>Összekötő-hitelesítés

Egy biztonságos szolgáltatást biztosít, összekötők kell a szolgáltatás felé hitelesíteni, és a szolgáltatás felé az összekötőt hitelesítésre. A hitelesítés történik, ha az összekötők kezdeményezzen kapcsolatot ügyfél és kiszolgáló-tanúsítványok használatával. Ezzel a módszerrel a rendszergazdai felhasználónevet és jelszót nem tárolja a csatlakozó számítógépen.

Az alkalmazásproxy-szolgáltatás használt tanúsítványok használhatók. Jön létre, a kezdeti regisztráció során, és a rendszer automatikusan megújítani az összekötők által minden néhány hónap során.

Ha több hónapon keresztül a szolgáltatás nem csatlakozik egy összekötőt, a tanúsítványok elavult lehet. Ebben az esetben távolítsa el, és telepítse újra az összekötő eseményindító-regisztrációt. A következő PowerShell-parancsok futtatásával is:

```
Import-module AppProxyPSModule
Register-AppProxyConnector
```

## <a name="under-the-hood"></a>Technikai részletek

Összekötők alapulnak a Windows Server a webalkalmazás-Proxy, úgy, hogy a legtöbb az azonos felügyeleti eszközökkel, például a Windows-eseménynaplók

![Az Eseménynapló-eseménynaplók](./media/application-proxy-connectors/event-view-window.png)

és Windows-teljesítményszámlálók.

![Az összekötő a Teljesítményfigyelő-számlálók hozzáadása](./media/application-proxy-connectors/performance-monitor.png)

Az összekötők rendelkezik rendszergazdai és a munkamenet naplókat. A felügyeleti naplók a fontos eseményeket és azok hibákat tartalmaznak. A munkamenet-naplók tartalmazzák a tranzakciók és a feldolgozási adataikat.

A naplók megtekintéséhez lépjen a Eseménynaplóra, nyissa meg a **nézet** menüt, és engedélyezze az **elemzési és hibakeresési naplók megjelenítése**lehetőséget. Ezt követően engedélyezze azokat, megkezdi az események gyűjtését. Ezek a naplók nem jelennek meg a webalkalmazás-Proxy a Windows Server 2012 R2 rendszerben, az összekötő újabb verziója alapulnak.

A szolgáltatás a szolgáltatások ablakban ellenőrizheti. Az összekötő két Windows-szolgáltatásból áll: a tényleges összekötőből és a updaterből. Mindkettő folyamatosan futnia kell.

 ![Példa: szolgáltatások ablak, amelyen az Azure AD Services helyi](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>Következő lépések

- [Alkalmazások közzététele különálló hálózatokon és helyszíneken összekötő csoportok használatával](application-proxy-connector-groups.md)
- [Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)
- [Alkalmazásproxy-és összekötő-hibák elhárítása](application-proxy-troubleshoot.md)
- [Az Azure AD Application Proxy-összekötő csendes telepítése](application-proxy-register-connector-powershell.md)
