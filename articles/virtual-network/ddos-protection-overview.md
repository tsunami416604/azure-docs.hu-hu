---
title: Azure DDoS Protection standard áttekintése
description: Ismerkedjen meg a Azure DDoS Protection szolgáltatással.
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
ms.openlocfilehash: fc47e1f4fbdb48e6e0abc1f2a7e32127b0325f47
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "82130974"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS Protection standard áttekintése

Az elosztott szolgáltatásmegtagadásos (DDoS-) támadások az egyik legnagyobb rendelkezésreállási és biztonsági kockázatot jelentik az olyan felhasználók számára, akik alkalmazásaikat a felhőbe helyezik át. A DDoS-támadás megpróbál kimeríteni egy alkalmazás erőforrásait, így az alkalmazás nem érhető el a legitim felhasználók számára. A DDoS-támadások bármilyen, az interneten keresztül nyilvánosan elérhető végpontot megcélozhatnak.

Az Azure DDoS Protection az alkalmazások kialakításával kapcsolatos ajánlott eljárásokkal kombinálva védelmet nyújt a DDoS-támadásokkal szemben. Az Azure DDoS Protection a következő szolgáltatási szinteket biztosítja:

- **Alapszintű**: automatikusan engedélyezve van az Azure platform részeként. A forgalom folyamatos monitorozása és a gyakori hálózati szintű támadások valós idejű enyhítése biztosítja a Microsoft online szolgáltatások által használt védelmi adatokat.Az Azure globális hálózatának teljes skálája felhasználható a különböző régiók közötti támadási forgalom elosztására és enyhítésére.A védelem az IPv4-és IPv6-alapú Azure [nyilvános IP-címekhez](virtual-network-public-ip-address.md)van megadva.
- **Standard**: további enyhítő képességeket biztosít az alapszintű szolgáltatási szinten, amelyeket kifejezetten az Azure Virtual Network erőforrásaihoz hangoltak. DDoS Protection a standard egyszerűen engedélyezhető, és nem igényel alkalmazás-módosítást. A védelmi szabályzatokat dedikált forgalomfigyelés és gépi tanulási algoritmusok finomhangolják. A házirendeket a rendszer a virtuális hálózatokban üzembe helyezett erőforrásokhoz társított nyilvános IP-címekre alkalmazza, például az Azure Load Balancer, az Azure Application Gateway és az Azure Service Fabric példányokban, de ez a védelem nem vonatkozik App Service környezetekre.A valós idejű telemetria a támadás során Azure Monitor nézeteken keresztül érhető el, és az előzményekhez. A hatékony támadások enyhítése diagnosztikai beállításokon keresztül érhető el. Az alkalmazások rétegének védelme az [azure Application Gateway webalkalmazási tűzfalon](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) keresztül, vagy egy külső gyártótól származó tűzfal az Azure Marketplace-ről való telepítésével adható hozzá. A védelem az IPv4-és IPv6-alapú Azure [nyilvános IP-címekhez](virtual-network-public-ip-address.md)van megadva.

|Szolgáltatás                                         |Alapszintű DDoS Protection                 |DDoS Protection standard                      |
|------------------------------------------------|--------------------------------------|----------------------------------------------|
|Aktív forgalom figyelése & always on észlelés |Igen                                   |Igen                                           |
|Automatikus támadás-enyhítés                    |Igen                                   |Igen                                           |
|Rendelkezésre állási garancia                          |Azure-régió                          |Alkalmazás                                   |
|Kockázatcsökkentő házirendek                             |Az Azure-beli forgalom régiójához hangolva |Az alkalmazások forgalmának mennyiségére hangolva          |
|Metrikák & riasztások                                |Nem                                    |Valós idejű támadási mérőszámok & erőforrás-naplókat a Azure Monitor használatával                                 |
|Kockázatcsökkentő jelentések                              |Nem                                    |Támadás utáni kockázatcsökkentő jelentések                |
|A kockázatcsökkentő folyamat naplói                            |Nem                                    |VIZSGÁLJA-naplózási stream a SIEM-integrációhoz           |
|Enyhítő szabályzat testreszabása                 |Nem                                    |DDoS-szakértők részvétele                           |
|Támogatás                                         |Legjobb erőfeszítés                           |Hozzáférés a DDoS-szakértőkhöz aktív támadás során|
|SLA                                             |Azure-régió                          |Alkalmazás-garancia & Cost Protection       |
|Díjszabás                                         |Ingyenes                                  |Havi & használat alapján                         |

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>A standard megoldásokkal DDoS Protection DDoS-támadások típusai

