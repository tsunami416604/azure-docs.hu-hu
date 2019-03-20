---
title: Az Azure AD-alkalmazásproxy-összekötők ismertetése |} A Microsoft Docs
description: Az Azure AD-alkalmazásproxy összekötőit alapjait ismerteti.
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 11/15/2018
ms.author: celested
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4f96a67fecead65af28a975267590ebb08c715d3
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57853008"
---
# <a name="understand-azure-ad-application-proxy-connectors"></a>Az Azure AD-alkalmazásproxy-összekötők ismertetése

Az összekötők olyan milyen Azure AD-alkalmazásproxy lehetővé teszi. Azok az egyszerű, könnyen üzembe helyezése és karbantartása és nagyon hatékony. Ez a cikk ismerteti, hogy mely összekötők, hogyan működnek, és néhány javaslatot az üzemelő példány optimalizálása a. 

## <a name="what-is-an-application-proxy-connector"></a>Mi az Application Proxy connector?

Az összekötők olyan egyszerűsített ügynökök, amelyek a helyszínen található, és elősegítik a kimenő kapcsolat az alkalmazásproxy-szolgáltatás. Összekötők egy olyan Windows Serveren, amely hozzáfér a háttéralkalmazás telepítve kell lennie. Összekötők az egyes csoportok az egyes alkalmazások adatforgalmának összekötő csoportokba rendezhetők.

## <a name="requirements-and-deployment"></a>Követelmények és üzembe helyezés

Az alkalmazásproxy sikeres üzembe helyezéséhez legalább egy összekötő szükséges, de azt javasoljuk, hogy két vagy több, nagyobb rugalmasság. Telepítse a Windows Server 2012 R2 vagy 2016 gépen. Az összekötő és az alkalmazásproxy-szolgáltatás, és közzéteszi a helyszíni alkalmazások közötti kommunikációhoz szükséges. 

### <a name="windows-server"></a>Windows server
A Windows Server 2012 R2 rendszerű kiszolgáló van szüksége, vagy később is telepíthető, amely az Application Proxy connector. A kiszolgáló csatlakozni az Azure-ban az alkalmazásproxy-szolgáltatásokat, és a helyszíni alkalmazásokat, amelyek az Ön közzétételi kell.

A windows server a TLS 1.2 engedélyezve van az alkalmazásproxy-összekötő telepítése előtt rendelkeznie kell. 1.5.612.0 régebbi verziót a meglévő összekötők továbbra is a korábbi verziói a TLS további értesítésig működni fog. A TLS 1.2 engedélyezése:

1. Állítsa be a következő beállításkulcsokat:
    
    ```
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
    [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319] "SchUseStrongCrypto"=dword:00000001
    ```

2. Indítsa újra a kiszolgálót


Az összekötő kiszolgáló hálózati követelményeivel kapcsolatos további információkért lásd: [alkalmazásproxy – első lépések, és telepítheti az összekötőt](application-proxy-add-on-premises-application.md).

## <a name="maintenance"></a>Karbantartás
Az összekötők és a szolgáltatás gondoskodik a magas rendelkezésre állású-feladatokat. Ezek hozzáadásának vagy eltávolításának dinamikusan is. Minden alkalommal, amikor új kérelem érkezik lesz irányítva a jelenleg elérhető összekötők egyikéhez. Ha egy összekötő átmenetileg nem érhető el, ez a forgalom nem válaszol.

Az összekötők állapot nélküli és konfigurációs adatok nem rendelkeznek a gépen. Kizárólag azokat az adatokat tárolnak a beállításokat a szolgáltatás és a hitelesítési tanúsítvány csatlakoztatására. A szolgáltatáshoz való csatlakozáskor, az összes szükséges konfigurációs adatok, és frissítse azt minden néhány perc alatt.

Összekötők is lekérdezi a kiszolgálót annak megállapításához, hogy az összekötő újabb verziója. Ha talál egyet, akkor az összekötők önmagukat frissítik.

Az összekötők a gépen, amelyen futnak az Eseménynapló és a teljesítményszámlálók használatával figyelheti. Vagy azok állapotát az Azure portal Application Proxy lapján tekintheti meg:

 ![Azure ad alkalmazásproxy-összekötők](./media/application-proxy-connectors/app-proxy-connectors.png)

