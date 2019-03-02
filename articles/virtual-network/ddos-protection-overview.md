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
ms.date: 10/13/2018
ms.author: jdial
ms.openlocfilehash: 3183ff5ca1b951e1a2f813c71ac52fef4ae17021
ms.sourcegitcommit: ad019f9b57c7f99652ee665b25b8fef5cd54054d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2019
ms.locfileid: "57245862"
---
# <a name="azure-ddos-protection-standard-overview"></a>Az Azure DDoS Protection Standard áttekintése

Az elosztott szolgáltatásmegtagadásos (DDoS-) támadások az egyik legnagyobb rendelkezésreállási és biztonsági kockázatot jelentik az olyan felhasználók számára, akik alkalmazásaikat a felhőbe helyezik át. A DDoS-támadások megpróbálják kimeríteni az alkalmazás erőforrásait, elérhetetlenné téve az alkalmazást a jogosult felhasználók számára. A DDoS-támadások bármilyen, az interneten keresztül nyilvánosan elérhető végpontot megcélozhatnak.

Az Azure DDoS protection alkalmazás tervezés – ajánlott eljárások, kombinálva DDoS-támadásokkal szembeni védelmet biztosít. Az Azure DDoS protection az alábbi szolgáltatási szinteken biztosít:

- **Alapszintű**: Az Azure platform részeként automatikusan engedélyezve van. Állandó forgalomfigyelést, és valós idejű csökkenti a hálózati szintű gyakori támadásoktól, adja meg a Microsoft online services által használt azonos védelem. A teljes méretezési csoport az Azure globális hálózatán használható, és csökkentheti a támadási forgalom régiók között elosztva. Védelmet biztosítanak az IPv4 és IPv6-alapú Azure [nyilvános IP-címek](virtual-network-public-ip-address.md).
- **Standard szintű**: Az alapszintű szolgáltatásszinten kifejezetten az Azure virtuális hálózati erőforrások hangolt további veszélyelhárítási szolgáltatásokat nyújt. DDoS Protection Standard az egyszerű, ha engedélyezni szeretné, és nem kell application módosítani. Az alkalmazásvédelmi szabályzatok hangolt dedikált forgalomfigyelést és gépi tanulási algoritmus segítségével. Társított erőforrások üzembe helyezett virtuális hálózatokon, például az Azure Load Balancer, az Azure Application Gateway és az Azure Service Fabric-példányok, nyilvános IP-szabályzatokkal, de ez a védelem az App Service Environment-környezetek nem vonatkozik. Valós idejű telemetriai támadások során, valamint a korábbi Azure Monitor nézetek érhető el. Gazdag támadási kockázatcsökkentési analytics diagnosztikai beállításokon keresztül érhetők el. Alkalmazásréteg-védelem használatával adhatók hozzá a [Azure Application Gateway webalkalmazásokhoz használható tűzfal](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) vagy a 3. fél tűzfal telepítése az Azure Marketplace-ről. Védelmet biztosítanak a IPv4 Azure [nyilvános IP-címek](virtual-network-public-ip-address.md).