DDoS Protection a standard a következő típusú támadásokat csökkentheti:

- Nagy mennyiségű **támadás**: a támadás célja, hogy elárasztsa a hálózati réteget, amely jelentős mértékben látszólag megbízható adatforgalommal rendelkezik. Ide tartozik az UDP-árvizek, az erősítési árvizek és a hamis csomagokra vonatkozó egyéb áradások. DDoS Protection standard az Azure globális hálózati skálázásával automatikusan felhasználhatja ezeket a lehetséges több gigabájtos támadásokat.
- **Protokollok elleni támadások**: ezek a támadások a 3. rétegbeli és a 4. rétegbeli protokollkészlet gyengeségének kihasználásával elérhetetlenné teszik a célt. Ez magában foglalja a SYN FLOOD támadásokat, a reflexiós támadásokat és más protokollok elleni támadásokat. A DDoS Protection standard csökkenti ezeket a támadásokat, megkülönbözteti a kártékony és a legitim forgalmat, az ügyféllel való interakcióval és a rosszindulatú forgalom blokkolásával. 
- **Erőforrás (alkalmazás) rétegbeli támadások**: ezek a támadások a webalkalmazási csomagokat célozzák meg a gazdagépek közötti adatátvitel megzavarásához. A támadások közé tartoznak a HTTP-protokollok megsértése, az SQL-injektálás, a helyközi parancsfájlkezelés és a 7. rétegbeli támadások. Használjon webalkalmazási tűzfalat, például az Azure [Application Gateway webalkalmazási tűzfalat](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), valamint DDoS Protection standardot a támadások elleni védelem biztosításához. Az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)-en a harmadik féltől származó webalkalmazási tűzfalak is elérhetők.

DDoS Protection standard védi a virtuális hálózatok erőforrásait, beleértve a virtuális gépekhez, a terheléselosztóhoz és az Application gatewayhez társított nyilvános IP-címeket is. Ha a Application Gateway webalkalmazási tűzfallal párosul, vagy egy nyilvános IP-címmel rendelkező virtuális hálózatban üzembe helyezett, harmadik féltől származó webalkalmazási tűzfal, DDoS Protection a standard teljes 3. rétegbeli, 7. rétegbeli kockázatcsökkentő képességet biztosít.

## <a name="ddos-protection-standard-features"></a>DDoS Protection standard funkciók

![DDoS-funkciók](./media/ddos-protection-overview/ddosfeatures.png)

DDoS Protection a standard funkciók a következők:

