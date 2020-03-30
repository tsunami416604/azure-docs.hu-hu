---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 02/07/2020
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: e84a77629026bb8885a48b8ed928699825f07115
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77111213"
---
| **Szállító** | **Eszközcsalád** | **Belső vezérlőprogram verziója** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (előzetes verzió)|
|Cisco | ASA | ASA ( * ) RouteBased (IKEv2- No BGP) az ASA esetében 9,8 alatt |
|Cisco | ASA | ASA RouteBased (IKEv2 - No BGP) az ASA 9.8+ esetében |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | KépernyőOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Juniper | SRX | JunOS 12.x Útvonalalapú BGP |
|Ubiquiti| EdgeRouter (szélvonal-útválasztó)| EdgeOS v1.10x útvonalalapú VTI|
|Ubiquiti| EdgeRouter (szélvonal-útválasztó)| EdgeOS v1.10x útvonalalapú BGP|

> [!NOTE]
> ( * ) Kötelező: NarrowAzureTrafficSelectors (usePolicyBasedTrafficSelectors option) és CustomAzurePolicies (IKE/IPsec)
>
