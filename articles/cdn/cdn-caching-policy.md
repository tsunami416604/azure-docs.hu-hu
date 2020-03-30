---
title: Az Azure CDN gyorsítótárazási házirendjének kezelése az Azure Media Servicesszolgáltatásban | Microsoft dokumentumok
description: Ez a cikk bemutatja, hogyan kezelheti az Azure CDN-gyorsítótárazási szabályzatot az Azure Media Servicesben.
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
ms.openlocfilehash: dc0482fbcbb1c9d1618ec18e1f48b03f686a6573
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74892579"
---
# <a name="manage-azure-cdn-caching-policy-in-azure-media-services"></a>Azure CDN-gyorsítótárazási házirend kezelése az Azure Media Servicesben
Az Azure Media Services HTTP-alapú adaptív streamelést és progresszív letöltést biztosít. A HTTP-alapú streamelés nagymértékben méretezhető a proxy- és CDN-rétegekben való gyorsítótárazás, valamint az ügyféloldali gyorsítótárazás előnyeivel. A streamelési végpontok általános streamelési lehetőségeket és http-gyorsítótár-fejlécek konfigurációját biztosítják. A streamelési végpontok http-gyorsítótár-vezérlést állítanak be: max-age és expires fejlécek. A HTTP-gyorsítótárfejlécekkel kapcsolatos további információkat [a W3.org](https://www.w3.org/Protocols/rfc2616/rfc2616-sec13.html)kaphat.

## <a name="default-caching-headers"></a>Alapértelmezett gyorsítótárazási fejlécek
Alapértelmezés szerint a streamelési-végpontok 3 napos gyorsítótár-fejléceket alkalmaznak az igény szerinti streamelési adatokra (tényleges médiatöredékek/adattömbök) és a jegyzékfájlra(playlist). Az élő streameléshez a streamelési végpontok 3 napos gyorsítótár-fejléceket alkalmaznak az adatokhoz (tényleges adathordozó-töredékek/adattömbök) és 2 másodpercnyi gyorsítótárfejlécet a manifest(playlist) kérelmekhez. Amikor az élő program igény szerint (élő archívum) fordul, akkor az igény szerinti streamelési gyorsítótárfejlécek érvényesek.

## <a name="azure-cdn-integration"></a>Azure CDN-integráció
Az Azure Media Services [integrált CDN-t](https://azure.microsoft.com/updates/azure-media-services-now-fully-integrated-with-azure-cdn/) biztosít a streamelési végpontokhoz. A gyorsítótár-vezérlő fejlécek ugyanúgy érvényesek, mint a CDN-kompatibilis streamelési végpontok streamelési végpontjai. Az Azure CDN streamelési végpontkonfigurált gyorsítótár-értékeket használ a belső gyorsítótárazott objektumok élettartamának meghatározásához, és ezt az értéket használja a kézbesítési gyorsítótár fejléceinek beállításához. CdN-kompatibilis streamelési végpontok használata esetén nem ajánlott kis gyorsítótár-értékeket beállítani. A kis értékek beállítása csökkenti a teljesítményt és csökkenti a CDN előnyeit. A CDN-kompatibilis streamelési végpontok esetében nem állítható 600 másodpercnél kisebb gyorsítótár-fejlécek.

> [!IMPORTANT]
>Az Azure Media Services teljes integrációt kínál az Azure CDN-nel. Egyetlen kattintással integrálhatja az összes rendelkezésre álló Azure CDN-szolgáltatót a streamelési végpontba, beleértve a standard és prémium termékeket is. További információt ebben a [közleményben](https://azure.microsoft.com/blog/standardstreamingendpoint/)talál.
> 
> A streamelési végpontról a CDN-re vonatkozó adatforgalmi díjak csak akkor lesz letiltva, ha a CDN engedélyezve van a végponti API-k streamelésén vagy az Azure Portal streamelési végpontszakaszának használatával. Manuális integráció vagy cdn-végpont közvetlen létrehozása CDN API-k vagy portálszakasz használatával nem tiltja le az adatforgalmi díjakat.

## <a name="configuring-cache-headers-with-azure-media-services"></a>Gyorsítótárfejlécek konfigurálása az Azure Media Services szolgáltatással
Az Azure Portal vagy az Azure Media Services API-k segítségével konfigurálhatja a gyorsítótár fejlécértékeit.

1. A gyorsítótár-fejlécek konfigurálásához az Azure Portal használatával, olvassa el a [Streamelési végpontok kezelése](../media-services/previous/media-services-portal-manage-streaming-endpoints.md) szakasz konfigurálása a streamelési végpont.
2. Azure Media Services REST API, [StreamingEndpoint](/rest/api/media/operations/streamingendpoint#StreamingEndpointCacheControl).
3. Az Azure Media Services .NET SDK, [StreamingEndpointCacheControl tulajdonságai](https://go.microsoft.com/fwlink/?LinkId=615302).

## <a name="cache-configuration-precedence-order"></a>Gyorsítótár-konfiguráció prioritási sorrendje
1. Az Azure Media Services konfigurált gyorsítótár-értéke felülbírálja az alapértelmezett értéket.
2. Ha nincs manuális konfiguráció, az alapértelmezett értékek érvényesek.
3. Alapértelmezés szerint 2 másodperces gyorsítótár-fejlécek vonatkozik az élő streamelési jegyzékfájl(playlist), függetlenül az Azure Media vagy az Azure Storage konfigurációját, és ennek az értéknek az felülbírálása nem érhető el.

