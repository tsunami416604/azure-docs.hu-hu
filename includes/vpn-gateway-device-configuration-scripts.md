---
title: fájl belefoglalása
description: fájl belefoglalása
services: vpn-gateway
author: cherylmc
ms.service: vpn-gateway
ms.topic: include
ms.date: 03/30/2018
ms.author: cherylmc
ms.custom: include file
ms.openlocfilehash: 00889717e0c22477b9933725bccc7de05c82bc4f
ms.sourcegitcommit: 7c4fd6fe267f79e760dc9aa8b432caa03d34615d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47454561"
---
| **Szállító** | **Eszközcsalád** | **Belső vezérlőprogram verziója** |
| --- | --- | --- |
|Cisco | ISR| IOS 15.1 (előzetes verzió)|
|Cisco | ASA | Az alábbi 9.8 ASA ASA (*) RouteBased (IKEv2-nincs BGP) |
|Cisco | ASA | Az ASA 9.8 + ASA RouteBased (IKEv2 - nincs BGP) |
|Juniper | SRX_GA | 12.x|
|Juniper | SSG_GA | ScreenOS 6.2.x|
|Juniper | JSeries_GA | JunOS 12.x|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased VTI|
|Ubiquiti| EdgeRouter| EdgeOS v1.10x RouteBased BGP|

> [!NOTE]
> (*) Kötelező: NarrowAzureTrafficSelectors (UsePolicyBasedTrafficSelectors beállítás engedélyezése) és CustomAzurePolicies (IKE/IPsec)
>
