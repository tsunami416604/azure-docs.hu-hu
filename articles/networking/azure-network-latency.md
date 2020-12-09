---
title: Azure-hálózat – az utazás késési statisztikája | Microsoft Docs
description: Ismerje meg az Azure-régiók közötti, az utakra vonatkozó késések statisztikáit.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 12/07/2020
ms.author: kumud
ms.openlocfilehash: fb828d239266691766f55c1b156831afab7cc5bc
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854673"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure-alapú hálózati időszeletelési késések statisztikája

Az Azure folyamatosan figyeli a hálózat alapvető területeinek késését (sebességét) a belső figyelési eszközökkel, valamint a [ThousandEyes](https://thousandeyes.com)által gyűjtött, harmadik féltől származó szintetikus megfigyelési szolgáltatással.

## <a name="how-are-the-measurements-collected"></a>Hogyan történik a mérések gyűjtése?

A késési mérések gyűjtése a ThousandEyes-ügynököktől történik, amelyek világszerte Azure Cloud-régiókban futnak, amelyek folyamatosan küldenek hálózati mintavételt egymás között 1 percenként. A havi késési statisztikát a havi begyűjtött minták átlaga alapján kell származtatni.

## <a name="november-2020-round-trip-latency-figures"></a>November 2020 ciklikus késési számok

Az Azure-régiók közötti, az elmúlt 30 napban (2020. november 30-án végződő) havi átlagos menettérti időpontok alább láthatók. A [ThousandEyes](https://thousandeyes.com)a következő méréseket látja el.

[![Az Azure régiók közötti késési statisztikái](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Következő lépések

Ismerje meg az [Azure-régiókat](https://azure.microsoft.com/global-infrastructure/regions/).
