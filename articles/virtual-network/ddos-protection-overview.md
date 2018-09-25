---
title: Az Azure DDoS Protection Standard – áttekintés |} A Microsoft Docs
description: Ismerje meg az Azure DDoS Protection szolgáltatás.
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/29/2018
ms.author: jdial
ms.openlocfilehash: f25da8c1eedc31209a67ae05aef9dded45b706e0
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46962402"
---
# <a name="azure-ddos-protection-standard-overview"></a>Az Azure DDoS Protection Standard áttekintése

Az elosztott szolgáltatásmegtagadási (DDoS-) támadásokat néhány a legnagyobb rendelkezésre állással és biztonsággal kapcsolatos aggályokat rendelkező ügyfelek számára a saját alkalmazásait a felhőbe áthelyezni. A DDoS-támadások próbál lefoglalhat egy alkalmazás-erőforrások, így az alkalmazás nem érhető el a jogosult felhasználók számára. DDoS-támadások célozhatják bármely, amely az interneten keresztül nyilvánosan elérhető végponton.

Az Azure DDoS protection alkalmazás tervezés – ajánlott eljárások, kombinálva DDoS-támadásokkal szembeni védelmet biztosít. Az Azure DDoS protection az alábbi szolgáltatási szinteken biztosít:

- **Alapszintű**: automatikusan engedélyezve van az Azure platform részeként. Állandó forgalomfigyelést, és valós idejű csökkenti a hálózati szintű gyakori támadásoktól, adja meg a Microsoft online services által használt azonos védelem. A teljes méretezési csoport az Azure globális hálózatán használható, és csökkentheti a támadási forgalom régiók között elosztva. Védelmet biztosítanak az IPv4 és IPv6-alapú Azure [nyilvános IP-címek](virtual-network-public-ip-address.md).
- **Standard szintű**: az alapszintű szolgáltatásszinten kifejezetten az Azure virtuális hálózati erőforrások hangolt keresztül további veszélyelhárítási szolgáltatásokat biztosít. DDoS Protection Standard az egyszerű, ha engedélyezni szeretné, és nem kell application módosítani. Az alkalmazásvédelmi szabályzatok hangolt dedikált forgalomfigyelést és gépi tanulási algoritmus segítségével. Nyilvános IP-címek a virtuális hálózathoz, például az Azure Load Balancer, az Azure Application Gateway és az Azure Service Fabric-ban üzembe helyezett erőforrásokhoz kapcsolódó szabályzatok alkalmazandók. Valós idejű telemetriai támadások során, valamint a korábbi Azure Monitor nézetek érhető el. Gazdag támadási kockázatcsökkentési analytics diagnosztikai beállításokon keresztül érhetők el. Alkalmazásréteg-védelem használatával adhatók hozzá a [Azure Application Gateway webalkalmazásokhoz használható tűzfal](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy a 3. fél tűzfal telepítése az Azure Marketplace-ről. Védelmet biztosítanak a IPv4 Azure [nyilvános IP-címek](virtual-network-public-ip-address.md).

![Az Azure DDoS Protection alapszintű vs. Standard](./media/ddos-protection-overview/ddoscomparison.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Csökkenti a DDoS Protection standard szintű DDoS-támadások típusai

A DDoS Protection Standard csökkentheti a következő típusú támadások:

- **-Es támadások**: A támadás célja, hogy a hálózati réteg látszólag jogos forgalom jelentős mennyiségű kéréssekkel túlterhelheti. UDP árvizek, erősítési árvíz és más megtévesztésre csomag árvíz tartalmazza. A DDoS Protection Standard csökkenti a ezek több gigabájt potenciális támadások elemzésével és tisztítási őket, az Azure globális hálózati skálával automatikusan.
- **Protokoll támadások**: ezeket a támadásokat jelennek meg a cél nem érhető el, a 3. rétegbeli és a 4. réteg protokollvermet egy gyengeségét kiaknázásával. Ez magában foglalja, külön elárasztó támadások, támadások és más protokollt támadásoknak. A DDoS Protection Standard csökkenti ezeket a támadásokat, rosszindulatú és jogszerű forgalom által az ügyfél használatához, és blokkolja a rosszindulatú forgalom megkülönböztetése. 
- **Erőforrás (alkalmazás) réteg támadások**: ezeket a támadásokat cél webes alkalmazás csomagok, a gazdagépek közötti adatátvitel akadályozza. A támadások közé tartozik a HTTP protokoll megsértése, az SQL injektálási, webhelyek közötti szkriptelést és egyéb 7. rétegbeli támadásokat. Az Azure használata [Application Gateway webalkalmazási tűzfal](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), a DDoS Protection Standard, ezek a támadások elleni védelmet biztosít. Is vannak elérhető külső webes alkalmazás tűzfal ajánlatok a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

A DDoS Protection Standard virtual machines, a terheléselosztók és az application Gateway átjárók társított nyilvános IP-címek többek között a virtuális hálózatban lévő erőforrásokra védi. Az Application Gateway webalkalmazási tűzfal szolgáltatással párosítva, DDoS Protection Standard biztosíthatja teljes layer 3. rétegbeli 7 kockázatcsökkentési képességet.

## <a name="ddos-protection-standard-features"></a>A DDoS Protection standard szintű funkciók

![A DDoS-funkciók](./media/ddos-protection-overview/ddosfeatures.png)

A DDoS Protection Standard funkciók:

- **Natív platformintegráció:** natív módon integrált Azure-bA. Az Azure Portalon keresztül konfigurációit is beleértve. A DDoS Protection Standard tisztában van azzal erőforrásait és erőforrás-konfigurációhoz.
- **Kulcsrakész védelem:** egyszerűsített konfiguráció azonnal védelmet biztosít egy virtuális hálózaton lévő összes erőforrást, amint a DDoS Protection Standard van engedélyezve. Nincs beavatkozás vagy a felhasználó megadása nem kötelező. A DDoS Protection Standard azonnal és automatikusan csökkenti a támadási után a rendszer azt észlelte.
- **Mindig forgalomfigyelést:** az alkalmazás forgalmi mintázatait figyelt 24 óránként, naponta, a hét, DDoS-támadások kijelzőjét keres. Kockázatcsökkentési alkalmazásvédelmi szabályzatok túllépése esetén történik.
- **Adaptív hangolás:** intelligens adatforgalom-profilkészítés képes megtanulni az alkalmazás forgalmának idővel, és kiválasztja, és frissíti a profilt, amely a szolgáltatás a legmegfelelőbb. A profil módosítja, a forgalom változik az idő múlásával.
- **Többrétegű védelem:** teljes verem DDoS elleni védelem, biztosít egy webalkalmazási tűzfallal rendelkező használatakor.
- **Széles körű kockázatcsökkentési méretezési:** 60 feletti különböző támadási típusok enyhíthető, globális kapacitással, a legnagyobb ismert DDoS-támadásokkal szembeni védelem érdekében.
- **Analytics támadási:** részletes jelentések lekérése öt perces egységekben támadás és részletes összefoglalását, a támadás leteltével. Stream kockázatcsökkentési Folyamatnaplók egy offline biztonsági információk és esemény-felügyelet (SIEM) rendszert a közel valós idejű figyelését a támadás során.
- **Metrikák támadási:** minden támadástól Summarized metrikák érhetők el Azure monitoron keresztül.
- **A támadás riasztási:** riasztások konfigurálhatók a kezdő és a egy támadás leállítása és a támadás időtartama alatt a beépített támadási mérőszámok segítségével. Riasztások integrálható a működési szoftver a Microsoft Azure Log Analytics, Splunk, Azure Storage, e-mailek és az Azure Portalon.
- **A Cost garancia:** adatátviteli és az alkalmazás horizontális felskálázás szolgáltatásokhoz biztosított kreditek a dokumentált DDoS-támadások.

## <a name="ddos-protection-standard-mitigation"></a>A DDoS Protection Standard kockázatcsökkentési

A DDoS Protection Standard tényleges forgalom kihasználtsági figyeli, és folyamatosan összevetett a a DDoS-házirendben meghatározott küszöbértékeket. A forgalom küszöbérték túllépésekor DDoS-támadás kockázatcsökkentése automatikusan kezdeményez. Amikor a forgalom a küszöbérték alá adja vissza, a kockázatcsökkentési törlődik.

![Kezelés](./media/ddos-protection-overview/mitigation.png)

Során kockázatcsökkentést a védett erőforrásokhoz irányuló adatforgalmat a rendszer átirányítja a DDoS protection szolgáltatás, és több ellenőrzés történik, mint például a következő ellenőrzések:

- Győződjön meg arról, csomagok felel meg az internet specifikációjának, és nem hibás formátumú.
- Annak megállapításához, hogy a forgalom potenciálisan identitáshamisításos csomagot az ügyfél kommunikálni (pl.: SZIN hitelesítéssel vagy külön cookie-k vagy eldobja a csomagot timeouts, hogy a forrás).
- Sebességkorlát-csomagokat, ha más kényszerítési metódus nem hajtható végre.

A DDoS protection a támadás forgalmat blokkol, és továbbítja a fennmaradó forgalmat a szándékolt cél. Támadások észlelése, néhány percen belül értesítést kap az Azure Monitor-metrikák használatával. A DDoS Protection normál telemetriai naplózásának konfigurálásával írhat jövőbeli elemzések a rendelkezésre álló beállítások a naplókat. Az Azure monitorban a DDoS Protection Standard metrikaadatok 30 napig őrződnek meg.

A Microsoft közzétegyék az általuk [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) hozhat létre egy felületet, ahol a DDoS Protection-kompatibilis nyilvános IP-címek szimulációkhoz forgalmát hozhat létre. A töréspont felhőalapú szimuláció lehetővé teszi:

- Ellenőrizze, hogy miként a Microsoft Azure DDoS Protection Standard védi az Azure-erőforrások DDoS-támadások ellen
- Az incidensmegoldási folyamatba, a DDoS-támadások optimalizálása
- A dokumentum a DDoS-megfelelőség
- A hálózati biztonsági csoportok betanítása

## <a name="next-steps"></a>További lépések

- [DDoS Protection-szabvány konfigurálása](manage-ddos-protection.md)
