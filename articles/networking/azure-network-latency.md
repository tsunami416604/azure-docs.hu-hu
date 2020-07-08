---
title: Az Azure Network Round Trip késési statisztikái | Microsoft Docs
description: Ismerje meg az Azure-régiók közötti, az utazások késésének statisztikai adatait.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 05/26/2020
ms.author: kumud
ms.openlocfilehash: 8cf5d07bb071217a5ecafca8f351c94590291603
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "83994007"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Az Azure Network Round Trip késési statisztikái

Az Azure folyamatosan figyeli a hálózat alapvető területeinek késését (sebességét) a belső figyelési eszközökkel, valamint a [ThousandEyes](https://thousandeyes.com)által gyűjtött, harmadik féltől származó szintetikus megfigyelési szolgáltatással.

## <a name="how-are-the-measurements-collected"></a>Hogyan történik a mérések gyűjtése?

A késési mérések gyűjtése a ThousandEyes-ügynököktől történik, amelyek világszerte Azure Cloud-régiókban futnak, amelyek folyamatosan küldenek hálózati mintavételt egymás között 1 percenként. A havi késési statisztikát a havi begyűjtött minták átlaga alapján kell származtatni.

## <a name="april-2020-round-trip-latency-figures"></a>2020. április az utazás késési számai

Az Azure-régiók közötti, az elmúlt 30 napban (2020. április 30-ig végződő) havi átlagos menettérti időpontok alább láthatók. A [ThousandEyes](https://thousandeyes.com)a következő méréseket látja el.

[![Az Azure régiók közötti késési statisztikái](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>További lépések

Ismerje meg az [Azure-régiókat](https://azure.microsoft.com/global-infrastructure/regions/).
