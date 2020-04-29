---
title: A kínai tartalom kézbesítése Azure CDNvel | Microsoft Docs
description: Ismerkedjen meg az Azure Content Delivery Network (CDN) használatával, hogy tartalmat nyújtson a kínai felhasználóknak.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/16/2018
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: fa144c7ebd68e6f5dd192fca83dc6f306d7b8d63
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81254104"
---
# <a name="china-content-delivery-with-azure-cdn"></a>A kínai tartalom kézbesítése Azure CDN

Az Azure Content Delivery Network (CDN) globálisan Kínában vagy bármely olyan POP-ponttal rendelkező felhasználók számára biztosít tartalmat a kínai felhasználóknak, akik a legjobb teljesítményt nyújtanak a Kínából származó kérelmek esetében. Ha azonban Kína jelentős piacot biztosít ügyfelei számára, és a gyors teljesítményre van szükségük, érdemes inkább a Azure CDN Kínában használni.

Azure CDN Kína különbözik a Azure CDN globálistól, hogy a tartalmakat a kínai pop-ról származó, számos helyi szolgáltatóval való partneri kapcsolattal biztosítja. A kínai megfelelőség és szabályozás miatt külön előfizetést kell regisztrálnia Azure CDN Kínában való használatra, és a webhelyeknek ICP-licenccel kell rendelkezniük. A tartalmak kézbesítésének engedélyezéséhez és kezeléséhez szükséges portál és API-élmény a Azure CDN globális és Azure CDN Kínában is megegyeznek.

## <a name="comparison-of-azure-cdn-global-and-azure-cdn-china"></a>A Azure CDN globális és Azure CDN Kínában való összehasonlítása

Azure CDN a Global és a Azure CDN China a következő funkciókat tartalmazza:

- Globális Azure CDN:

     - Portálhttps://portal.azure.com  

     - A tartalom átvitelét Kínában kívül végzi

     - Négy díjszabási szint: Microsoft standard, Verizon standard, Verizon Premium és Akamai standard

     - [Dokumentáció](https://docs.microsoft.com/azure/cdn/)

- Azure CDN Kínában:

     - Portálhttps://portal.azure.cn

     - Elvégzi a tartalom továbbítását Kínában

     - Két díjszabási szint: standard és prémium

     - [Dokumentáció](https://docs.azure.cn/en-us/cdn/)
 

## <a name="next-steps"></a>További lépések

Ha többet szeretne megtudni a Azure CDN Kínával kapcsolatban, tekintse meg a következőt:

- [Content Delivery Network funkciók](https://www.azure.cn/en-us/home/features/cdn/)

- [Az Azure Content Delivery Network áttekintése](https://docs.azure.cn/en-us/cdn/cdn-overview)

- [Az Azure Content Delivery Network használata](https://docs.azure.cn/en-us/cdn/cdn-how-to-use)

- [Azure-szolgáltatás elérhetősége Kínában](https://docs.microsoft.com/azure/china/concepts-service-availability)



