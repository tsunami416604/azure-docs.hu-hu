---
title: Az Azure CDN gyorsítótárazási házirend a az Azure Media Services kezelése |} A Microsoft Docs
description: Ismerje meg, hogyan kezelheti az Azure CDN gyorsítótárazási házirend az Azure Media Servicesben.
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
ms.openlocfilehash: a747e5064ff0ef99fb43c545c29c2a34535445d0
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57540148"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Az Azure CDN gyorsítótárazási házirend a az Azure Media Services kezelése
Az Azure Media Services biztosítja a HTTP-alapú adaptív Streamelés és a progresszív letöltés. HTTP-alapú streamelési az előnyökkel, a gyorsítótárazás a proxy- és a CDN-rétegek, valamint a ügyféloldali gyorsítótárazás rugalmasan méretezhető. Streamvégpontok nyújt általános idejű közvetítési lehetőségeket, és HTTP-fejlécek gyorsítótár konfigurációját. Streamvégpontok beállítja a HTTP Cache-Control: max-age és Expires fejlécet. További információt a HTTP-gyorsítótár fejléceket [W3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html).

## <a name="default-caching-headers"></a>Alapértelmezett gyorsítótárazási fejlécek
Alapértelmezés szerint a streamelési végpontokkal 3 nap gyorsítótár fejlécek igény szerinti folyamatos átviteli adatok (tényleges media töredék/adattömbök) és az manifest(playlist) alkalmazza. Az élő streameléshez streamvégpontok 3 nap gyorsítótár fejlécek adatok (tényleges media töredék/adattömbök) vonatkoznak, és 2 másodperc gyorsítótár manifest(playlist) kérelem fejléce. Ha élő program kerül, igény szerinti (élő archív), majd igény szerinti folyamatos átviteli gyorsítótár fejlécek érvényesek lesznek.

## <a name="azure-cdn-integration"></a>Az Azure CDN-integráció
Az Azure Media Services modullal járulékos konfigurálás [integrált CDN](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) streamelési végpontok. A cache-control fejléceket irányadó ugyanúgy streamvégpontok CDN engedélyezve van a streamvégpont. Streamvégpont Azure CDN az konfigurálva gyorsítótár értékek meghatározásához a belső használatú gyorsítótárazott objektumok élettartama, és ezt az értéket is használ a kézbesítési gyorsítótár fejlécek beállítása. Ha a CDN szolgáltatás használata engedélyezett streamvégpontok nem ajánlott kis méretű gyorsítótár értékeinek beállításához. Kis-értékek beállításával csökkentheti a teljesítményt, és csökkentheti a CDN előnyeit. Kisebb, mint 600 másodperc CDN engedélyezve van a streamvégpontok gyorsítótár fejlécek beállítása nem engedélyezett.

> [!IMPORTANT]
>Az Azure Media Services az Azure CDN a teljes integrációs rendelkezik. Egyetlen kattintással az összes elérhető az Azure CDN-szolgáltató integrálható a streamvégpont, beleértve a standard és prémium szintű termékeket. További információkért lásd: Ez [közlemény](https://azure.microsoft.com/blog/standardstreamingendpoint/).
> 
> A streamvégpontot CDN adatforgalmi díjat csak lekérdezi érhető el, ha a CDN engedélyezve van a streamvégpont API-k vagy a streamelés szakaszt az Azure Portalon a végpont használatával. Manuális integrációs vagy közvetlenül a CDN API-k vagy a portál a szakasz a CDN-végpont létrehozása nem tiltsa le a díjat.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Gyorsítótár-fejlécek konfigurálása az Azure Media Services
Az Azure portal vagy az Azure Media Services API-k segítségével állíthatja be a gyorsítótár fejléc értékét.

1. Konfigurálhatja az Azure portal használatával gyorsítótár-fejléceket, tekintse meg [adatfolyam-továbbítási végpontok kezelése hogyan](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) szakasz a Streamvégpont konfigurálása.
2. Az Azure Media Services REST API-t [Streamvégpontok](https://msdn.microsoft.com/library/azure/dn783468.aspx#StreamingEndpointCacheControl).
3. Az Azure Media Services .NET SDK- [StreamingEndpointCacheControl tulajdonságok](https://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Gyorsítótár konfigurációs sorrendet
1. Az Azure Media Services konfigurált gyorsítótár érték felülbírálja az alapértelmezett értéket.
2. Ha nem tartozik konfiguráció manuális, alapértelmezett értékeket a alkalmazni.
3. 2 másodperc gyorsítótára alapértelmezés szerint a fejlécek élő streamelési manifest(playlist) függetlenül az Azure Media vagy az Azure Storage-konfiguráció vonatkozik, és felülbírálja ezt az értéket nem érhető el.

