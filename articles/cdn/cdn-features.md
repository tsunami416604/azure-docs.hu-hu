---
title: Az Azure Content Delivery Network- (CDN-) termékszolgáltatások összehasonlítása | Microsoft Docs
description: A jelen cikkben az egyes Azure Content Delivery Network- (CDN-) termékek által támogatott szolgáltatásokat ismerheti meg.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: ''
ms.service: cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 03/25/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: d78b10edd005a593b4ebe4f34ca2280ccdfdaa04
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918909"
---
# <a name="compare-azure-cdn-product-features"></a>Az Azure CDN-termékszolgáltatások összehasonlítása

Az Azure Content Delivery Network (CDN) négy termékek tartalmazza: **A Microsoft standard szintű Azure CDN**, **standard szintű Azure CDN az Akamaitól**, **standard szintű Azure CDN a Verizontól**, és **prémium szintű Azure CDN a Verizontól**. A **Verizon Azure CDN Standard** típusú profilok **Verizon Azure CDN Premium** típusú profilba történő migrálására vonatkozó információkért lásd az [Azure CDN-profil Standard Verizonból Premium Verizonba történő migrálását](cdn-migrate.md) ismertető cikket.

Az alábbi táblázat az egyes termékek szolgáltatásait hasonlítja össze.

| **Teljesítménnyel kapcsolatos szolgáltatások és optimalizálási lehetőségek** | **Szabványos Microsoft** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** |
| --- | --- | --- | --- | --- |
| [Dinamikus helygyorsítás](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Keresztül kínált [Azure bejárati ajtajának szolgáltatás](https://docs.microsoft.com/azure/frontdoor/front-door-overview) | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dinamikus helygyorsítás – Adaptív képtömörítés](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dinamikus helygyorsítás – Előzetes objektumbetöltés](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Általános webes kézbesítés optimalizálása](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;**, Válassza az optimalizálás típusa, ha az átlagos mérete 10 MB-nál kisebb  | **&#x2713;** |  **&#x2713;** |
| [Online streamelés optimalizálása](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | Általános webes kézbesítés keresztül | **&#x2713;**  | Általános webes kézbesítés keresztül |  Általános webes kézbesítés keresztül |
| [Nagyméretű fájlok optimalizálása](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | Általános webes kézbesítés keresztül | **&#x2713;**, Jelölje be az optimalizálás típusa, ha az átlagos mérete 10 MB-nál nagyobb   | Általános webes kézbesítés keresztül |  Általános webes kézbesítés keresztül |
| Optimalizálás típusának módosítása | |**&#x2713;** | | |
| Forrásport |Összes TCP-port |[Engedélyezett forrásportok](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Összes TCP-port |Összes TCP-port |
| [Globális kiszolgálói terheléselosztás (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Gyors végleges törlés](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**, Kiürítés és a helyettesítő karakteres kiürítés nem támogatottak az Akamai Azure CDN jelenleg |**&#x2713;** |**&#x2713;** |
| [Objektumok előzetes betöltése](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Gyorsítótár-/fejlécbeállítások (a [gyorsítótárszabályokkal](cdn-caching-rules.md))  |  |**&#x2713;** |**&#x2713;** | |
| Testre szabható, szabályok alapján tartalomkézbesítési motor (használatával [szabálymotorral](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Gyorsítótár-/fejlécbeállítások (a [szabálymotorral](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| URL-átirányítás/átírás (használatával [szabálymotorral](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| Mobileszközökre vonatkozó szabályok (a [szabálymotorral](cdn-rules-engine.md))  |  | | |**&#x2713;** |
| [Lekérdezési sztringek gyorsítótárazása](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Kettős verem (IPv4/IPv6) | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2-támogatás](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Biztonság** | **Szabványos Microsoft** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| HTTPS-támogatás CDN-végponttal | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Egyéni tartomány HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Egyéni tartománynevek támogatása](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geoszűrés](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Jogkivonat-hitelesítés](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Védelem DDOS-támadások ellen](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [A saját tanúsítványát](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  |  |  |
||||
| **Elemzések és jelentéskészítés** | **Szabványos Microsoft** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| [Azure diagnosztikai naplók](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Alapvető jelentések a Verizontól](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Egyéni jelentések a Verizontól](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Speciális HTTP-jelentések](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Valós idejű statisztikák](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Az élcsomópontok teljesítményének](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Valós idejű riasztások](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Könnyű használat** | **Szabványos Microsoft** | **Akamai Standard** | **Verizon Standard** | **Verizon Premium** | 
| Egyszerű integráció a [Storage](cdn-create-a-storage-account-with-cdn.md), a [Web Apps](cdn-add-to-web-app.md), a [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md) és egyéb Azure-szolgáltatásokkal  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Felügyelet [REST API](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) vagy [PowerShell](cdn-manage-powershell.md) használatával  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [A tömörítés MIME-típusok](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Csak az alapértelmezett |Konfigurálható |Konfigurálható  |Konfigurálható  |
| A tömörítés kódolásokat  |gzip, brotli |gzip |gzip, deflate, bzip2, brotili  |gzip, deflate, bzip2, brotili  |