![Az Azure DDoS Protection alapszintű vs. Standard](./media/ddos-protection-overview/ddoscomparison.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>Csökkenti a DDoS Protection standard szintű DDoS-támadások típusai

A DDoS Protection Standard csökkentheti a következő típusú támadások:

- **-Es támadások**: A támadás célja, hogy a hálózati réteg látszólag jogos forgalom jelentős mennyiségű kéréssekkel túlterhelheti. UDP árvizek, erősítési árvíz és más megtévesztésre csomag árvíz tartalmazza. A DDoS Protection Standard csökkenti a ezek több gigabájt potenciális támadások elemzésével és tisztítási őket, az Azure globális hálózati skálával automatikusan.
- **Protokoll támadások**: Ezeket a támadásokat jelennek meg a cél nem érhető el, egy a 3. rétegbeli a gyengeségét és a réteg 4 protokollvermet kiaknázásával. Ez magában foglalja, külön elárasztó támadások, támadások és más protokollt támadásoknak. A DDoS Protection Standard csökkenti ezeket a támadásokat, rosszindulatú és jogszerű forgalom által az ügyfél használatához, és blokkolja a rosszindulatú forgalom megkülönböztetése. 
- **Erőforrás (alkalmazás) réteg támadások**: Ezeket a támadásokat a céloznia webes alkalmazás csomagok, a gazdagépek közötti adatátvitel akadályozza. A támadások közé tartozik a HTTP protokoll megsértése, az SQL injektálási, webhelyek közötti szkriptelést és egyéb 7. rétegbeli támadásokat. Az Azure használata [Application Gateway webalkalmazási tűzfal](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), a DDoS Protection Standard, ezek a támadások elleni védelmet biztosít. Is vannak elérhető külső webes alkalmazás tűzfal ajánlatok a [Azure Marketplace-en](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

A DDoS Protection Standard virtual machines, a terheléselosztók és az application Gateway átjárók társított nyilvános IP-címek többek között a virtuális hálózatban lévő erőforrásokra védi. Az Application Gateway webalkalmazási tűzfal szolgáltatással párosítva, DDoS Protection Standard biztosíthatja teljes layer 3. rétegbeli 7 kockázatcsökkentési képességet.

## <a name="ddos-protection-standard-features"></a>A DDoS Protection standard szintű funkciók

![A DDoS-funkciók](./media/ddos-protection-overview/ddosfeatures.png)

A DDoS Protection Standard funkciók:

- **A platform natív integráció:** Natív módon integrálva az Azure-bA. Az Azure Portalon keresztül konfigurációit is beleértve. A DDoS Protection Standard tisztában van azzal erőforrásait és erőforrás-konfigurációhoz.
- **Kulcsrakész védelem:** Egyszerűsített konfiguráció azonnal, amint a DDoS Protection Standard engedélyezett védelmet biztosít egy virtuális hálózaton lévő összes erőforrást. Nincs beavatkozás vagy a felhasználó megadása nem kötelező. A DDoS Protection Standard azonnal és automatikusan csökkenti a támadási után a rendszer azt észlelte.
- **Mindig forgalomfigyelést:** Az alkalmazás forgalmi minták figyelhetők 24 óránként, naponta, hetente, 7 nap DDoS-támadások kijelzőjét keres. Kockázatcsökkentési alkalmazásvédelmi szabályzatok túllépése esetén történik.
- **Adaptív hangolás:** Intelligens adatforgalom-profilkészítés képes megtanulni az alkalmazás forgalmának idővel, és kiválasztja, és frissíti a profilt, amely a szolgáltatás a legmegfelelőbb. A profil módosítja, a forgalom változik az idő múlásával.
- **Többrétegű védelem:** Teljes verem DDoS elleni védelem, biztosít a webalkalmazás-tűzfal együtt használva.
- **Széles körű kockázatcsökkentési beosztás:** Több mint 60 különböző támadási típusok enyhíthető, globális kapacitással, a legnagyobb ismert DDoS-támadásokkal szembeni védelem érdekében.
- **A támadás analytics:** Támadás idején ötpercenként részletes jelentéseket, majd a támadás végén egy teljes összegzést kap. Stream kockázatcsökkentési Folyamatnaplók egy offline biztonsági információk és esemény-felügyelet (SIEM) rendszert a közel valós idejű figyelését a támadás során.
- **A támadás metrikák:** Minden támadástól összesített mérőszámok Azure monitoron keresztül érhetők el.
- **A támadás riasztás:** Riasztások konfigurálhatók a kezdő és a egy támadás leállítása és a támadás időtartama alatt a beépített támadási mérőszámok segítségével. Riasztások integrálható a működési szoftver a Microsoft Azure Monitor naplók, Splunk, Azure Storage, e-mailek és az Azure Portalon.
- **A Cost garancia:** Adatátvitel – és az alkalmazás horizontális felskálázás szolgáltatásokhoz biztosított kreditek a dokumentált DDoS-támadások.

## <a name="ddos-protection-standard-mitigation"></a>A DDoS Protection Standard kockázatcsökkentési

A DDoS Protection Standard tényleges forgalom kihasználtsági figyeli, és folyamatosan összevetett a a DDoS-házirendben meghatározott küszöbértékeket. A forgalom küszöbérték túllépésekor DDoS-támadás kockázatcsökkentése automatikusan kezdeményez. Amikor a forgalom a küszöbérték alá adja vissza, a kockázatcsökkentési törlődik.

![Kezelés](./media/ddos-protection-overview/mitigation.png)

Során kockázatcsökkentést a védett erőforrásokhoz irányuló adatforgalmat a rendszer átirányítja a DDoS protection szolgáltatás, és több ellenőrzés történik, mint például a következő ellenőrzések:

- Győződjön meg arról, csomagok felel meg az internet specifikációjának, és nem hibás formátumú.
- Annak megállapításához, hogy a forgalom potenciálisan identitáshamisításos csomagot az ügyfél kommunikálni (pl.: KÜLÖN hitelesítési vagy külön cookie-k vagy a forrás timeouts, hogy egy csomag elvetésével).
- Sebességkorlát-csomagokat, ha más kényszerítési metódus nem hajtható végre.

A DDoS protection a támadás forgalmat blokkol, és továbbítja a fennmaradó forgalmat a szándékolt cél. Támadások észlelése, néhány percen belül értesítést kap az Azure Monitor-metrikák használatával. A DDoS Protection normál telemetriai naplózásának konfigurálásával írhat jövőbeli elemzések a rendelkezésre álló beállítások a naplókat. Az Azure monitorban a DDoS Protection Standard metrikaadatok 30 napig őrződnek meg.

A Microsoft közzétegyék az általuk [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) hozhat létre egy felületet, ahol a DDoS Protection-kompatibilis nyilvános IP-címek szimulációkhoz forgalmát hozhat létre. A töréspont felhőalapú szimuláció lehetővé teszi:

- Ellenőrizze, hogy miként a Microsoft Azure DDoS Protection Standard védi az Azure-erőforrások DDoS-támadások ellen
- Az incidensmegoldási folyamatba, a DDoS-támadások optimalizálása
- A dokumentum a DDoS-megfelelőség
- A hálózati biztonsági csoportok betanítása

## <a name="next-steps"></a>További lépések

- [DDoS Protection-szabvány konfigurálása](manage-ddos-protection.md)
