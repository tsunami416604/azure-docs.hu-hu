---
title: Kína tartalomkézbesítés az Azure CDN-nel | Microsoft dokumentumok
description: Ismerje meg, hogy miként kézbesíti a tartalmat a kínai felhasználók nak az Azure Content Delivery Network (CDN) használatával.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: a63914116f5ef9922ac05745764e0ad505dc3c91
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593951"
---
# <a name="china-content-delivery-with-azure-cdn"></a>Kína tartalomkézbesítés az Azure CDN-nel

Az Azure Content Delivery Network (CDN) globális tartalommal szolgálhat a kínai felhasználók számára a Kínához közeli POP-helyekkel vagy bármely olyan POP-mal, amely a legjobb teljesítményt nyújtja a Kínából származó kéréseknek. Ha azonban Kína jelentős piac az ügyfelek számára, és gyors teljesítményre van szükségük, fontolja meg az Azure CDN China használatát.

Az Azure CDN China annyiban különbözik az Azure CDN globaltól, hogy számos helyi szolgáltatóval együttműködve szállít jattot a Kínán belüli POP-okból. A kínai megfelelőség és szabályozás miatt külön előfizetést kell regisztrálnia az Azure CDN China használatához, és webhelyeinek ICP-licenccel kell rendelkezniük. A tartalomkézbesítés engedélyezéséhez és kezeléséhez szükséges portál és API-élmény megegyezik az Azure CDN globális és az Azure CDN China szolgáltatással.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>Az Azure CDN globális és az Azure CDN China összehasonlítása

Az Azure CDN global és az Azure CDN China a következő funkciókkal rendelkezik:

- Azure CDN globális:

     - Portál:https://portal.azure.com  

     - Tartalomkézbesítés tanaként teljesít Kínán kívül

     - Négy árképzési szint: Microsoft szabvány, Verizon szabvány, Verizon prémium és Akamai szabvány

     - [Dokumentáció](https://docs.microsoft.com/azure/cdn/)

- Azure CDN Kína:

     - Portál:https://portal.azure.cn

     - Tartalomkézbesítést hajt végre Kínán belül

     - Két tarifacsomag: Standard és prémium

     - [Dokumentáció](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni az Azure CDN China-ról, olvassa el a következő témakört:

- [Tartalomkézbesítési hálózat szolgáltatásai](https://www.azure.cn/en-us/home/features/cdn/)

- [Az Azure Content Delivery Network áttekintése](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Az Azure Content Delivery Network használata](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [Az Azure-szolgáltatások elérhetősége Kínában](https://docs.microsoft.com/azure/china/concepts-service-availability)



