---
title: A standard szintű Azure DDoS Protection áttekintése | Microsoft Docs
description: Ismerkedjen meg a Azure DDoS Protection szolgáltatással.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/13/2018
ms.author: kumud
ms.openlocfilehash: 5c964eaf3cae4f4f47724c59caf9ff60d9f4d2cd
ms.sourcegitcommit: e1b6a40a9c9341b33df384aa607ae359e4ab0f53
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/27/2019
ms.locfileid: "71336301"
---
# <a name="azure-ddos-protection-standard-overview"></a>Azure DDoS Protection standard áttekintése

Az elosztott szolgáltatásmegtagadásos (DDoS-) támadások az egyik legnagyobb rendelkezésreállási és biztonsági kockázatot jelentik az olyan felhasználók számára, akik alkalmazásaikat a felhőbe helyezik át. A DDoS-támadások megpróbálják kimeríteni az alkalmazás erőforrásait, elérhetetlenné téve az alkalmazást a jogosult felhasználók számára. A DDoS-támadások bármilyen, az interneten keresztül nyilvánosan elérhető végpontot megcélozhatnak.

Az Azure DDoS Protection az alkalmazások kialakításával kapcsolatos ajánlott eljárásokkal kombinálva védelmet nyújt a DDoS-támadásokkal szemben. Az Azure DDoS Protection a következő szolgáltatási szinteket biztosítja:

- Alapszintű: Az Azure platform részeként automatikusan engedélyezve van. A forgalom folyamatos monitorozása és a gyakori hálózati szintű támadások valós idejű enyhítése biztosítja a Microsoft online szolgáltatások által használt védelmi adatokat. Az Azure globális hálózatának teljes skálája felhasználható a különböző régiók közötti támadási forgalom elosztására és enyhítésére. A védelem az IPv4-és IPv6-alapú Azure [nyilvános IP-címekhez](virtual-network-public-ip-address.md)van megadva.
- **Standard**: További enyhítő képességeket biztosít az alapszintű szolgáltatási szinten, amelyek kifejezetten az Azure Virtual Network erőforrásaihoz vannak hangolva. DDoS Protection a standard egyszerűen engedélyezhető, és nem igényel alkalmazás-módosítást. A védelmi szabályzatok a dedikált forgalom monitorozásával és a gépi tanulási algoritmusokkal vannak összehangolva. A házirendeket a rendszer a virtuális hálózatokban üzembe helyezett erőforrásokhoz társított nyilvános IP-címekre alkalmazza, például az Azure Load Balancer, az Azure Application Gateway és az Azure Service Fabric példányokban, de ez a védelem nem vonatkozik App Service környezetekre. A valós idejű telemetria a támadás során Azure Monitor nézeteken keresztül érhető el, és az előzményekhez. A hatékony támadások enyhítése diagnosztikai beállításokon keresztül érhető el. Az alkalmazások rétegének védelme az [azure Application Gateway webalkalmazási tűzfalon](../application-gateway//application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) keresztül, vagy egy külső gyártótól származó tűzfal az Azure Marketplace-ről való telepítésével adható hozzá. A védelem az IPv4-és IPv6-alapú Azure [nyilvános IP-címekhez](virtual-network-public-ip-address.md)van megadva.

|Funkció                                         |DDos Protection – alapszintű                 |DDos Protection standard                      |
|------------------------------------------------|--------------------------------------|----------------------------------------------|
|Aktív forgalom figyelése & always on észlelés |Igen                                   |Igen                                           |
|Automatikus támadás-enyhítés                    |Igen                                   |Igen                                           |
|Rendelkezésre állási garancia                          |Azure-régió                          |Alkalmazás                                   |
|Kockázatcsökkentő házirendek                             |Az Azure-beli forgalom régiójához hangolva |Az alkalmazások forgalmának mennyiségére hangolva          |
|Metrikák és értesítések                                |Nem                                    |Valós idejű támadási mérőszámok & diagnosztikai naplók az Azure monitoron keresztül                                 |
|Kockázatcsökkentő jelentések                              |Nem                                    |Támadás utáni kockázatcsökkentő jelentések                |
|A kockázatcsökkentő folyamat naplói                            |Nem                                    |VIZSGÁLJA-naplózási stream a SIEM-integrációhoz           |
|Áttelepítési szabályzat testreszabása                 |Nem                                    |DDos-szakértők részvétele                           |
|Támogatás                                         |Legjobb erőfeszítés                           |Hozzáférés a DDOs-szakértőkhöz aktív támadás során|
|SLA                                             |Azure-régió                          |Alkalmazás-garancia & Cost Protection       |
|Díjszabás                                         |Free                                  |Havi & használat alapján                         |

## <a name="types-of-ddos-attacks-that-ddos-protection-standard-mitigates"></a>A standard megoldásokkal DDoS Protection DDoS-támadások típusai

DDoS Protection a standard a következő típusú támadásokat csökkentheti:

- **Térfogatos támadások**: A támadás célja, hogy elárasztsa a hálózati réteget, amely jelentős mennyiségű látszólag megbízható adatforgalommal rendelkezik. Ide tartozik az UDP-árvizek, az erősítési árvizek és a hamis csomagokra vonatkozó egyéb áradások. DDoS Protection standard az Azure globális hálózati skálázásával automatikusan felhasználhatja ezeket a lehetséges több gigabájtos támadásokat.
- **Protokollok elleni támadások**: Ezek a támadások nem hozzáférhetővé teszik a célt, mert a 3. rétegbeli és a 4. rétegbeli protokollbeli verem gyengeségét kihasználva. Ez magában foglalja a SYN FLOOD támadásokat, a reflexiós támadásokat és más protokollok elleni támadásokat. A DDoS Protection standard csökkenti ezeket a támadásokat, megkülönbözteti a kártékony és a legitim forgalmat, az ügyféllel való interakcióval és a rosszindulatú forgalom blokkolásával. 
- **Erőforrás (alkalmazás) rétegbeli támadások**: Ezek a támadások a webalkalmazási csomagokat célozzák meg a gazdagépek közötti adatátvitel megzavarásához. A támadások közé tartoznak a HTTP-protokollok megsértése, az SQL-injektálás, a helyközi parancsfájlkezelés és a 7. rétegbeli támadások. Az Azure [Application Gateway webalkalmazási tűzfal](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)DDoS Protection standard szintű használatával biztosít védelmet a támadásokkal szemben. Az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)-en a harmadik féltől származó webalkalmazási tűzfalak is elérhetők.

