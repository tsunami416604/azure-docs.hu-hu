---
title: Az Azure Content Delivery Network- (CDN-) termékszolgáltatások összehasonlítása | Microsoft Docs
description: A jelen cikkben az egyes Azure Content Delivery Network- (CDN-) termékek által támogatott szolgáltatásokat ismerheti meg.
services: cdn
documentationcenter: ''
author: dksimpson
manager: cfowler
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 05/09/2018
ms.author: v-deasim
ms.custom: mvc
ms.openlocfilehash: 7ec4961a78b2207c9990b787c4a0f9cc9c2e7931
ms.sourcegitcommit: 194789f8a678be2ddca5397137005c53b666e51e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/25/2018
ms.locfileid: "39237114"
---
# <a name="compare-azure-cdn-product-features"></a>Az Azure CDN-termékszolgáltatások összehasonlítása

Az Azure Content Delivery Network (CDN) négy terméket tartalmaz: a **Microsoft Azure CDN Standard** (előnézetben), az **Akamai Azure CDN Standard**, a **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** terméket. 

A **Verizon Azure CDN Standard** típusú profilok **Verizon Azure CDN Premium** típusú profilba történő migrálására vonatkozó információkért lásd az [Azure CDN-profil Standard Verizonból Premium Verizonba történő migrálását](cdn-migrate.md) ismertető cikket.

Az alábbi táblázat az egyes termékek szolgáltatásait hasonlítja össze.

| **Teljesítménnyel kapcsolatos szolgáltatások és optimalizálási lehetőségek** | **Standard Microsoft (előzetes verzió)** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Dinamikus helygyorsítás](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  |  | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dinamikus helygyorsítás – Adaptív képtömörítés](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dinamikus helygyorsítás – Előzetes objektumbetöltés](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-akamai-only)  |  | **&#x2713;**  |  |  |
| [Videóstreamelés optimalizálása](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [Nagyméretű fájlok optimalizálása](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | \* | **&#x2713;**  | \* |  \* |
| [Globális kiszolgálói terheléselosztás (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Gyors végleges törlés](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Objektumok előzetes betöltése](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Gyorsítótár-/fejlécbeállítások (a [gyorsítótárszabályokkal](cdn-caching-rules.md))  |  |**&#x2713;** |**&#x2713;** | |
| Gyorsítótár-/fejlécbeállítások (a [szabálymotorral](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| [Lekérdezési sztringek gyorsítótárazása](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Kettős verem (IPv4/IPv6) | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2-támogatás](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Biztonság** | **Standard Microsoft (előzetes verzió)** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| HTTPS-támogatás CDN-végponttal | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Egyéni tartományi HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | |**&#x2713;** |**&#x2713;** |
| [Egyéni tartománynevek támogatása](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geoszűrés](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Jogkivonat-hitelesítés](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Védelem DDOS-támadások ellen](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Saját tanúsítvány használata](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  |  |  |
||||
| **Elemzések és jelentéskészítés** | **Standard Microsoft (előzetes verzió)** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Azure-beli diagnosztikai naplók](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Alapvető jelentések a Verizontól](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Egyéni jelentések a Verizontól](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Speciális HTTP-jelentések](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Valós idejű statisztikák](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Határcsomópont teljesítménye](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Valós idejű riasztások](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Könnyű használat** | **Standard Microsoft (előzetes verzió)** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Egyszerű integráció a [Storage](cdn-create-a-storage-account-with-cdn.md), a [Web Apps](cdn-add-to-web-app.md), a [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md) és egyéb Azure-szolgáltatásokkal  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Felügyelet [REST API](https://msdn.microsoft.com/library/mt634456.aspx), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) vagy [PowerShell](cdn-manage-powershell.md) használatával  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Testreszabható, szabályalapú tartalomkézbesítési motor](cdn-rules-engine.md)  |  | | |**&#x2713;** |
| URL-átirányítás/átírás (a [szabálymotorral](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Mobileszközökre vonatkozó szabályok (a [szabálymotorral](cdn-rules-engine.md))  |  | | |**&#x2713;** |

\* A Microsoft és a Verizon az általános webes kézbesítési optimalizálás használatával támogatja a nagy méretű fájlok és médiatartalmak küldését.



