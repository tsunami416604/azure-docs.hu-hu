---
title: Az Azure DDoS védelem szabványos áttekintése |} Microsoft Docs
description: További tudnivalók az Azure DDoS-védelem szolgáltatás.
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
ms.openlocfilehash: 705f69f9143e3d2b27a3099f340218aaa12931f8
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/19/2018
---
# <a name="azure-ddos-protection-standard-overview"></a>Az Azure szabványos DDoS-védelem áttekintése

Elosztott szolgáltatásmegtagadásos (DDoS-) támadások az ügyfelek számára, áthelyezi az alkalmazásaikat a felhőbe irányuló legnagyobb rendelkezésre állási és biztonsági szempontok közé tartoznak. DDoS-támadások megkísérli lefoglalhat egy alkalmazás-erőforrásokat, így az alkalmazás valódi felhasználók számára nem érhető el. DDoS-támadások tud célozható tetszőleges végpontot, amely nyilvánosan elérhető az interneten keresztül.

Az alkalmazás tervezési ajánlott eljárásokkal, Azure DDoS védelem DDoS-támadások elleni védelmet biztosít. Az Azure DDos-védelem a következő szolgáltatási szinteket tartalmazza:

- **Alapszintű**: automatikusan engedélyezve van, nem kell külön fizetni az Azure platform részeként. – A forgalom figyelése és a valós idejű javaslat közös hálózati szintű támadások, adja meg a Microsoft online services által használt azonos védelmet. Teljes méretű Azure globális hálózati támadás forgalom terjesztése és régiók közötti használható. Védelmet biztosítanak a IPv4 és IPv6-alapú Azure [nyilvános IP-címek](virtual-network-public-ip-address.md).
- **Standard**: további megoldás képességeket biztosít a alapvető szolgáltatási rétegben, amely kifejezetten az Azure virtuális hálózati erőforrások hangolt keresztül. DDoS védelem szabványos felettébb egyszerű engedélyezéséhez és a szükséges alkalmazás ne változzon. Adatvédelmi szabályzatok hangolt dedikált forgalom figyelése és a gépi tanulási algoritmus. Nyilvános IP-címek a virtuális hálózatokon, például az Azure Load Balancer, az Azure Application Gateway és az Azure Service Fabric-példányok üzembe helyezett erőforrás társított házirendek vonatkoznak. Valós idejű telemetriai érhető Azure figyelő nézetek, a támadás során, és az előzmények. Alkalmazásvédelem réteg segítségével is hozzáadhat a [Azure Application Gateway webalkalmazási tűzfal](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json). Védelmet biztosítanak a IPv4 Azure [nyilvános IP-címek](virtual-network-public-ip-address.md).

![Az Azure DDoS védelem Standard](./media/ddos-protection-overview/ddospic.png)

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>DDoS védelem szabványos csökkenti DDoS-támadások típusai

DDoS védelem szabványos csökkentheti a következő típusú támadások:

- **-Es támadások**: A támadás célja, hogy a hálózati réteg látszólag jogos forgalom jelentős mennyiségű kéréssekkel. Ez magában foglalja az UDP árvizek, erősítési árvizek és egyéb megtévesztésre csomag árvizek. DDoS védelem szabványos azzal csökkenti a többszörös gigabájt webtartalmak szűkítő és tisztítási őket, amely Azure globális hálózati, automatikusan.
- **Protokoll támadások**: ilyen támadások leképezési célja nem érhető el, a 3 rétegbeli és a 4. rétegbeli protokollverem egyik támadható gyenge pontja kihasználva. Ez magában foglalja, SZIN árvíz támadások, támadások és más protokoll támadásoknak. DDoS védelem szabványos azzal csökkenti az ilyen támadások, rosszindulatú és jogos forgalom által az ügyfél való interakció, és blokkolja a rosszindulatú forgalom megkülönböztetése. 
- **Erőforrás (alkalmazás) réteg támadások**: ilyen támadások cél webes alkalmazás csomagok, és megzavarhatják a gazdagépek közötti adatátvitel. A támadások lehetnek a HTTP protokoll megsértése, SQL injektálási, többhelyes parancsfájlok és egyéb réteg 7 támadások. Használja az Azure [Alkalmazásátjáró webalkalmazási tűzfal](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), a DDoS védelem Standard, az ilyen támadások elleni védelmet biztosít. Van még külső webes alkalmazás tűzfal ajánlatok érhető el a [Azure piactér](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall).

DDoS védelem szabványos védi többek között a virtuális gépek terheléselosztók és alkalmazásátjárót társított nyilvános IP-címek egy virtuális hálózatán lévő erőforrásokat. Az Alkalmazásátjáró webalkalmazási tűzfal alapján kialakulhat, amikor DDoS védelem szabványos biztosíthat teljes réteg 3. rétegbeli 7 csökkentése terén.

