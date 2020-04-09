---
title: Az Azure hálózati oda-vissza késési statisztikái | Microsoft dokumentumok
description: Ismerje meg az Azure-régiók közötti oda-vissza késési statisztikáit.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: d9cae04499f046749e504bcab89b893fcc31a81c
ms.sourcegitcommit: d187fe0143d7dbaf8d775150453bd3c188087411
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80886949"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Az Azure hálózati oda-vissza késési statisztikái

Az Azure folyamatosan figyeli a késés (sebesség) a fő területek a hálózat belső figyelési eszközök, valamint a mérések által gyűjtött [ThousandEyes,](https://thousandeyes.com)egy harmadik fél szintetikus figyelési szolgáltatás.

## <a name="how-are-the-measurements-collected"></a>Hogyan történik a mérések gyűjtése?

A késésmérések a ThousandEyes-ügynököktől kerülnek gyűjtésre, amelyek világszerte üzemeltetik az Azure felhőrégióiban, és folyamatosan küldik egymás között a hálózati mintavételeket 1 perces időközönként. A havi késési statisztikák az összegyűjtött minták havi átlagolásából származnak.

## <a name="march-2020-round-trip-latency-figures"></a>Március 2020 oda-vissza látencia adatok

Az Azure-régiók közötti havi átlagos oda-vissza áraaz elmúlt 31 nap (2020. március 31-ig végződő) az alábbiakban látható. A következő mérések powered by [ThousandEyes](https://thousandeyes.com).

[![Az Azure régiók közötti késési statisztikái](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>További lépések

További információ az [Azure-régiókról.](https://azure.microsoft.com/global-infrastructure/regions/)
