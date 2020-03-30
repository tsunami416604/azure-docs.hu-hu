---
title: Az Azure DDoS Protection standard – áttekintés
description: Ismerje meg az Azure DDoS Protection szolgáltatást.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/22/2020
ms.author: kumud
ms.openlocfilehash: f1dd33425a57689974fc98a28724adf7b130ab40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536343"
---
# <a name="azure-ddos-protection-standard-overview"></a>Az Azure DDoS Protection Standard áttekintése

Az elosztott szolgáltatásmegtagadásos (DDoS-) támadások az egyik legnagyobb rendelkezésreállási és biztonsági kockázatot jelentik az olyan felhasználók számára, akik alkalmazásaikat a felhőbe helyezik át. A DDoS-támadás megpróbálja kimeríteni az alkalmazás erőforrásait, így az alkalmazás nem érhető el a jogos felhasználók számára. A DDoS-támadások bármilyen, az interneten keresztül nyilvánosan elérhető végpontot megcélozhatnak.

Az Azure DDoS-védelem az alkalmazástervezéssel kapcsolatos gyakorlati tanácsokkal kombinálva védelmet nyújt a DDoS-támadások ellen. Az Azure DDoS-védelem a következő szolgáltatási szinteket biztosítja:

- **Alapszintű**: Automatikusan engedélyezve az Azure platform részeként. A mindig forgalomfigyelés és a gyakori hálózati szintű támadások valós idejű csökkentése ugyanazt a védelmet biztosítja, mint a Microsoft online szolgáltatásai.Az Azure globális hálózatának teljes skálája használható a támadások közötti forgalom régiók közötti elosztására és csökkentésére.Az IPv4- és Az IPv6 Azure [nyilvános IP-címeihez védelem](virtual-network-public-ip-address.md)biztosított.
- **Standard**: További kockázatcsökkentési képességeket biztosít az alapszintű szolgáltatási rétegen keresztül, amelyek kifejezetten az Azure virtuális hálózati erőforrásokra vannak hangolva. A DDoS Protection Standard egyszerűen engedélyező, és nem igényel alkalmazásmódosításokat. A védelmi szabályzatokat dedikált forgalomfigyelés és gépi tanulási algoritmusok finomhangolják. A szabályzatok a virtuális hálózatokban üzembe helyezett erőforrásokhoz , például az Azure Load Balancer, az Azure Application Gateway és az Azure Service Fabric-példányokhoz társított nyilvános IP-címekre vonatkoznak, de ez a védelem nem vonatkozik az App Service-környezetekre.Valós idejű telemetriai adatok érhetők el az Azure Monitor nézetek támadás során, és az előzmények. A támadások kockázatcsökkentési elemzések diagnosztikai beállításokon keresztül érhetők el. Az alkalmazásréteg-védelem hozzáadható az [Azure Application Gateway webalkalmazás-tűzfalon](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) keresztül, vagy egy külső tűzfal telepítése az Azure Marketplace-ről. Az IPv4- és Az IPv6 Azure [nyilvános IP-címeihez védelem](virtual-network-public-ip-address.md)biztosított.

|Szolgáltatás                                         |DDoS védelem alap                 |DDoS védelmi szabvány                      |
|------------------------------------------------|--------------------------------------|----------------------------------------------|
|Az aktív forgalomfigyelés mindig észleléskor & |Igen                                   |Igen                                           |
|Automatikus támadáscsökkentés                    |Igen                                   |Igen                                           |
|Rendelkezésre állási garancia                          |Azure-régió                          |Alkalmazás                                   |
|Kockázatcsökkentési politikák                             |Az Azure forgalmi régió kötetére hangolva |Alkalmazásforgalom-hangnemre hangolva          |
|Mérőszámok & riasztások                                |Nem                                    |Valós idejű támadási mutatók & diagnosztikai naplók az Azure-figyelőn keresztül                                 |
|Kockázatcsökkentési jelentések                              |Nem                                    |Támadás utáni kockázatcsökkentési jelentések                |
|Kockázatcsökkentési folyamatnaplók                            |Nem                                    |NRT naplófolyam a SIEM-integrációhoz           |
|A kockázatcsökkentési házirend testreszabása                 |Nem                                    |Vegyenek részt a DDoS szakértőiben                           |
|Támogatás                                         |Legjobb erőfeszítés                           |Hozzáférés a DDoS-szakértőkhöz egy aktív támadás során|
|SLA                                             |Azure-régió                          |Alkalmazásgarancia & költségvédelem       |
|Díjszabás                                         |Ingyenes                                  |Havi & használat alapján                         |

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>A DDoS-védelem standard által enyhített DDoS-támadások típusai