## <a name="ddos-protection-standard-features"></a>DDoS védelem szabványos szolgáltatások

![DDoS-funkciók](./media/ddos-protection-overview/ddosfeatures.png)

DDoS védelem szabványos szolgáltatások a következők:

- **Natív platformintegráció:** natív módon integrált Azure be. Az Azure portálon keresztül konfigurációs tartalmazza. DDoS védelem szabványos együttműködik az erőforrások és az erőforrás-konfigurációban.
- **Kulcsrakész védelmi:** egyszerűsített konfigurációs azonnal védelmet nyújt a virtuális hálózaton lévő összes erőforrást, amint DDoS védelem szabványos engedélyezett. Nincs beavatkozás vagy a felhasználó-definícióra szükség. DDoS védelem szabványos azonnal és automatikusan csökkenti a támadási után észlelhető.
- **Állandóan működő forgalomfigyelés:** az alkalmazás forgalmi minták figyelt 24 óránként, naponta, a hét, DDoS-támadások mutatók keres. Megoldás adatvédelmi szabályzatok túllépése esetén történik.
- **Adaptív hangolása:** intelligens forgalom profilkészítési tanulják meg az alkalmazás forgalom adott idő alatt, és kiválasztja, és frissíti a profilt, amely a szolgáltatás számára legalkalmasabb. A profil módosítja, a forgalom idővel változik.
- **3. rétegbeli réteg 7 védelem:** DDoS-védelem teljes verem biztosít a webalkalmazási tűzfal használata esetén.
- **Széles körű megoldás méretezési:** keresztül 60 különböző támadás típusok kivédhető, globális kapacitás, a legnagyobb ismert DDoS-támadások elleni védelem érdekében.
- **Támadás metrikák:** Summarized metrikák minden támadások elleni Azure figyelő keresztül érhetők el.
- **Támadás riasztási:** riasztások konfigurálhatók a kezdő- és az olyan támadások, állítsa le és a támadás időtartama alatt használja a beépített támadás metrikákat. Riasztások integrálja a működési szoftverek, mint a Microsoft Azure Naplóelemzés, Splunk, Azure Storage, e-mailek és az Azure-portálon.
- **Költség garancia:** adatátvitelt és alkalmazás kibővített szolgáltatási jóváírások dokumentált DDoS-támadások.

## <a name="ddos-protection-standard-mitigation"></a>DDoS védelem szabványos megoldás

DDoS védelem szabványos tényleges forgalom kihasználtsági figyeli, és folyamatosan összevetett a a DDoS házirendben meghatározott küszöbértékeket. A forgalom küszöbérték túllépésekor DDoS megoldás automatikusan megkezdődik. Ha a forgalom adja vissza, a küszöbérték alá, a megoldás törlődik.

![Kezelés](./media/ddos-protection-overview/mitigation.png)

Során csökkentése a védett erőforrásokhoz küldött forgalmat a rendszer átirányítja a DDoS védelem szolgáltatás, és számos a rendszer ellenőrzi, például a következő műveleteket:

- Győződjön meg arról, csomagok felel meg az internet specifikációjának, illetve nem hibás formátumú.
- Annak megállapításához, hogy a forgalom potenciálisan hamisított csomagot az ügyfél kommunikálni (pl.: SZIN Auth vagy SZIN cookie-k vagy újraküldésére az adatforrásra vonatkozóan egy csomag ejtésével).
- Sebességhatár csomagok, ha más érvényesítési metódus nem hajtható végre.

DDoS-védelem támadás forgalmat blokkolja, és továbbítja a fennmaradó forgalmat a kívánt rendeltetési. Észlelt támadás, néhány percen belül értesítést kap Azure figyelő metrikák használatával. DDoS védelem szabványos telemetriai bejelentkezés konfigurálásával írhat a naplók jövőbeli elemzéshez beállításokat. Az Azure-figyelő DDoS védelem szabvány metrikaadatokat 30 napig megmarad.

Microsoft rendelkezik közösen rendelkező [BreakingPoint felhő](https://www.ixiacom.com/products/breakingpoint-cloud) hozhat létre egy felület, ahol hozhat létre a forgalom DDoS-védelem engedélyezve nyilvános IP-címek szimulációja ellen. A töréspont felhő szimuláció teszi lehetővé:

- Ellenőrizze, hogy hogyan a Microsoft Azure DDoS védelem Standard védelmet nyújt az Azure-erőforrások DDoS-támadások
- Az incidensekre adott reakciók folyamat DDoS támadás optimalizálása
- A dokumentum DDoS megfelelőségi
- A hálózati biztonsági csoportok képzése

## <a name="next-steps"></a>További lépések

- [DDoS védelem Standard konfigurálása](manage-ddos-protection.md)