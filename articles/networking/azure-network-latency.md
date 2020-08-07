---
title: Azure-hálózat – az utazás késési statisztikája | Microsoft Docs
description: Ismerje meg az Azure-régiók közötti, az utakra vonatkozó késések statisztikáit.
services: networking
author: nayak-mahesh
ms.service: virtual-network
ms.topic: article
ms.date: 08/05/2020
ms.author: kumud
ms.openlocfilehash: 1898bcf619f1fc9aaf19384968225e35c9589688
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2020
ms.locfileid: "87847438"
---
# <a name="azure-network-round-trip-latency-statistics"></a>Azure-alapú hálózati időszeletelési késések statisztikája

Az Azure folyamatosan figyeli a hálózat alapvető területeinek késését (sebességét) a belső figyelési eszközökkel, valamint a [ThousandEyes](https://thousandeyes.com)által gyűjtött, harmadik féltől származó szintetikus megfigyelési szolgáltatással.

## <a name="how-are-the-measurements-collected"></a>Hogyan történik a mérések gyűjtése?

A késési mérések gyűjtése a ThousandEyes-ügynököktől történik, amelyek világszerte Azure Cloud-régiókban futnak, amelyek folyamatosan küldenek hálózati mintavételt egymás között 1 percenként. A havi késési statisztikát a havi begyűjtött minták átlaga alapján kell származtatni.

## <a name="july-2020-round-trip-latency-figures"></a>2020. július kerekítési késési számok

Az Azure-régiók közötti, az elmúlt 31 napban (2020. július 31-én) végződő havi átlagos menettérti időpontok alább láthatók. A [ThousandEyes](https://thousandeyes.com)a következő méréseket látja el.

[![Az Azure régiók közötti késési statisztikái](media/azure-network-latency/azure-network-latency.png)](media/azure-network-latency/azure-network-latency.png#lightbox)

## <a name="next-steps"></a>Következő lépések

Ismerje meg az [Azure-régiókat](https://azure.microsoft.com/global-infrastructure/regions/).
