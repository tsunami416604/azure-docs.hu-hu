---
title: "Az Azure DDoS védelem szabványos áttekintése |} Microsoft Docs"
description: "További tudnivalók az Azure DDoS-védelem szolgáltatás."
services: virtual-network
documentationcenter: na
author: jimdial
manager: jeconnoc
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/13/2017
ms.author: jdial
ms.openlocfilehash: 6b15be022ba3b8373cfb852be8fc6915824801dc
ms.sourcegitcommit: e266df9f97d04acfc4a843770fadfd8edf4fa2b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="azure-ddos-protection-standard-overview"></a>Az Azure szabványos DDoS-védelem áttekintése

Elosztott szolgáltatásmegtagadásos (DDoS-) támadások olyan ügyfelek helyezze át az alkalmazásaikat a felhőbe irányuló legnagyobb rendelkezésre állást és a biztonsági aggályokon egyikét. DDoS-támadások megkísérli lefoglalhat egy alkalmazás-erőforrásokat, így az alkalmazás valódi felhasználók számára nem érhető el. DDoS-támadások tud célozható tetszőleges végpontot, amely nyilvánosan elérhető az interneten keresztül.

Az alkalmazás tervezési ajánlott eljárásokkal, Azure DDoS védelem DDoS-támadások elleni védelmet biztosít. Az Azure DDos-védelem a következő szolgáltatási szinteket tartalmazza: 

