---
title: Az Azure Network Round Trip késési statisztikái | Microsoft Docs
description: Ismerje meg az Azure-régiók közötti, az utazások késésének statisztikai adatait.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 03/10/2020
ms.author: kumud
ms.openlocfilehash: d9cae04499f046749e504bcab89b893fcc31a81c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80886949"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Az Azure Network Round Trip késési statisztikái

Az Azure folyamatosan figyeli a hálózat alapvető területeinek késését (sebességét) a belső figyelési eszközökkel, valamint a [ThousandEyes](https://thousandeyes.com)által gyűjtött, harmadik féltől származó szintetikus megfigyelési szolgáltatással.

## <a name="how-are-the-measurements-collected"></a>Hogyan történik a mérések gyűjtése?

A késési mérések gyűjtése a ThousandEyes-ügynököktől történik, amelyek világszerte Azure Cloud-régiókban futnak, amelyek folyamatosan küldenek hálózati mintavételt egymás között 1 percenként. A havi késési statisztikát a havi begyűjtött minták átlaga alapján kell származtatni.

## <a name="march-2020-round-trip-latency-figures"></a>Március 2020 időarányos késési számok

Az Azure-régiók közötti, az elmúlt 31 napban (2020. március 31-én végződő) havi átlagos menettérti időpontok alább láthatók. A [ThousandEyes](https://thousandeyes.com)a következő méréseket látja el.

[![Az Azure régiók közötti késési statisztikái](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>További lépések

Ismerje meg az [Azure-régiókat](https://azure.microsoft.com/global-infrastructure/regions/).
