---
title: Az Azure hálózati oda-vissza késési statisztikái | Microsoft dokumentumok
description: Ismerje meg az Azure-régiók közötti oda-vissza késési statisztikáit.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: 3469daa8b81b20d5d0052a23ce3236fc4fde75de
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79082938"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Az Azure hálózati oda-vissza késési statisztikái

Az Azure folyamatosan figyeli a késés (sebesség) a fő területek a hálózat belső figyelési eszközök, valamint a mérések által gyűjtött [ThousandEyes,](https://thousandeyes.com)egy harmadik fél szintetikus figyelési szolgáltatás.

## <a name="how-are-the-measurements-collected"></a>Hogyan történik a mérések gyűjtése?

A késésmérések a ThousandEyes-ügynököktől kerülnek gyűjtésre, amelyek világszerte üzemeltetik az Azure felhőrégióiban, és folyamatosan küldik egymás között a hálózati mintavételeket 1 perces időközönként. A havi késési statisztikák az összegyűjtött minták havi átlagolásából származnak.

## <a name="february-2020-round-trip-latency-figures"></a>Február 2020 oda-vissza látencia adatok

Az Azure-régiók közötti havi átlagos oda-vissza áraaz elmúlt 29 nap (2020. február 29-én végződő) átlagos árad. A következő mérések powered by [ThousandEyes](https://thousandeyes.com).

[![Az Azure régiók közötti késési statisztikái](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>További lépések

További információ az [Azure-régiókról.](https://azure.microsoft.com/global-infrastructure/regions/)