- **Az Azure DDoS védelem alapvető**: automatikusan engedélyezve van, nem kell külön fizetni az Azure platform részeként. Közös hálózati szintű támadások figyelési és valós idejű megoldás – a forgalmat a Microsoft online services által használt azonos védelmet nyújt. Teljes méretű Azure globális hálózati támadás forgalom terjesztése és régiók közötti használható. Védelmet biztosítanak a IPv4 és IPv6-alapú Azure [nyilvános IP-címek](virtual-network-public-ip-address.md).
- **Az Azure DDoS védelem szabványos** kifejezetten az Azure virtuális hálózati erőforrások beállított megoldás további képességeket biztosít. Egyszerű engedélyezi, és nincs alkalmazás módosítását igényli. Adatvédelmi szabályzatok hangolt keresztül dedikált forgalom figyelése és a gépi tanulási algoritmusok és üzembe helyezett virtuális hálózatokhoz, például az Azure Load Balancer, az Azure Application Gateway és az Azure erőforrás társított nyilvános IP-címek érvényesek A Service Fabric-példányokat. Valós idejű telemetriai érhető Azure figyelő nézetek, a támadás során, és az előzmények. Alkalmazás rétegek védelmét segítségével is hozzáadhat [alkalmazás átjáró webalkalmazási tűzfal](https://azure.microsoft.com/services/application-gateway). Védelmet biztosítanak a IPv4 Azure [nyilvános IP-címek](virtual-network-public-ip-address.md). 

![Az Azure DDoS védelem Standard](./media/ddos-protection-overview/ddos-protection-overview-fig2.png)

> [!IMPORTANT]
> Az Azure DDoS védelem szabványos jelenleg előzetes verzió. Védelmet biztosítanak a az Azure-erőforrásokkal, amely társítva, például virtuális gépek terheléselosztók vagy alkalmazásátjárót Azure nyilvános IP-címe. Kell [regisztrálása](http://aka.ms/ddosprotection) a szolgáltatás engedélyezése előtt DDoS védelem szabványos az előfizetéséhez. A regisztrálás után az Azure DDoS csapat felveszi a kapcsolatot, és végigvezeti Önt az engedélyezési folyamat. DDoS védelem szabványos nem áll rendelkezésre az USA keleti régiója, USA keleti régiója 2. régiója, USA nyugati régiója, csak a nyugati középső Régiójában, Észak-Európa, Nyugat-Európában, Nyugat-japán, kelet-japán, Kelet-Ázsia, és Délkelet-Ázsia régiók. Előzetes akkor nem számolnak szolgáltatásunkat használja.

Javasoljuk, hogy próbálja DDoS védelem szabványos, fejlesztési, tesztelési vagy üzemi környezetben. A következő források segítségével visszajelzést adhat az Önnek nyújtott élményt:
- [A Microsoft Azure fórum Azure DDoS védelem](https://feedback.azure.com/forums/905032-azure-ddos-protection). 
- [Az Azure DDoS védelem az MSDN fórumon](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azureddosprotection)
- [A verem túlcsordulását Azure DDos védelem](https://stackoverflow.com/tags/azure-ddos/info)

Támogatási problémák esetére, akkor [nyissa meg az Azure támogatási jegy](../azure-supportability/how-to-create-azure-support-request.md). Míg DDoS védelem szabványos Preview, nyújtott támogatás a legjobb alapon.

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>DDoS védelem szabványos csökkenti DDoS-támadások típusai

DDoS védelem szabványos mérséklésére vonatkozó útmutatások ilyen jellegű támadások:

- **-Es támadások**: A támadás célja, hogy a hálózati réteg látszólag jogos forgalom jelentős mennyiségű kéréssekkel. Ez magában foglalja az UDP árvizek, erősítési árvizek és egyéb megtévesztésre csomag árvizek. DDoS védelem szabványos azzal csökkenti ezeket több gigabájt webtartalmak szűkítő, és azokat, a tisztítási Azure globális hálózati méretezési, automatikusan kihasználva. 
- **Protokoll támadások**: ilyen támadások leképezési célja nem érhető el a 3 rétegbeli és a 4. rétegbeli protokollverem egyik támadható gyenge pontja kihasználva. Ez magában foglalja, SZIN árvíz támadások, támadások és más protokoll támadásoknak. DDoS védelem szabványos azzal csökkenti az ilyen támadások, rosszindulatú és jogos forgalom, az ügyfél való interakció, és blokkolja a rosszindulatú forgalom megkülönböztetése. 
- **Alkalmazás réteg támadások**: ilyen támadások cél webes alkalmazás csomagok zavarná a gazdagépek közötti adatátvitel. Ez magában foglalja a HTTP protokoll megsértése, SQL injektálási, többhelyes parancsfájlok és egyéb réteg 7 támadások. Használja az Azure [Alkalmazásátjáró webalkalmazási tűzfal](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), a DDoS védelem Standard, az ilyen támadások elleni védelmet biztosít. 

DDoS védelem szabványos védi többek között a virtuális gépek terheléselosztók és alkalmazásátjárót társított nyilvános IP-címek egy virtuális hálózatán lévő erőforrásokat. Az Alkalmazásátjáró webalkalmazási tűzfal alapján kialakulhat, amikor DDoS védelem szabványos biztosíthat teljes réteg 3. rétegbeli 7 csökkentése terén.

## <a name="ddos-protection-standard-features"></a>DDoS védelem szabványos szolgáltatások

![DDoS-funkciók](./media/ddos-protection-overview/ddos-overview-fig1.png)

DDoS védelem szabványos szolgáltatások a következők: 

- **Natív platformintegráció:** natív módon integrálva az Azure, és tartalmazza a konfiguráció az Azure portál és a PowerShell használatával. DDoS védelem szabványos együttműködik az erőforrások és az erőforrás-konfigurációban.
- **Állandóan működő forgalomfigyelés:** az alkalmazás forgalmi minták figyelt 24 óránként, naponta, a hét, DDoS-támadások mutatók keres. Megoldás adatvédelmi szabályzatok túllépése esetén történik.
- **Kulcsrakész védelmi:** egyszerűsített konfigurációs azonnal védelmet nyújt a virtuális hálózaton lévő összes erőforrást, amint DDoS védelem szabványos engedélyezett. Nincs beavatkozás vagy a felhasználó-definícióra szükség. DDoS védelem szabványos azonnal és automatikusan csökkenti a támadási után észlelhető.
- **Adaptív hangolása:** intelligens forgalom profilkészítési tanulják meg az alkalmazás forgalom adott idő alatt, és kiválasztja, és frissíti a profilt, amely a szolgáltatás számára legalkalmasabb. A profil módosítja, a forgalom idővel változik.
- **3. rétegbeli réteg 7 védelem:** DDoS-védelem teljes verem biztosít olyan átjárót használva.
- **Széles körű megoldás méretezési:** keresztül 60 különböző támadás típusok kivédhető, globális kapacitás, a legnagyobb ismert DDoS-támadások elleni védelem érdekében. 
- **Támadás metrikák:** Summarized metrikák minden támadások elleni Azure figyelő keresztül érhetők el.
- **Támadás riasztási:** riasztások konfigurálhatók a kezdő- és az olyan támadások, állítsa le és a támadás időtartama alatt használja a beépített támadás metrikákat. Riasztások integrálja a működési szoftverek, mint a Microsoft Operations Management Suite, Splunk, Azure Storage, e-mailek és az Azure-portálon.
- **Költség garancia:** adatátvitelt és alkalmazás kibővített szolgáltatási jóváírások dokumentált DDoS-támadások.

## <a name="ddos-protection-standard-mitigation"></a>DDoS védelem szabványos megoldás

A Microsoft DDoS-védelem szolgáltatás tényleges forgalom kihasználtsági figyeli, és folyamatosan összevetett a a DDoS házirendben meghatározott küszöbértékeket. Forgalom a küszöbérték túllépésekor, majd automatikusan DDoS megoldás kezdeményezett meg. Ha a forgalom adja vissza, a küszöbérték alá, a megoldás törlődik.

Során csökkentése a védett erőforrásokhoz küldött forgalmat a rendszer átirányítja a DDoS-védelem szolgáltatás, és a rendszer több ellenőrzi. Az ellenőrzések általában hajtsa végre a következő funkciókat:

- Győződjön meg arról, csomagok felel meg az Internet specifikációjának, illetve nem hibás formátumú.
- Annak megállapításához, hogy a forgalom potenciálisan hamisított csomagot az ügyfél kommunikálni (pl.: SZIN Auth vagy SZIN cookie-k vagy újraküldésére az adatforrásra vonatkozóan egy csomag ejtésével).
- Sebességhatár csomagok, ha más érvényesítési metódus nem hajtható végre.

DDoS védelem blokkok támadásokkal és a fennmaradó forgalmat a kívánt rendeltetési továbbítást. Észlelt támadás, néhány percen belül értesítést kap Azure figyelő metrikák használatával. DDoS védelem szabványos telemetriai bejelentkezés konfigurálásával írhat a naplók jövőbeli elemzéshez beállításokat. Az Azure-figyelő DDoS védelem szabvány metrikaadatokat jelenleg 30 napig megőrzi.

Az ügyfelek számára a saját DDoS-támadások szimulálása használata nem ajánlott. Ehelyett felhasználók használhatják a támogatási csatornán keresztül egy DDoS kérelem végrehajtja az Azure-hálózat szimuláció támadásoknak. Egy mérnököt felveszi Önnel a DDoS-támadások (portok, protokollok, cél IP-címek) részleteit elrendezése és a vizsgálat ütemezése egyszerre.

## <a name="next-steps"></a>Következő lépések

- További információk kezelése DDoS védelem szabványos használatával [Azure PowerShell](ddos-protection-manage-ps.md) vagy a [Azure-portálon](ddos-protection-manage-portal.md).