DDoS Protection standard védi a virtuális hálózatok erőforrásait, beleértve a virtuális gépekhez, a terheléselosztóhoz és az Application gatewayhez társított nyilvános IP-címeket is. Ha a Application Gateway webalkalmazási tűzfallal párosul, az DDoS Protection standard teljes 3. rétegbeli és 7. rétegbeli kockázatcsökkentő képességet is biztosít.

## <a name="ddos-protection-standard-features"></a>DDoS Protection standard funkciók

![DDoS-funkciók](./media/ddos-protection-overview/ddosfeatures.png)

DDoS Protection a standard funkciók a következők:

- **Natív platform-integráció:** Natív módon integrálva az Azure-ba. A Azure Portal konfigurációját tartalmazza. A DDoS Protection standard értelmezése az erőforrásokat és az erőforrás-konfigurációt.
- **Kulcsrakész védelem:** Az egyszerűsített konfiguráció azonnal megvédi a virtuális hálózat összes erőforrását, amint DDoS Protection standard engedélyezve van. Nincs szükség beavatkozásra vagy felhasználói definícióra. A rendszer azonnal DDoS Protection a standardot, és automatikusan csökkenti a támadásokat, amint azt észlelte.
- **Folyamatos forgalom figyelése:** Az alkalmazás forgalmi mintáit napi 24 órában, a hét 7 napján, a DDoS-támadások mutatóit keresve figyeli. A rendszer a védelmi házirendek túllépése esetén csökkenti a megoldást.
- **Adaptív hangolás:** Az intelligens forgalmi profilkészítés az idő múlásával megtanulja az alkalmazás forgalmát, és kiválasztja és frissíti a szolgáltatás számára legmegfelelőbb profilt. A profil a forgalmi változások időbeli változásával változik.
- **Többrétegű védelem:** Teljes verem DDoS-védelmet biztosít a webalkalmazási tűzfallal való használat során.
- **Széleskörű kockázatcsökkentő skála:** Több mint 60 különböző támadási típust lehet mérsékelni, globális kapacitással, hogy védelmet nyújtson a legnagyobb ismert DDoS-támadásokkal szemben.
- **Támadási elemzés:** Támadás idején ötpercenként részletes jelentéseket, majd a támadás végén egy teljes összegzést kap. A stream-elhárítási folyamat naplóit egy offline biztonsági információ-és eseménykezelő (SIEM) rendszerbe helyezi, közel valós idejű figyeléshez a támadás során.
- **Támadási metrikák:** Az egyes támadások összesített metrikái a Azure Monitoron keresztül érhetők el.
- **Támadási riasztások:** A riasztások a támadás indításakor és leállításakor, valamint a támadás időtartamán keresztül konfigurálhatók a beépített támadási metrikák használatával. A riasztások integrálva vannak az operatív szoftverbe, például Microsoft Azure a monitorozási naplók, a splunk, az Azure Storage, az E-mail és a Azure Portal.
- **Cost garancia:** Adatátviteli és alkalmazás-kibővített szolgáltatási kreditek a dokumentált DDoS-támadásokhoz.

