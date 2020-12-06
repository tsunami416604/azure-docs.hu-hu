---
title: Azure DDoS Protection standard áttekintése
description: Ismerje meg, hogy az Azure DDoS Protection Standard a DDoS-támadások elleni védelem érdekében hogyan kombinálható az alkalmazástervezés ajánlott eljárásaival.
services: virtual-network
documentationcenter: na
author: yitoh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/9/2020
ms.author: yitoh
ms.openlocfilehash: 749d8d6edf9c94185c8363c5fec1b91e60ae9272
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2020
ms.locfileid: "96746471"
---
# <a name="azure-ddos-protection-standard-overview"></a>Az Azure DDoS Protection Standard áttekintése

Az elosztott szolgáltatásmegtagadásos (DDoS-) támadások az egyik legnagyobb rendelkezésreállási és biztonsági kockázatot jelentik az olyan felhasználók számára, akik alkalmazásaikat a felhőbe helyezik át. A DDoS-támadás megpróbál kimeríteni egy alkalmazás erőforrásait, így az alkalmazás nem érhető el a legitim felhasználók számára. A DDoS-támadások bármilyen, az interneten keresztül nyilvánosan elérhető végpontot megcélozhatnak.

Az Azure minden tulajdonságát az Azure infrastruktúrájának DDoS (alapszintű) védelme védi, többletköltség nélkül. A globálisan üzembe helyezett Azure-hálózat skálázása és kapacitása védelmet nyújt a közös hálózati rétegbeli támadásokkal szemben a folyamatos forgalom monitorozása és a valós idejű mérséklés révén. DDoS Protection alapszintű beállításhoz nincs szükség felhasználói konfigurációra vagy alkalmazás módosítására. DDoS Protection alapszintű, így biztosítható az összes Azure-szolgáltatás, beleértve a Pásti-szolgáltatásokat, mint a Azure DNS.

A standard szintű, az alkalmazások kialakításával kapcsolatos ajánlott eljárásokkal kombinálva a DDoS-támadások elleni védelem érdekében fejlett DDoS-elhárítási funkciókat biztosít. Azure DDoS Protection A rendszer automatikusan hangolja az adott Azure-erőforrások védelmére egy virtuális hálózaton. A védelem egyszerűen engedélyezhető bármely új vagy meglévő virtuális hálózaton, és nem igényel alkalmazás-vagy erőforrás-módosítást. Az alapszintű szolgáltatás számos előnnyel jár, beleértve a naplózást, a riasztásokat és a telemetria is. 

![Azure DDoS Protection szolgáltatás összehasonlítása](./media/ddos-protection-overview/ddos-comparison.png)

Az Azure DDoS Protection nem tárolja az ügyféladatokat.

## <a name="features"></a>Szolgáltatások

- **Natív platform-integráció:** Natív módon integrálva az Azure-ba. A Azure Portal konfigurációját tartalmazza. A DDoS Protection standard értelmezése az erőforrásokat és az erőforrás-konfigurációt.
- **Kulcsrakész védelem:** Az egyszerűsített konfiguráció azonnal megvédi a virtuális hálózat összes erőforrását, amint DDoS Protection standard engedélyezve van. Nincs szükség beavatkozásra vagy felhasználói definícióra. A rendszer azonnal DDoS Protection a standardot, és automatikusan csökkenti a támadásokat, amint azt észlelte.
- **Folyamatos forgalom figyelése:** Az alkalmazás forgalmi mintáit napi 24 órában, hetente 7 nappal figyelik, a DDoS-támadások mutatóit keresve. A rendszer a védelmi házirendek túllépése esetén csökkenti a megoldást.
- **Adaptív hangolás:** Az intelligens forgalmi profilkészítés az idő múlásával megtanulja az alkalmazás forgalmát, és kiválasztja és frissíti a szolgáltatás számára legmegfelelőbb profilt. A profil a forgalmi változások időbeli változásával változik.
- **Többrétegű védelem:** A teljes verem DDoS-védelmet biztosít webalkalmazási tűzfallal való használat esetén, hogy a hálózati réteg (3. réteg és Azure DDoS Protection 4. réteg) és az alkalmazási réteg (a WAF által kínált 7. réteg) esetében is védelmet kapjon. A WAF-ajánlatok közé tartoznak az Azure [Application Gateway WAF SKU](../web-application-firewall/ag/ag-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) , valamint az [Azure piactéren](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)elérhető, harmadik féltől származó webalkalmazási tűzfalak is.
- **Széleskörű kockázatcsökkentő skála:** Több mint 60 különböző támadási típust lehet mérsékelni, globális kapacitással, hogy védelmet nyújtson a legnagyobb ismert DDoS-támadásokkal szemben.
- **Támadási elemzés:** A támadás során öt percenkénti lépésekben részletes jelentéseket kaphat, és a támadás befejezése után teljes összefoglalás is elérhető. A stream-elhárítási folyamat naplóit az [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md) vagy egy offline biztonsági információ-és ESEMÉNYKEZELŐ (SIEM) rendszer ismerteti a támadás során a közel valós idejű figyeléshez.
- **Támadási metrikák:** Az egyes támadások összesített metrikái a Azure Monitoron keresztül érhetők el.
- **Támadási riasztások:** A riasztások a támadás indításakor és leállításakor, valamint a támadás időtartamán keresztül konfigurálhatók a beépített támadási metrikák használatával. A riasztások integrálva vannak az operatív szoftverbe, például Microsoft Azure a monitorozási naplók, a splunk, az Azure Storage, az E-mail és a Azure Portal.
- **DDoS gyors válasz**: a DDoS Protection Rapid Response (DRR) csapatának segítségére van a támadás kivizsgálásához és elemzéséhez. További információ: [DDoS gyors válasz](ddos-rapid-response.md).
- **Cost garancia:** Adatátviteli és alkalmazás-kibővített szolgáltatási kreditek a dokumentált DDoS-támadásokhoz.

## <a name="pricing"></a>Díjszabás

A standard díjszabással kapcsolatos további információkért tekintse meg a [Azure DDoS Protection standard díjszabást](https://azure.microsoft.com/pricing/details/ddos-protection/)Azure DDoS Protection.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [DDoS Protection terv létrehozása](manage-ddos-protection.md)