A DDoS Protection Standard a következő típusú támadásokat csökkentheti:

- **Volumetrikus támadások**: A támadás célja, hogy elárassza a hálózati réteget jelentős mennyiségű látszólag jogos forgalommal. Ez magában foglalja az UDP árvizek, erősítő árvizek, és egyéb hamisított csomag árvizek. A DDoS Protection Standard az Azure globális hálózati skálájával automatikusan csökkenti ezeket a potenciális több gigabájtos támadásokat.
- **Protokolltámadások**: Ezek a támadások elérhetetlenné teszik a célpontot a 3- as és a 4-es réteg protokollverem gyengeségének kihasználásával. Ez magában foglalja, SYN árvíztámadások, reflexiós támadások, és egyéb protokoll támadások. A DDoS Protection Standard az ügyféllel való interakcióval és a rosszindulatú forgalom blokkolásával mérsékli ezeket a támadásokat, különbséget tesz a rosszindulatú és a jogszerű forgalom között. 
- **Erőforrás (alkalmazás) rétegtámadások**: Ezek a támadások webes alkalmazáscsomagokat céloznak meg, hogy megzavarják az adatok átvitelét az állomások között. A támadások közé tartozik a HTTP protokoll megsértése, SQL-injektálás, helyek közötti parancsfájlok és egyéb 7- es réteges támadások. Használjon webalkalmazás-tűzfalat, például az Azure [Application Gateway webalkalmazás tűzfalát,](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)valamint a DDoS-védelmi szabványt a támadások elleni védelem biztosításához. Az [Azure Piactéren](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)is elérhetők a külső webalkalmazások tűzfalajánlatai.

A DDoS Protection Standard védi a virtuális hálózat erőforrásait, beleértve a virtuális gépekhez, terheléselosztókhoz és alkalmazásátjárókhoz társított nyilvános IP-címeket. Ha az Application Gateway webalkalmazás-tűzfalával vagy egy nyilvános IP-című virtuális hálózatban telepített külső webalkalmazás-tűzfallal párosul, a DDoS Protection Standard teljes 3-as réteg-csökkentési képességet biztosít.

## <a name="ddos-protection-standard-features"></a>DDoS Protection Standard funkciók

![DDoS funkció](./media/ddos-protection-overview/ddosfeatures.png)

DDoS Protection Standard funkciók a következők:

