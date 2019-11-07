---
title: Azure-hálózat – az utazás késési statisztikája | Microsoft Docs
description: Ismerje meg az Azure-régiók közötti, az utakra vonatkozó késések statisztikáit.
services: networking
documentationcenter: na
author: nayak-mahesh
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/04/2019
ms.author: mnayak
ms.openlocfilehash: 500676983233f943fdc9638d75758645dee65564
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73587588"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure-alapú hálózati időszeletelési késések statisztikája

Az Azure folyamatosan figyeli a hálózat alapvető területeinek késését (sebességét) a belső figyelési eszközökkel, valamint a [ThousandEyes](https://thousandeyes.com)által gyűjtött, harmadik féltől származó szintetikus megfigyelési szolgáltatással.

## <a name="how-are-the-measurements-collected"></a>Hogyan történik a mérések gyűjtése?

A késési mérések az Azure-beli felhő-régiókban üzemeltetett ThousandEyes-ügynököktől lesznek gyűjtve, amelyek folyamatosan hálózati mintavételt küldenek egymás között, 1 perces időközönként. A havi késési statisztikát a havi begyűjtött minták átlaga alapján kell származtatni.

## <a name="october-2019-latency-figures"></a>Október 2019 késési számok

A 2019. október 31-ig végződő 31 napos időszakra az összesített régiókban a havi minimális és a maximális időpontra vonatkozó késési idő a következő:

- **5 MS** – **72 MS** , **Észak-Amerika** régión belüli, kerek utakon.
- **3 MS** – **28 MS** az **Európai** régiókban való oda-és beutazáshoz.
- **4 MS** -ról **134 MS** -ra az **ázsiai** régiókon belüli menettérti utakon.

A régiók közötti késési méréseket a [ThousandEyes](https://thousandeyes.com)működteti. Az alábbi táblázatban szereplő mérési egység ezredmásodpercben (MS) van.

![Az Azure régiók közötti késési statisztikái](media/azure-network-latency/azure-inter-region-latency.png)


## <a name="next-steps"></a>További lépések
- Ismerje meg az [Azure-régiókat](https://azure.microsoft.com/global-infrastructure/regions/).