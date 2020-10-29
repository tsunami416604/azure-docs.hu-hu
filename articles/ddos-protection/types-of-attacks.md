---
title: A támadások típusai Azure DDoS Protection standard enyhítés
description: Ismerje meg, milyen típusú támadások Azure DDoS Protection a standard védelmet.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 50dd5cf9a51ebcc6b2df188838ef2910afcd224d
ms.sourcegitcommit: 693df7d78dfd5393a28bf1508e3e7487e2132293
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/28/2020
ms.locfileid: "92905504"
---
# <a name="types-of-ddos-attacks-overview"></a>A DDoS-támadások típusai – áttekintés

DDoS Protection a standard a következő típusú támadásokat csökkentheti:

- **Térfogatos támadások** : ezek a támadások jelentős mennyiségű látszólag megbízható forgalommal árasztják el a hálózati réteget. Ilyenek például az UDP-árvizek, az erősítési árvizek és más hamisítási csomagok. DDoS Protection standard az Azure globális hálózati skálázásával automatikusan felhasználhatja ezeket a lehetséges több gigabájtos támadásokat.
- **Protokollok elleni támadások** : ezek a támadások a 3. rétegbeli és a 4. rétegbeli protokollkészlet gyengeségének kihasználásával elérhetetlenné teszik a célt. Ilyenek például a SYN FLOOD támadások, a reflexiós támadások és más protokollok elleni támadások. A DDoS Protection standard csökkenti ezeket a támadásokat, megkülönbözteti a kártékony és a legitim forgalmat, az ügyféllel való interakcióval és a rosszindulatú forgalom blokkolásával. 
- **Erőforrás (alkalmazás) rétegbeli támadások** : ezek a támadások a webalkalmazási csomagokat célozzák meg a gazdagépek közötti adatátvitel megzavarásához. Ilyenek például a HTTP protokoll megsértései, az SQL-injektálás, a helyek közötti parancsfájlkezelés és a 7. rétegbeli támadások. Használjon webalkalmazási tűzfalat, például az Azure [Application Gateway webalkalmazási tűzfalat](../application-gateway/application-gateway-web-application-firewall-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json), valamint DDoS Protection standardot a támadások elleni védelem biztosításához. Az [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=web%20application%20firewall)-en a harmadik féltől származó webalkalmazási tűzfalak is elérhetők.

## <a name="azure-ddos-protection-standard"></a>Azure DDoS Protection Standard

DDoS Protection standard védi a virtuális hálózatok erőforrásait, beleértve a virtuális gépekhez, a terheléselosztóhoz és az Application gatewayhez társított nyilvános IP-címeket is. Ha a Application Gateway webalkalmazási tűzfallal párosul, vagy egy nyilvános IP-címmel rendelkező virtuális hálózatban üzembe helyezett, harmadik féltől származó webalkalmazási tűzfal, DDoS Protection a standard teljes 3. rétegbeli, 7. rétegbeli kockázatcsökkentő képességet biztosít.

## <a name="next-steps"></a>Következő lépések

- Megtudhatja, hogyan [hozhat létre DDoS Protection-tervet](manage-ddos-protection.md).