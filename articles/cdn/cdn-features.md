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
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "81260513"
---
# <a name="compare-azure-cdn-product-features"></a>Az Azure CDN-termékszolgáltatások összehasonlítása

Az Azure Content Delivery Network (CDN) négy terméket tartalmaz: a **Microsoft Azure CDN Standard**, az **Akamai Azure CDN Standard**, a **Verizon Azure CDN Standard** és a **Verizon Azure CDN Premium** terméket. A **Verizon Azure CDN Standard** típusú profilok **Verizon Azure CDN Premium** típusú profilba történő migrálására vonatkozó információkért lásd az [Azure CDN-profil Standard Verizonból Premium Verizonba történő migrálását](cdn-migrate.md) ismertető cikket. Vegye figyelembe, hogy habár a standard Verizon és a prémium Verizon közötti frissítési útvonal található, a többi termék között nincs konverziós mechanizmus.

Az alábbi táblázat az egyes termékek szolgáltatásait hasonlítja össze.

| **Teljesítménnyel kapcsolatos szolgáltatások és optimalizálási lehetőségek** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Verizon Premium** |
| --- | --- | --- | --- | --- |
| [Dinamikus helygyorsítás](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration)  | Az [Azure bejárati ajtó szolgáltatásán](https://docs.microsoft.com/azure/frontdoor/front-door-overview) keresztül elérhető | **&#x2713;**  | **&#x2713;** | **&#x2713;** |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dinamikus helygyorsítás – Adaptív képtömörítés](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#adaptive-image-compression-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;[Dinamikus helygyorsítás – Előzetes objektumbetöltés](https://docs.microsoft.com/azure/cdn/cdn-dynamic-site-acceleration#object-prefetch-azure-cdn-from-akamai-only)  |  | **&#x2713;**  |  |  |
| [Általános webes kézbesítés optimalizálása](https://docs.microsoft.com/azure/cdn/cdn-optimization-overview#general-web-delivery)  | **&#x2713;** | **&#x2713;**, akkor válassza ezt az optimalizálási típust, ha az átlagos fájlméret kisebb, mint 10 MB  | **&#x2713;** |  **&#x2713;** |
| [Videó-adatfolyam optimalizálása](https://docs.microsoft.com/azure/cdn/cdn-media-streaming-optimization)  | Általános webes Kézbesítésen keresztül | **&#x2713;**  | Általános webes Kézbesítésen keresztül |  Általános webes Kézbesítésen keresztül |
| [Nagyméretű fájlok optimalizálása](https://docs.microsoft.com/azure/cdn/cdn-large-file-optimization)  | Általános webes Kézbesítésen keresztül | **&#x2713;**, akkor válassza ezt az optimalizálási típust, ha az átlagos fájlméret meghaladja a 10 MB-ot   | Általános webes Kézbesítésen keresztül |  Általános webes Kézbesítésen keresztül |
| Optimalizálás típusának módosítása | |**&#x2713;** | | |
| Forrás port |Minden TCP-port |[Engedélyezett származási portok](https://docs.microsoft.com/previous-versions/azure/mt757337(v%3Dazure.100)#allowed-origin-ports) |Minden TCP-port |Minden TCP-port |
| [Globális kiszolgálói terheléselosztás (GSLB)](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-load-balancing-azure)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Gyors végleges törlés](cdn-purge-endpoint.md)  | **&#x2713;** |**&#x2713;**, a Akamai jelenleg nem támogatja az összes törlést és a helyettesítő karakterek kiürítését Azure CDN |**&#x2713;** |**&#x2713;** |
| [Objektumok előzetes betöltése](cdn-preload-endpoint.md)  |  | |**&#x2713;** |**&#x2713;** |
| Gyorsítótár-/fejlécbeállítások (a [gyorsítótárszabályokkal](cdn-caching-rules.md))  |**&#x2713;** a [Standard Rules Engine](cdn-standard-rules-engine.md) használatával  |**&#x2713;** |**&#x2713;** | |
| Testreszabható, szabályokon alapuló Content Delivery Engine |**&#x2713;** a [Standard Rules Engine](cdn-standard-rules-engine.md) használatával  | | |**&#x2713;** [szabályok motor](cdn-rules-engine.md) használatával |
| Gyorsítótár/fejléc beállításai  |**&#x2713;** a [Standard Rules Engine](cdn-standard-rules-engine.md) használatával | | |**&#x2713;** [Premium Rules Engine](cdn-rules-engine.md) használatával |
| URL-átirányítás/újraírás |**&#x2713;** a [Standard Rules Engine](cdn-standard-rules-engine.md) használatával  | | |**&#x2713;** [Premium Rules Engine](cdn-rules-engine.md) használatával |
| Mobileszköz-szabályok  |**&#x2713;** a [Standard Rules Engine](cdn-standard-rules-engine.md) használatával | | |**&#x2713;** [Premium Rules Engine](cdn-rules-engine.md) használatával |
| [Lekérdezési karakterlánc gyorsítótárazása](cdn-query-string.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Kettős verem (IPv4/IPv6) | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [HTTP/2-támogatás](cdn-http2.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
||||
 **Biztonság** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Verizon Premium** | 
| HTTPS-támogatás CDN-végponttal | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Egyéni tartomány HTTPS](cdn-custom-ssl.md)  | **&#x2713;** | **&#x2713;**, közvetlen CNAME-t igényel az engedélyezéshez |**&#x2713;** |**&#x2713;** |
| [Egyéni tartománynevek támogatása](cdn-map-content-to-custom-domain.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Geoszűrés](cdn-restrict-access-by-country.md)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Jogkivonat-alapú hitelesítés](cdn-token-auth.md)  |  |  |  |**&#x2713;**| 
| [DDOS-védelem](https://www.us-cert.gov/ncas/tips/ST04-015)  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Saját tanúsítvány használata](cdn-custom-ssl.md?tabs=option-2-enable-https-with-your-own-certificate#tlsssl-certificates) |**&#x2713;** |  | **&#x2713;** | **&#x2713;** |
| Támogatott TLS-verziók | TLS 1,2, TLS 1.0/1.1 – [konfigurálható](https://docs.microsoft.com/rest/api/cdn/customdomains/enablecustomhttps#usermanagedhttpsparameters) | TLS 1.2 | TLS 1.2 | TLS 1.2 |
||||
| **Elemzések és jelentéskészítés** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Verizon Premium** | 
| [Azure diagnosztikai naplók](cdn-azure-diagnostic-logs.md)  | **&#x2713;** | **&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Alapvető jelentések a Verizontól](cdn-analyze-usage-patterns.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Egyéni jelentések a Verizontól](cdn-verizon-custom-reports.md)  |  | |**&#x2713;** |**&#x2713;** |
| [Speciális HTTP-jelentések](cdn-advanced-http-reports.md)  |  | | |**&#x2713;** |
| [Valós idejű statisztikák](cdn-real-time-stats.md)  |  | | |**&#x2713;** |
| [Határcsomópont teljesítménye](cdn-edge-performance.md)  |  | | |**&#x2713;** |
| [Valós idejű riasztások](cdn-real-time-alerts.md)  |  | | |**&#x2713;** |
||||
| **Egyszerű használat** | **Standard Microsoft** | **Standard Akamai** | **Standard Verizon** | **Verizon Premium** | 
| Egyszerű integráció a [Storage](cdn-create-a-storage-account-with-cdn.md), a [Web Apps](cdn-add-to-web-app.md), a [Media Services](../media-services/media-services-portal-manage-streaming-endpoints.md) és egyéb Azure-szolgáltatásokkal  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| Felügyelet [REST API](/rest/api/cdn/), [.NET](cdn-app-dev-net.md), [Node.js](cdn-app-dev-node.md) vagy [PowerShell](cdn-manage-powershell.md) használatával  | **&#x2713;** |**&#x2713;** |**&#x2713;** |**&#x2713;** |
| [Tömörítési MIME-típusok](https://docs.microsoft.com/azure/cdn/cdn-improve-performance)  |Csak alapértelmezett |Konfigurálható |Konfigurálható  |Konfigurálható  |
| Tömörítési kódolások  |gzip, brotli |gzip |gzip, deflate, bzip2, brotili  |gzip, deflate, bzip2, brotili  |






