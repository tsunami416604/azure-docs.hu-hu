---
title: Azure CDN gyorsítótárazási házirend Azure Media Services kezelése |} Microsoft Docs
description: 'Útmutató: Azure CDN gyorsítótárazási házirend Azure Media Services kezelése.'
services: media-services,cdn
documentationcenter: .NET
author: juliako
manager: erikre
editor: ''
ms.assetid: be33aecc-6dbe-43d7-a056-10ba911e0e94
ms.service: media-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/04/2017
ms.author: juliako
ms.openlocfilehash: dce8d588a78b028223776e3ade737d4abd26094b
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
ms.locfileid: "33765285"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Azure CDN gyorsítótárazási házirend Azure Media Services kezelése
Azure Media Services biztosította HTTP alapú adaptív Streameléshez és progresszív letöltés. A HTTP-alapú adatfolyam magas szinten méretezhető előnyt proxy és a CDN rétegek gyorsítótárazás, valamint a ügyféloldali gyorsítótárazás. Adatfolyam-végpontok nyújt általános adatfolyam képességek, valamint a gyorsítótár HTTP-fejlécek konfigurációját. Adatfolyam-végpontok állítja be a HTTP-Cache-Control: maximális-kor és Expires fejléc. További részleteket a HTTP-gyorsítótár fejléceket [W3.org](http://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Alapértelmezett gyorsítótárazását fejlécek
Alapértelmezés szerint az adatfolyam-végpontok 3 nap gyorsítótár fejlécek az igényalapú streamelési adatok (tényleges media töredék/adattömbök) és manifest(playlist) alkalmazni. Az élő adatfolyamként történő streamvégpontok 3 nap gyorsítótár fejlécek adatok (tényleges media töredék/adattömbök) vonatkoznak, és 2 másodperc gyorsítótár manifest(playlist) kérelem fejléce. Élő program kikapcsolja az igény szerinti (élő archív), majd igény szerinti adatfolyam-továbbítási gyorsítótár fejlécek vonatkoznak.

## <a name="azure-cdn-integration"></a>Azure CDN-integráció
Azure Media Services biztosította [CDN integrált](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) streaming-végpontok. A cache-control fejlécek alkalmazza ugyanúgy CDN streamvégpontok streamvégpontok engedélyezve van. Az Azure CDN által használt adatfolyam-továbbítási végpontra gyorsítótár értékek megadhatók a belső gyorsítótárazott objektumok élettartama idején konfigurálva, és ezt az értéket is használja a kézbesítési gyorsítótár fejlécek beállítása. Streamvégpontok CDN használatával engedélyezése nem ajánlott kis gyorsítótár értékeinek beállításához. Kis beállításértékek csökkentheti a teljesítményt és a CDN előnyeit. Gyorsítótár fejlécek kisebb, mint a CDN 600 másodperc engedélyezett streamvégpontok beállítása nem engedélyezett.

> [!IMPORTANT]
>Azure Media Services Azure CDN teljes integrálva van. Egy kattintással elérhető Azure CDN szolgáltatók integrálhatja a streamvégpontján, beleértve a standard és premium termékek. További információkért tekintse meg a [közlemény](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> Az adatfolyam-továbbítási végpontra CDN adatok díjak csak lekérdezi használható, ha a CDN adatfolyam-továbbítási végpontra API-k vagy az Azure portál adatfolyam végpont szakaszában engedélyezve van. Manuális integráció közvetlenül a CDN API-k vagy a portál szakaszban CDN-végpont létrehozása vagy adatok díjak nem letiltása.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Gyorsítótár-fejlécek konfigurálása az Azure Media Services
Azure-portálon vagy az Azure Media Services API-k segítségével konfigurálhatja a gyorsítótár fejléc értékei.

1. Konfigurálhatja az Azure-portál használatával gyorsítótár-fejléceket, tekintse meg [adatfolyam-végpontok kezelése hogyan](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) szakasz a Streamvégponton konfigurálása.
2. Azure Media Services REST API-t [StreamingEndpoint](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. Azure Media Services .NET SDK-val [StreamingEndpointCacheControl tulajdonságok](http://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Gyorsítótár konfigurációs prioritási sorrendje
1. Az Azure Media Services konfigurált gyorsítótár érték felülbírálja az alapértelmezett érték.
2. Ha nincs kézi konfigurálásra, alapértelmezett értékek érvényesek.
3. Alapértelmezett 2 másodperc gyorsítótár fejlécek vonatkozik élő adatfolyam-továbbítási manifest(playlist) Azure Media vagy az Azure Storage függetlenül, és ennek az értéknek felülbírálása nem érhető el.