## <a name="ddos-protection-standard-mitigation"></a>DDoS Protection standard enyhítés

DDoS Protection standard figyeli a tényleges forgalom kihasználtságát, és folyamatosan összehasonlítja a DDoS-szabályzatban meghatározott küszöbértékekkel. Ha túllépi a forgalom küszöbértékét, a DDoS-megoldás automatikusan indul el. Ha a forgalom a küszöbérték alatti értéket adja vissza, a rendszer eltávolítja a megoldást.

![Kezelés](./media/ddos-protection-overview/mitigation.png)

A mérséklés során a rendszer átirányítja a védett erőforrásnak továbbított forgalmat a DDoS Protection szolgáltatástól, és számos ellenőrzést végez, például a következő ellenőrzéseket:

- Győződjön meg arról, hogy a csomagok megfelelnek az Internet-specifikációknak, és nem hibásak.
- Az ügyféllel együttműködve megállapíthatja, hogy a forgalom potenciálisan hamis csomag-e (például: SYN Auth vagy SYN cookie, vagy egy csomag eldobása a forrás újraküldéséhez.
- Díjszabás – korlátozza a csomagokat, ha nem hajtható végre más kényszerítési módszer.

A DDoS Protection blokkolja a támadási forgalmat, és továbbítja a fennmaradó forgalmat a kívánt célra. A támadások észlelése néhány percen belül Azure Monitor metrikák használatával értesíti Önt. Ha a naplózást DDoS Protection standard telemetria konfigurálja, a naplókat a jövőbeli elemzésekhez elérhető lehetőségek közül is megírhatja. DDoS Protection standard esetén a Azure Monitor metrikus adatokat 30 napig őrzi meg a rendszer.

A Microsoft a BreakingPoint- [felhővel](https://www.ixiacom.com/products/breakingpoint-cloud) együttműködve olyan felületet hoz létre, amely a szimulációk számára DDoS Protection-kompatibilis nyilvános IP-címekkel való adatforgalmat is létrehoz. A töréspontok Felhőbeli szimulációja a következőket teszi lehetővé:

- Annak ellenőrzése, hogy a Microsoft Azure DDoS Protection standard Hogyan védi az Azure-erőforrásokat a DDoS-támadásoktól
- Optimalizálja az incidensek megválaszolásának folyamatát a DDoS-támadás alatt
- Dokumentum DDoS-megfelelősége
- A hálózati biztonsági csapatok betanítása

## <a name="next-steps"></a>További lépések

- [DDoS Protection standard konfigurálása](manage-ddos-protection.md)