- **Natív platform-integráció:** Natív módon integrálva az Azure-ba. A Azure Portal konfigurációját tartalmazza. A DDoS Protection standard értelmezése az erőforrásokat és az erőforrás-konfigurációt.
- **Kulcsrakész védelem:** Az egyszerűsített konfiguráció azonnal megvédi a virtuális hálózat összes erőforrását, amint DDoS Protection standard engedélyezve van. Nincs szükség beavatkozásra vagy felhasználói definícióra. A rendszer azonnal DDoS Protection a standardot, és automatikusan csökkenti a támadásokat, amint azt észlelte.
- **Folyamatos forgalom figyelése:** Az alkalmazás forgalmi mintáit napi 24 órában, a hét 7 napján, a DDoS-támadások mutatóit keresve figyeli. A rendszer a védelmi házirendek túllépése esetén csökkenti a megoldást.
- **Adaptív hangolás:** Az intelligens forgalmi profilkészítés az idő múlásával megtanulja az alkalmazás forgalmát, és kiválasztja és frissíti a szolgáltatás számára legmegfelelőbb profilt. A profil a forgalmi változások időbeli változásával változik.
- **Többrétegű védelem:** Teljes verem DDoS-védelmet biztosít a webalkalmazási tűzfallal való használat során.
- **Széleskörű kockázatcsökkentő skála:** Több mint 60 különböző támadási típust lehet mérsékelni, globális kapacitással, hogy védelmet nyújtson a legnagyobb ismert DDoS-támadásokkal szemben.
- **Támadási elemzés:** A támadás során öt percenkénti lépésekben részletes jelentéseket kaphat, és a támadás befejezése után teljes összefoglalás is elérhető. A stream-elhárítási folyamat naplóit egy offline biztonsági információ-és eseménykezelő (SIEM) rendszerbe helyezi, közel valós idejű figyeléshez a támadás során.
- **Támadási metrikák:** Az egyes támadások összesített metrikái a Azure Monitoron keresztül érhetők el.
- **Támadási riasztások:** A riasztások a támadás indításakor és leállításakor, valamint a támadás időtartamán keresztül konfigurálhatók a beépített támadási metrikák használatával. A riasztások integrálva vannak az operatív szoftverbe, például Microsoft Azure a monitorozási naplók, a splunk, az Azure Storage, az E-mail és a Azure Portal.
- **Cost garancia:** Adatátviteli és alkalmazás-kibővített szolgáltatási kreditek a dokumentált DDoS-támadásokhoz.

## <a name="ddos-protection-standard-mitigation"></a>DDoS Protection standard enyhítés

DDoS Protection standard figyeli a tényleges forgalom kihasználtságát, és folyamatosan összehasonlítja a DDoS-szabályzatban meghatározott küszöbértékekkel. Ha túllépi a forgalom küszöbértékét, a DDoS-megoldás automatikusan indul el. Ha a forgalom a küszöbérték alatti értéket adja vissza, a rendszer eltávolítja a megoldást.

![Kezelés](./media/ddos-protection-overview/mitigation.png)

A mérséklés során a rendszer átirányítja a védett erőforrásnak továbbított forgalmat a DDoS Protection szolgáltatástól, és számos ellenőrzést végez, például a következő ellenőrzéseket:

- Győződjön meg arról, hogy a csomagok megfelelnek az Internet-specifikációknak, és nem hibásak.
- Az ügyféllel együttműködve megállapíthatja, hogy a forgalom potenciálisan hamis csomag-e (pl.: SYN Auth vagy SYN cookie, vagy egy csomag eldobása a forrás számára az újraküldéshez).
- Díjszabás – korlátozza a csomagokat, ha nem hajtható végre más kényszerítési módszer.

A DDoS Protection blokkolja a támadó adatforgalmat, a fennmaradó forgalmat pedig az eredeti célhelyre továbbítja. Az észlelt támadásokról az Azure Monitor metrikái néhány percen belül értesítést küldenek. Ha a naplózást DDoS Protection standard telemetria konfigurálja, a naplókat a jövőbeli elemzésekhez elérhető lehetőségek közül is megírhatja. DDoS Protection standard esetén a Azure Monitor metrikus adatokat 30 napig őrzi meg a rendszer.

A Microsoft a BreakingPoint- [felhővel](https://www.ixiacom.com/products/breakingpoint-cloud) együttműködve olyan felületet hoz létre, amely a szimulációk számára DDoS Protection-kompatibilis nyilvános IP-címekkel való adatforgalmat is létrehoz. A töréspontok Felhőbeli szimulációja a következőket teszi lehetővé:

- Annak ellenőrzése, hogy a Microsoft Azure DDoS Protection standard Hogyan védi az Azure-erőforrásokat a DDoS-támadásoktól
- Optimalizálja az incidensek megválaszolásának folyamatát a DDoS-támadás alatt
- Dokumentum DDoS-megfelelősége
- A hálózati biztonsági csapatok betanítása

## <a name="next-steps"></a>További lépések

- [DDoS Protection standard konfigurálása](manage-ddos-protection.md)