- **Natív platformintegráció:** Natívmódon integrálva az Azure-ba. Az Azure Portalon keresztüli konfigurációt tartalmazza. A DDoS Protection Standard tisztában van az erőforrásokkal és az erőforrás-konfigurációval.
- **Kulcsrakész védelem:** Az egyszerűsített konfiguráció azonnal védi a virtuális hálózaton lévő összes erőforrást, amint a DDoS Protection Standard engedélyezve van. Nincs szükség beavatkozásra vagy felhasználódefinícióra. DDoS Protection Standard azonnal és automatikusan csökkenti a támadást, ha észleli.
- **Mindig forgalomfigyelés:** Az alkalmazás forgalmi minták figyelik a nap 24 órájában, a hét minden napján, a DDoS-támadások mutatóit keresve. A védelmi házirendek túllépése esetén a kockázatcsökkentés történik.
- **Adaptív hangolás:** Az intelligens forgalmi profilkészítés idővel megtanulja az alkalmazás forgalmát, és kiválasztja és frissíti a szolgáltatásszámára legmegfelelőbb profilt. A profil a forgalom időbeli változásával változik.
- **Többrétegű védelem:** Teljes verem DDoS-védelmet biztosít, ha webalkalmazás-tűzfallal használja.
- **Kiterjedt mérséklési skála:** Több mint 60 különböző támadástípus mérsékelhető globális kapacitással a legnagyobb ismert DDoS-támadások elleni védelem érdekében.
- **Támadás elemzés:** Részletes jelentéseket kaphat öt perces lépésekben a támadás során, és egy teljes összefoglalót a támadás befejezése után. Streammitigation flow naplók egy offline biztonsági információs és eseménykezelési (SIEM) rendszer közel valós idejű figyelése a támadás során.
- **Támadási mérőszámok:** Az egyes támadások összesített metrikái az Azure Monitoron keresztül érhetők el.
- **Támadásriasztás:** Riasztások konfigurálható a támadás kezdetén és leállításakor, és a támadás időtartama alatt, beépített támadási metrikák használatával. A riasztások integrálhatók az operatív szoftverekbe, például a Microsoft Azure Monitor naplóiba, a Splunkba, az Azure Storageba, az E-mailbe és az Azure Portalra.
- **Költséggarancia:** Adattovábbítási és alkalmazás-kibővített szolgáltatás kreditek dokumentált DDoS-támadások.

## <a name="ddos-protection-standard-mitigation"></a>DDoS Protection Standard kockázatcsökkentés

A DDoS Protection Standard figyeli a tényleges forgalomkihasználtságot, és folyamatosan összehasonlítja azt a DDoS-szabályzatban meghatározott küszöbértékekkel. A forgalmi küszöbérték túllépése esetén a DDoS-kockázatcsökkentés automatikusan elindul. Ha a forgalom visszatér a küszöbérték alatt, a kockázatcsökkentés törlődik.

![Kezelés](./media/ddos-protection-overview/mitigation.png)

A megoldás során a védett erőforrásnak küldött forgalmat a DDoS védelmi szolgáltatás átirányítja, és több ellenőrzést hajt végre, például a következő ellenőrzéseket:

- Győződjön meg arról, hogy a csomagok megfelelnek az internetes specifikációknak, és nem hibásak.
- Lépjen kapcsolatba az ügyféllel annak megállapítására, hogy a forgalom potenciálisan hamisított csomag-e (pl. SYN Auth vagy SYN Cookie, vagy a forrás újraküldéséhez szükséges csomag eldobása).
- Rate-limit csomagok, ha más kényszerítési módszer nem hajtható végre.

A DDoS Protection blokkolja a támadó adatforgalmat, a fennmaradó forgalmat pedig az eredeti célhelyre továbbítja. Az észlelt támadásokról az Azure Monitor metrikái néhány percen belül értesítést küldenek. A DDoS Protection Standard telemetriai adatok naplózásának konfigurálásával a naplókat a jövőbeli elemzéshez rendelkezésre álló lehetőségekre írhatja. Metrika adatok az Azure Monitor ddos-védelmi szabvány 30 napig megmarad.

A Microsoft a [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud) vállalattal együttműködve olyan felületet hozhat létre, ahol a DDoS Protection-kompatibilis nyilvános IP-címek ellen forgalmat hozhat létre szimulációkhoz. A BreakPoint Cloud szimuláció lehetővé teszi, hogy:

- Annak ellenőrzése, hogy a Microsoft Azure DDoS Protection Standard hogyan védi az Azure-erőforrásokat a DDoS-támadásokellen
- Optimalizálja az incidensválasz-folyamatot a DDoS-támadás alatt
- Dokumentum DDoS-megfelelőség
- A hálózati biztonsági csapatok betanítása

## <a name="next-steps"></a>További lépések

- [DdoS védelmi szabvány konfigurálása](manage-ddos-protection.md)
