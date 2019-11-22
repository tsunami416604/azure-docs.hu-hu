---
title: Az Azure Content Delivery Network- (CDN-) termékszolgáltatások összehasonlítása | Microsoft Docs
description: A jelen cikkben az egyes Azure Content Delivery Network- (CDN-) termékek által támogatott szolgáltatásokat ismerheti meg.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: mdgattuso
ms.assetid: ''
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2019
ms.author: magattus
ms.custom: mvc
ms.openlocfilehash: 64b906c8a6b52d9c9655f3fe2b13d504d8eed4cb
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/21/2019
ms.locfileid: "74278086"
---
# <a name="compare-azure-cdn-product-features"></a>Az Azure CDN-termékszolgáltatások összehasonlítása

Az Azure Content Delivery Network (CDN) négy terméket tartalmaz: a **Microsoft Azure CDN Standard**, az **Akamai Azure CDN Standard**, a **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** terméket. A **Verizon Azure CDN Standard** típusú profilok **Verizon Azure CDN Premium** típusú profilba történő migrálására vonatkozó információkért lásd az [Azure CDN-profil Standard Verizonból Premium Verizonba történő migrálását](cdn-migrate.md) ismertető cikket. Vegye figyelembe, hogy habár a standard Verizon és a prémium Verizon közötti frissítési útvonal található, a többi termék között nincs konverziós mechanizmus.

Az alábbi táblázat az egyes termékek szolgáltatásait hasonlítja össze.

| **Teljesítménnyel kapcsolatos szolgáltatások és optimalizálási lehetőségek** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** |
| --- | --- | --- | --- | --- |
| [Dinamikus helygyorsítás](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Az [Azure bejárati ajtó szolgáltatásán](https://docs.microsoft.com/azure/frontdoor/front-door-overview) keresztül elérhető | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dinamikus helygyorsítás – Adaptív képtömörítés](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dinamikus helygyorsítás – Előzetes objektumbetöltés](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Általános webes kézbesítés optimalizálása](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;** , Válassza ezt az optimalizálási típust, ha az átlagos fájlméret 10 MB-nál kisebb méretű  | **&#x2713;** |  **&#x2713;** |
| [Videóstreamelés optimalizálása](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | Általános webes Kézbesítésen keresztül | **&#x2713;**  | Általános webes Kézbesítésen keresztül |  Általános webes Kézbesítésen keresztül |
| [Nagyméretű fájlok optimalizálása](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | Általános webes Kézbesítésen keresztül | **&#x2713;** , Válassza ezt az optimalizálási típust, ha az átlagos fájlméret nagyobb, mint 10 MB   | Általános webes Kézbesítésen keresztül |  Általános webes Kézbesítésen keresztül |
| Optimalizálás típusának módosítása | |**&#x2713;** | | |
| Forrás port |Minden TCP-port |[Engedélyezett származási portok](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Minden TCP-port |Minden TCP-port |
| [Globális kiszolgálói terheléselosztás (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Gyors végleges törlés](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;** Az összes törlése és a helyettesítő karakteres kiürítés nem támogatott Azure CDN a Akamai jelenleg |**&#x2713;** |**&#x2713;** |
| [Objektumok előzetes betöltése](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Gyorsítótár-/fejlécbeállítások (a [gyorsítótárszabályokkal](cdn-caching-rules.md))  |**&#x2713;** a [Standard Rules Engine](cdn-standard-rules-engine.md) használata  |**&#x2713;** |**&#x2713;** | |
| Testreszabható, szabályokon alapuló Content Delivery Engine |**&#x2713;** a [Standard Rules Engine](cdn-standard-rules-engine.md) használata  | | |**&#x2713;** a [Rules Engine](cdn-rules-engine.md) használata |
| Gyorsítótár/fejléc beállításai  |**&#x2713;** a [Standard Rules Engine](cdn-standard-rules-engine.md) használata | | |**&#x2713;** [Premium Rules Engine](cdn-rules-engine.md) használata |
| URL-átirányítás/újraírás |**&#x2713;** a [Standard Rules Engine](cdn-standard-rules-engine.md) használata  | | |**&#x2713;** [Premium Rules Engine](cdn-rules-engine.md) használata |
| Mobileszköz-szabályok  |**&#x2713;** a [Standard Rules Engine](cdn-standard-rules-engine.md) használata | | |**&#x2713;** [Premium Rules Engine](cdn-rules-engine.md) használata |
| [Lekérdezési sztringek gyorsítótárazása](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Kettős verem (IPv4/IPv6) | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2-támogatás](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Biztonság** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| HTTPS-támogatás CDN-végponttal | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Egyéni tartományi HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;** , Közvetlen CNAME-t igényel az engedélyezéshez |**&#x2713;** |**&#x2713;** |
| [Egyéni tartománynevek támogatása](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geoszűrés](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Jogkivonat-hitelesítés](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [Védelem DDOS-támadások ellen](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Saját tanúsítvány használata](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#ssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
||||
| **Elemzések és jelentéskészítés** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Premium Verizon** | 
| [Azure-beli diagnosztikai naplók](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
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
| Tömörítési kódolások  |gzip, brotli |Gzip |gzip, deflate, bzip2, brotili  |gzip, deflate, bzip2, brotili  |






