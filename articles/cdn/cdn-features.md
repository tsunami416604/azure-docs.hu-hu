---
title: Az Azure Content Delivery Network- (CDN-) termékszolgáltatások összehasonlítása | Microsoft Docs
description: A jelen cikkben az egyes Azure Content Delivery Network- (CDN-) termékek által támogatott szolgáltatásokat ismerheti meg.
services: cdn
documentationcenter: ''
author: asudbring
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2019
ms.author: allensu
ms.custom: mvc
ms.openlocfilehash: 0e57ae691bf4b07b8161bc343929510d6be041a8
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/13/2020
ms.locfileid: "81260513"
---
# <a name="compare-azure-cdn-product-features"></a>Az Azure CDN-termékszolgáltatások összehasonlítása

Az Azure Content Delivery Network (CDN) négy terméket tartalmaz: a **Microsoft Azure CDN Standard**, az **Akamai Azure CDN Standard**, a **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** terméket. A **Verizon Azure CDN Standard** típusú profilok **Verizon Azure CDN Premium** típusú profilba történő migrálására vonatkozó információkért lásd az [Azure CDN-profil Standard Verizonból Premium Verizonba történő migrálását](cdn-migrate.md) ismertető cikket. Ne feledje, hogy bár van egy frissítési útvonal a Standard Verizon-tól a Premium Verizon-ig, jelenleg nincs konverziós mechanizmus más termékek között.

Az alábbi táblázat az egyes termékek szolgáltatásait hasonlítja össze.

| **Teljesítménnyel kapcsolatos szolgáltatások és optimalizálási lehetőségek** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Dinamikus helygyorsulás](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Az [Azure Bejárati ajtaján](https://docs.microsoft.com/azure/frontdoor/front-door-overview) keresztül elérhető | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dinamikus helygyorsítás – Adaptív képtömörítés](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dinamikus helygyorsítás – Előzetes objektumbetöltés](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Általános webes kézbesítésoptimalizálás](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;**, Akkor válassza ezt az optimalizálási típust, ha az átlagos fájlméret kisebb, mint 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Videóstreamelés optimalizálása](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | keresztül Általános Web Delivery | **&#x2713;**  | keresztül Általános Web Delivery |  keresztül Általános Web Delivery |
| [Nagyfájl-optimalizálás](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | keresztül Általános Web Delivery | **&#x2713;**, Akkor válassza ezt az optimalizálási típust, ha az átlagos fájlméret nagyobb, mint 10 MB   | keresztül Általános Web Delivery |  keresztül Általános Web Delivery |
| Optimalizálástípus módosítása | |**&#x2713;** | | |
| Eredeti port |Minden TCP-port |[Engedélyezett kezdőportok](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Minden TCP-port |Minden TCP-port |
| [Globális kiszolgálói terheléselosztás (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Gyors végleges törlés](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**, Az összes kiürítése és a helyettesítő karakteres kiürítésnem támogatja az Azure CDN-t az Akamai-ból jelenleg |**&#x2713;** |**&#x2713;** |
| [Objektumok előzetes betöltése](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Gyorsítótár-/fejlécbeállítások (a [gyorsítótárszabályokkal](cdn-caching-rules.md))  |**&#x2713;** [szabványos szabálymotor használatával](cdn-standard-rules-engine.md)  |**&#x2713;** |**&#x2713;** | |
| Testreszabható, szabályokon alapuló tartalomkézbesítési motor |**&#x2713;** [szabványos szabálymotor használatával](cdn-standard-rules-engine.md)  | | |**&#x2713;** [a szabálymotor használata](cdn-rules-engine.md) |
| Gyorsítótár-/fejléc-beállítások  |**&#x2713;** [szabványos szabálymotor használatával](cdn-standard-rules-engine.md) | | |**&#x2713;** [prémium szabályok motorját használja](cdn-rules-engine.md) |
| URL-átirányítás/átírás |**&#x2713;** [szabványos szabálymotor használatával](cdn-standard-rules-engine.md)  | | |**&#x2713;** [prémium szabályok motorját használja](cdn-rules-engine.md) |
| Mobileszköz-szabályok  |**&#x2713;** [szabványos szabálymotor használatával](cdn-standard-rules-engine.md) | | |**&#x2713;** [prémium szabályok motorját használja](cdn-rules-engine.md) |
| [Lekérdezési sztringek gyorsítótárazása](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Kettős verem (IPv4/IPv6) | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2-támogatás](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Biztonság** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| HTTPS-támogatás CDN-végponttal | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Egyéni tartomány HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;**, közvetlen CNAME szükséges az engedélyezéshez |**&#x2713;** |**&#x2713;** |
| [Egyéni tartománynevek támogatása](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geoszűrés](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Jogkivonat-hitelesítés](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS védelem](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Saját tanúsítvány használata](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Támogatott TLS-verziók | TLS 1.2, TLS 1.0/1.1 - [Konfigurálható](https://docs.microsoft.com/rest/api/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Elemzések és jelentéskészítés** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Azure diagnosztikai naplók](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Alapvető jelentések a Verizontól](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Egyéni jelentések a Verizontól](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Speciális HTTP-jelentések](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Valós idejű statisztikák](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Határcsomópont teljesítménye](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Valós idejű riasztások](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Könnyű használat** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| Egyszerű integráció a [Storage](cdn-create-a-storage-account-with-cdn.md), a [Web Apps](cdn-add-to-web-app.md), a [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md) és egyéb Azure-szolgáltatásokkal  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Felügyelet [REST API](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) vagy [PowerShell](cdn-manage-powershell.md) használatával  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tömörítési MIME-típusok](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Csak alapértelmezett |Konfigurálható |Konfigurálható  |Konfigurálható  |
| Tömörítési kódolások  |gzip, brotli |Gzip |gzip, leereszt, bzip2, brotili  |gzip, leereszt, bzip2, brotili  |






