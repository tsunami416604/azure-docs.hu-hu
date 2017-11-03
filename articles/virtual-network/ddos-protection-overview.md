---
title: "Az Azure DDoS védelem szabványos áttekintése |} Microsoft Docs"
description: "További tudnivalók az Azure DDoS-védelem szolgáltatás."
services: virtual-network
documentationcenter: na
author: kumudD
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/22/2017
ms.author: kumud
ms.openlocfilehash: 76da0d4e805c732d40a7bd02e5c70973c792e26c
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="azure-ddos-protection-standard-overview"></a>Az Azure szabványos DDoS-védelem áttekintése

>[!IMPORTANT]
>Az Azure DDoS védelem szabványos jelenleg előzetes verzió. Korlátozott számú DDoS védelem Standard Azure-erőforrások támogatása, és válassza ki több régióban. Kell [a szolgáltatás regisztrálása](http://aka.ms/ddosprotection) a korlátozott előzetes beolvasandó DDoS védelem szabványos engedélyezett az előfizetéséhez. Próbál kapcsolódni, az Azure DDoS csoport regisztráláskor végigvezeti az engedélyezési folyamat által. DDoS védelem szabványos érhető el a amerikai keleti, Velünk nyugati és nyugati középső Régiójában régióban. Előzetes akkor nem számolnak szolgáltatásunkat használja.

Elosztott szolgáltatásmegtagadásos (DDoS-) támadások közül a legnagyobb rendelkezésre állási és biztonsági helyezze át az alkalmazásaikat a felhőbe irányuló ügyfelek vonatkozik. DDoS-támadások megkísérli lefoglalhat egy alkalmazás források az alkalmazás nem érhető el a valódi felhasználók számára. DDoS-támadások tud célozható tetszőleges végpontot, amely nyilvánosan elérhető az interneten keresztül.

Az alkalmazás ajánlott tervezési eljárásokat együttesen Azure DDoS-védelem ilyen támadások elleni védelmet biztosít. E két szolgáltatásszintek itt találhatók: 

- **Az Azure DDoS védelem alapvető** -használatáért nem kell külön fizetni az Azure platform részeként már automatikusan engedélyezve van. Közös hálózati szintű támadások figyelési és valós idejű megoldás – a forgalmat a Microsoft online services által használt azonos védelmet nyújt.  Teljes méretű Azure globális hálózati támadás forgalom terjesztése és régiók közötti használható. 
- **Az Azure DDoS védelem szabványos** -kifejezetten a virtuális hálózati erőforrások beállított megoldás további képességeket biztosít. Egyszerű engedélyezése és nem alkalmazás módosítását igényli. Adatvédelmi szabályzatok hangolt keresztül dedikált forgalom figyelése és a gépi tanulási algoritmusok és a társított virtuális hálózati erőforrások, például terheléselosztó Application Gateway és a Service Fabric-példányok nyilvános IP-címek érvényesek.  Valós idejű telemetriai érhető Azure figyelő nézetek, a támadás során, és az előzmények. Alkalmazás rétegek védelmét segítségével is hozzáadhat [alkalmazás átjáró webalkalmazási tűzfal](https://azure.microsoft.com/services/application-gateway/). 

![Az Azure DDoS védelem Standard](./media/ddos-protection-overview/ddos-protection-overview-fig2.png)

Javasoljuk, hogy próbálja DDoS védelem szabványos, fejlesztési, tesztelési vagy üzemi környezetben. A következő források segítségével visszajelzést adhat az Önnek nyújtott élményt:
- [A Microsoft Azure fórum Azure DDoS védelem](https://feedback.azure.com/forums/905032-azure-ddos-protection). 
- [Az Azure DDoS védelem az MSDN fórumon](https://social.msdn.microsoft.com/forums/azure/en-US/home?forum=azureddosprotection)
- [A verem túlcsordulását Azure DDos védelem](https://stackoverflow.com/tags/azure-ddos/info)

Támogatási problémák esetére, akkor [nyissa meg az Azure támogatási jegy](../azure-supportability/how-to-create-azure-support-request.md). Míg DDoS védelem szabványos Preview, nyújtott támogatás a legjobb alapon.

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>DDoS védelem szabványos csökkenti DDoS-támadások típusai

DDoS védelem szabványos mérséklésére vonatkozó útmutatások ilyen jellegű támadások:

- **-Es támadások** -a támadás célja, hogy a hálózati réteg látszólag jogos forgalom jelentős mennyiségű kéréssekkel. UDP árvizek, erősítési árvizek és egyéb megtévesztésre csomag árvizek tartalmaz. DDoS védelem szabványos azzal csökkenti a többszörös gigabájt webtartalmak szűkítő & őket a tisztítási Azure globális hálózati méretezési automatikusan kihasználva. 
- **Protokoll támadások** -ilyen támadások leképezési célja nem érhető el a 3. rétegbeli és a réteg 4 protokollverem egyik támadható gyenge pontja kihasználva. Ez magában foglalja, SZIN árvíz támadások, támadások és más protokoll támadásoknak. DDoS védelem szabványos azzal csökkenti az ilyen támadások, rosszindulatú és jogos forgalom által az ügyfél való interakció, és blokkolja a rosszindulatú forgalom megkülönböztetése. 
- **Alkalmazás réteg támadások** -ilyen támadások cél webes alkalmazás csomagok zavarná a gazdagépek közötti adatátvitel. Ez magában foglalja a HTTP protokoll megsértése, SQL injektálási, többhelyes parancsfájlok és egyéb réteg 7 támadásoknak. DDoS védelem standard alkalmazás átjáró WAF használatával adja meg az ilyen támadások elleni védelmet. 

DDoS védelem szabványos védi többek között a virtuális gépek, a belső terheléselosztók és az alkalmazás átjárók társított nyilvános IP-címek egy virtuális hálózatán lévő erőforrásokat. Az alkalmazás átjáró WAF SKU alapján kialakulhat, amikor DDoS védelem szabványos biztosíthat teljes 3. a 7. megoldás funkció.

## <a name="ddos-protection-standard-features"></a>DDoS védelem szabványos szolgáltatások

![DDoS-funkciók](./media/ddos-protection-overview/ddos-overview-fig1.png)

DDoS védelem szabványos szolgáltatások a következők: 

- **Natív platformintegráció:** DDoS védelem szabványos natív módon integrálva van Azure és az Azure portál és a PowerShell használatával konfigurációs tartalmazza. DDoS védelem szabványos együttműködik az erőforrások és az erőforrás-konfigurációban.
- **Állandóan működő forgalomfigyelés:** az alkalmazás forgalmi minták figyelt 24 x 7, DDoS-támadások mutatók keres. Megoldás adatvédelmi szabályzatok túllépése esetén történik.
- **Kulcsrakész védelmi:** egyszerűsített konfigurációs azonnal védelmet nyújt a virtuális hálózaton lévő összes erőforrást, amint DDoS védelem szabványos engedélyezett. Nincs beavatkozás vagy a felhasználó-definícióra szükség – DDoS védelem szabványos azonnal és automatikusan csökkenti a támadási után a rendszer észlelte.
- **Adaptív hangolása:** intelligens forgalom profilkészítési tanulják meg az alkalmazás forgalom adott idő alatt, és kiválasztja, és frissíti a profilt, amely a szolgáltatás számára legalkalmasabb. A profil módosítja, a forgalom idővel változik.
- **3. a 7. a védelmet az Alkalmazásátjáró:** alkalmazás átjáró WAF szolgáltatásokat nyújtó teljes verem DDoS-védelem.
- **Széles körű megoldás méretezési:** keresztül 60 különböző támadás típusok mérsékelhető a globális kapacitással a legnagyobb ismert DDoS-támadások elleni védelem érdekében. 
- **Támadás metrikák:** Summarized metrikák minden támadások elleni Azure figyelő keresztül érhetők el.
- **A támadás riasztási:** riasztások konfigurálhatók a kezdő- és állítsa le az olyan támadások és a támadás alatt tartozó időtartam használata beépített támadás metrikákat. Riasztások integrálja a működési szoftverek, például OMS, Splunk, Azure Storage, e-mailek és az Azure portálon.
- **Költség garancia:** adatátvitelt és alkalmazás kibővített szolgáltatási jóváírások dokumentált DDoS-támadások.

## <a name="ddos-protection-standard-mitigation"></a>DDoS védelem szabványos megoldás

A Microsoft DDoS-védelem szolgáltatás tényleges forgalom kihasználtsági figyeli, és folyamatosan összevetett a a DDoS házirendben meghatározott küszöbértékeket. Forgalom a küszöbérték túllépésekor, majd automatikusan DDoS megoldás kezdeményezett meg. Ha a forgalom adja vissza, a küszöbérték alá, a megoldás törlődik.

Során csökkentése a védett erőforrások felé forgalmat a rendszer átirányítja a DDoS-védelem szolgáltatás, és a rendszer több ellenőrzi. Az ellenőrzések általában hajtsa végre a következő függvény:

- Győződjön meg arról, csomagok felel meg az Internet specifikációjának, illetve nem hibás formátumú.
- Állapítsa meg, hogy vélhetően hamisított csomagot az ügyfél kommunikálni (pl.: SZIN Auth vagy SZIN cookie-k vagy újraküldésére az adatforrásra vonatkozóan egy csomag ejtésével).
- Ha nincs más kényszerítési módszerének sebességhatár csomagok végezheti el.

DDoS-védelem blokkok forgalmat, és előre a megfelelő cél felé irányuló forgalom fennmaradó támadásnak. Észlelt támadás, néhány percen belül értesítést kap Azure figyelő metrikák használatával. DDoS védelem szabványos telemetriai bejelentkezés konfigurálásával írhat a naplók jövőbeli elemzéshez beállításokat. Az Azure-figyelő DDoS védelem szabvány metrikaadatokat jelenleg 30 napig megőrzi.

Az ügyfelek számára a saját DDoS-támadások szimulálása használata nem ajánlott. Ehelyett felhasználók használhatják a támogatási csatornán keresztül egy DDoS kérelem végrehajtja az Azure-hálózat szimuláció támadásoknak. Egy mérnököt felveszi Önnel a DDoS-támadások (portok, protokollok, cél IP-címek) részleteit elrendezése és a vizsgálat ütemezése egyszerre.

## <a name="next-steps"></a>Következő lépések

- További információk kezelése DDoS védelem szabványos használatával [Azure PowerShell](ddos-protection-manage-ps.md) vagy a [Azure-portálon](ddos-protection-manage-portal.md).