Nem, nem használt összekötők manuálisan törölni kell. Amikor fut egy összekötőt, addig marad aktív, csatlakozik a szolgáltatáshoz. A fel nem használt összekötők címkével _inaktív_ és 10 nap inaktivitás után törlődnek. Ha szeretné távolítani egy összekötőt, távolítsa el az összekötő szolgáltatást és a frissítési szolgáltatást is a kiszolgálóról. Indítsa újra a számítógépet a szolgáltatás teljes eltávolításához.

## <a name="automatic-updates"></a>Automatikus frissítések

Az Azure AD összes telepített összekötő esetében az automatikus frissítések biztosít. Mindaddig, amíg a Alkalmazásproxyösszekötő szolgáltatás fut, automatikusan frissíti az összekötőkhöz. Ha nem látja a összekötő frissítési szolgáltatást a kiszolgálón, kell [telepítse újra az összekötő](application-proxy-add-on-premises-application.md) bármely frissítéseket. 

Ha nem szeretné, várjon, amíg az összekötő való automatikus frissítését, kézi frissítés teheti meg. Nyissa meg a [összekötő letöltési oldal](https://download.msappproxy.net/subscription/d3c8b69d-6bf7-42be-a529-3fe9c2e70c90/connector/download) a kiszolgálókra, ahol az összekötő található, és válassza ki a **letöltése**. Ez a folyamat elindít egy frissítést a helyi összekötő. 

A bérlők számára a több összekötőt az automatikus frissítések minden csoport a környezetében az állásidő elkerülése érdekében egyszerre egy összekötő célként. 

Amikor frissíti az összekötőt, ha az állásidő fordulhatnak elő:  
- Csak egy összekötőt rendelkezik javasoljuk, hogy telepít egy második összekötőt és [hozzon létre egy összekötőcsoporthoz](application-proxy-connector-groups.md). Ezzel elkerülheti a kritikus és magasabb rendelkezésre állást biztosít.  
- Egy összekötő történt egy tranzakció közepén a frissítés megkezdése. Bár a kezdeti tranzakció megszakad, a böngésző automatikusan érdemes újrapróbálkoznia a művelettel, vagy a lap frissítésével. Amikor a kérést a rendszer újraküldi, a forgalmat irányítja a rendszer egy biztonsági mentési összekötőt.

## <a name="creating-connector-groups"></a>Összekötő-csoportok létrehozása

Összekötőcsoportok engedélyezése rendelhet hozzá az egyedi összekötők adott alkalmazások kiszolgálására. Összekötőket csoportosíthatja, és rendelje hozzá a csoport mindegyik alkalmazás. 

Összekötőcsoportok megkönnyítik a nagy méretű központi telepítések felügyeletéhez szükséges. Akkor is javíthatja késés a bérlők számára, amelyek különböző régiókban lévő üzemeltetett alkalmazások, mert csak a helyi alkalmazások kiszolgálására helyalapú összekötő csoportokat hozhat létre. 

Összekötőcsoportok kapcsolatos további információkért lásd: [külön hálózatok és helyek összekötőcsoportok használatával az alkalmazások közzététele](application-proxy-connector-groups.md).

## <a name="capacity-planning"></a>Kapacitástervezés 

Fontos, hogy elegendő a kapacitása kezelni a várt forgalom mennyisége összekötők között megtervezése. Általános, annál több felhasználó rendelkezik, a nagyobb egy gépre lesz szüksége. Az alábbi, egy táblát, amely röviden ismerteti a kötetet a különböző gépek képes kezelni. Vegye figyelembe az összes-alapú a várt tranzakció egy második (TPS) helyett felhasználó óta használati minták eltérőek lehetnek, és terhelés előrejelzése nem használható. Is lesz a válaszok és a háttérrendszer alkalmazás válaszideje alapján különbségeket – válasz nagyobb méretek és a lassabb válaszidőket egy alacsonyabb maximális TPS eredményez. Azt javasoljuk, hogy további gépeket úgy, hogy a gépek között a elosztott terhelés körülbelül 50 %. A további kapacitást biztosítja, hogy magas rendelkezésre állás és rugalmasság.

|Processzormagok|RAM|Várható késés (MS) – P99|Maximális TPS|
| ----- | ----- | ----- | ----- |
|2|8|325|586|
|4|16|320|1150|
|8|32|270|1190|
|16|64|245|1200*|

\* Ez a gép egy egyéni beállítás segítségével emelni néhányat az alapértelmezett kapcsolat korlátai túl az ajánlott beállítások .NET. Azt javasoljuk, hogy az alapértelmezett beállításokkal tesztjének futtatása előtt vegye fel a kapcsolatot a bérlőhöz tartozó módosítani ezt a korlátot az ügyfélszolgálathoz.
 
>[!NOTE]
>Mekkora a legnagyobb TPS 4, 8 és 16 mag gépek közötti különbség nem szerepel. Azokat a fő különbség a várható késés van.  

## <a name="security-and-networking"></a>Biztonsági és hálózatkezelési

Összekötők bárhol a hálózaton, amellyel kérést küld az alkalmazásproxy-szolgáltatás telepíthető. Fontos, hogy az összekötő is futtató számítógép rendelkezik-e a hozzáférés az alkalmazásokhoz. Az összekötők a vállalati hálózaton belül, vagy a felhőben futó virtuális gépre telepíthető. Az összekötők a demilitarizált zónában (DMZ) futtatható, de már nem szükséges, mert az összes forgalom akkor kimenő forgalomról beszélünk, így a hálózat biztonságának megőrzéséről.

Összekötők csak a kimenő kéréseket küld. A kimenő adatforgalom az alkalmazásproxy-szolgáltatás, és a közzétett alkalmazásokhoz. Nem kell bemenő portokat nyitni, mert a forgalom mindkét irányban keresztszűréssel egy munkamenet létrehozását követően. Emellett nem kell konfigurálni a bejövő hozzáférést a tűzfalon keresztül. 

Kimenő tűzfalszabályok konfigurálásával kapcsolatos további információkért lásd: [együttműködnek a meglévő helyszíni proxykiszolgálók](application-proxy-configure-connectors-with-proxy-servers.md).


## <a name="performance-and-scalability"></a>Teljesítmény és méretezhetőség

Az alkalmazásproxy-szolgáltatás nagy számú transzparens, de méretezési fontos tényező összekötők. Szüksége lesz elég összekötők túllépő forgalom kezelésére. Mivel az összekötők állapot nélküli, nem vonatkoznak rájuk a felhasználók és munkamenetek számát. Ehelyett válaszoljanak kérések és a hasznos adatainak mérete. Szabványos webes forgalmat egy átlagos gép képes kezelni néhány ezer vonatkozó kérelmek másodpercenkénti száma. A megadott kapacitás gép pontos jellemzőitől függ. 

Összekötő teljesítményét a CPU és a hálózat van kötve. CPU-teljesítményre van szükség az SSL-titkosítás és visszafejtés, hálózati fontos, hogy az Azure-ban az alkalmazások és az online szolgáltatás gyors kapcsolat beolvasása közben.

Ezzel szemben a memória mérete kisebb összekötőkkel kapcsolatos problémát. Az online szolgáltatás gondoskodik a feldolgozás jelentős részét, és minden hitelesítés nélküli forgalmat. Minden, a felhőben is elvégezhető a felhőben történik. 

Ha bármilyen okból, hogy az összekötő vagy a gép elérhetetlenné válik, a forgalom indul el a csoport egy másik összekötőhöz is. Ez rugalmasságot miért javasoljuk, hogy több összekötőt is.

Teljesítményét befolyásoló másik tényező között az összekötők, többek között a hálózatkezelés minőségét: 

* **Az online szolgáltatás**: Az alkalmazásproxy-szolgáltatás az Azure-ban a lassú vagy a nagy késésű kapcsolatok befolyásolhatja az összekötők teljesítménye. A legjobb teljesítmény érdekében csatlakozzon a szervezet Azure Express route-tal. Ellenkező esetben rendelkezik a hálózatkezelésért felelős csapat, győződjön meg arról, hogy a kapcsolatok az Azure-bA a lehető leghatékonyabb kezelik. 
* **A háttéralkalmazásokhoz**: Bizonyos esetekben vannak további proxykat, az összekötő és a háttérmodul-alkalmazásokkal, amelyek lassú vagy megakadályozzák a kapcsolatok között. Ebben a forgatókönyvben a hiba elhárításához nyisson meg egy böngészőt, az összekötő-kiszolgálóról, és próbálja meg elérni az alkalmazást. Ha az Azure-ban futtatja az összekötők, de az alkalmazások a helyszínen, a tapasztalatok nem a felhasználók várható.
* **A tartományvezérlők**: Az összekötők egyszeri bejelentkezés (SSO) a Kerberos által korlátozott delegálás használatával hajtsa végre, ha azok a tartományvezérlőkhöz, a háttérkiszolgáló a kérelem elküldése előtt. Az összekötők rendelkezik egy Kerberos-jegyekhez gyorsítótárát, de foglalt környezetekben a tartományvezérlők válaszkészségét befolyásolhatja a teljesítményt. A probléma általában akkor használatos, az Azure-ban futtatva, de az kommunikálni a tartományvezérlők, amelyek a helyszíni összekötők esetében. 

A hálózat optimalizálása kapcsolatos további információkért lásd: [Azure Active Directory Application Proxy használata esetén a hálózati topológiai szempontok a](application-proxy-network-topology.md).

## <a name="domain-joining"></a>Tartománycsatlakoztatás

Az összekötők olyan számítógépen, amelyen a nem tartományhoz csatlakoztatott futtathatja. Azonban ha szeretne egyszeri bejelentkezés (SSO) integrált Windows-hitelesítés (IWA) használó alkalmazások, akkor egy tartományhoz csatlakoztatott gép. Ebben az esetben az összekötő gépek csatlakozniuk kell egy tartományhoz, amely hajthat végre [Kerberos](https://web.mit.edu/kerberos) által korlátozott delegálás a közzétett alkalmazások a felhasználók nevében.

Összekötők is csatlakoztatható tartományokban vagy erdőkben találhatók, amelyek egy részleges megbízhatóságot, vagy az írásvédett tartományvezérlőre.

## <a name="connector-deployments-on-hardened-environments"></a>Összekötő telepítések a megerősített környezetben

Összekötő központi telepítés általában nagyon egyszerű, és nem igényel külön konfigurálást. Vannak azonban bizonyos egyedi feltételeket kell figyelembe venni:

* Szervezetek számára, amelyek a kimenő forgalmat korlátozni kell [nyissa meg a szükséges portok](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment).
* A FIPS előírásainak megfelelő gépek szükség lehet módosítani a konfigurációt, hogy az összekötő folyamatokat létrehozni és menteni egy tanúsítványt.
* Győződjön meg arról, hogy mindkét összekötő-szolgáltatások engedélyezve vannak-e el az összes szükséges portok és IP-címek kell szervezeteknek, amelyek alapján a folyamatot, amelyet a hálózati kéréseket környezetükben zárolását.
* Bizonyos esetekben a kimenő előre proxyk felosztása a kétirányú Tanúsítványalapú hitelesítés, és hatására a kommunikáció meghiúsul.

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

A naplók megtekintéséhez nyissa meg az eseménynaplót, nyissa meg a **nézet** menüt, és engedélyezze **megjelenítése elemzési és hibakeresési naplók**. Ezt követően engedélyezze azokat, megkezdi az események gyűjtését. Ezek a naplók nem jelennek meg a webalkalmazás-Proxy a Windows Server 2012 R2 rendszerben, az összekötő újabb verziója alapulnak.

A szolgáltatás a szolgáltatások ablakban ellenőrizheti. Az összekötő két Windows-szolgáltatás épül fel: a tényleges összekötő, és megtekinti a. Mindkettő folyamatosan futnia kell.

 ![Azure ad-szolgáltatások helyi](./media/application-proxy-connectors/aad-connector-services.png)

## <a name="next-steps"></a>További lépések


* [Külön hálózatok és helyek összekötőcsoportok használatával az alkalmazások közzététele](application-proxy-connector-groups.md)
* [Meglévő helyszíni proxykiszolgálók használata](application-proxy-configure-connectors-with-proxy-servers.md)
* [Proxy és az összekötő kapcsolatos hibák elhárítása](application-proxy-troubleshoot.md)
* [Az Azure AD alkalmazásproxy-összekötő csendes telepítése](application-proxy-register-connector-powershell.md)

