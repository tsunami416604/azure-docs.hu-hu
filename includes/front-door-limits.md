---
title: fájl belefoglalása
description: fájl belefoglalása
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.topic: include
ms.date: 05/09/2019
ms.author: sharadag
ms.custom: include file
ms.openlocfilehash: 148ec3eccce71ab7a4a6c1391c0fa4753c248bd8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334773"
---
| Erőforrás | Korlát |
| --- | --- |
| Azure Bejárati ajtó erőforrásai előfizetésenként | 100 |
| Előtér-állomások, amelyek erőforrásonként egyéni tartományokat tartalmaznak | 500 |
| Útválasztási szabályok erőforrásonként | 500 |
| Háttérkészletek erőforrásonként | 50 |
| Háttérvégénként háttérkészlet | 100 |
| Útválasztási szabálynak megfelelő útvonalminták | 25 |
| URL-címek egyetlen gyorsítótár-kiürítési hívásban | 100 |
| Egyéni webalkalmazás-tűzfalszabályok házirendenként | 100 |
| Webalkalmazás tűzfalházirendje előfizetésenként | 100 |
| A webalkalmazás tűzfala egyéni szabályokonként ifeltételeknek felel meg | 10 |
| Webalkalmazás tűzfalának IP-címtartományai egyezési feltételenként | 600 |
| A webalkalmazás tűzfalkarakterlánca megfeleltetési feltételenkénti értékeknek | 10 |
| Webalkalmazás tűzfalkarakterláncának egyezési értéke | 256 |
| Webalkalmazás tűzfal A POST törzsparaméterének nevének hossza | 256 |
| Webalkalmazás-tűzfal HTTP-fejlécének hosszának hossza | 256 |
| Webalkalmazás-tűzfal cookie-jának hossza | 256 |
| Webalkalmazás tűzfalÁNAK HTTP-kérelemtörzs-mérete ellenőrizve | 128 KB |
| Webalkalmazás tűzfalegyéni válasz törzsének hossza | 2 KB |

### <a name="timeout-values"></a>Idő-eltinési értékek
#### <a name="client-to-front-door"></a>Ügyfél a bejárati ajtó
* A Bejárati ajtó tétje 61 másodperces tétlen TCP-kapcsolati időtúllépés.

#### <a name="front-door-to-application-back-end"></a>Bejárati ajtó az alkalmazás háttér-vég
* Ha a válasz egy darabolt válasz, egy 200-as t ad vissza, ha vagy amikor az első adattömb érkezik.
* A HTTP-kérelem háttérrendszerbe továbbítása után a Bejárati ajtó 30 másodpercet vár a háttérrendszerelső csomagjára. Ezután egy 503-as hibát ad vissza az ügyfélnek. Ez az érték konfigurálható a sendRecvTimeoutSeconds mezőn keresztül az API-ban.
    * Gyorsítótárazási forgatókönyvek esetén ez az időtúllépése nem konfigurálható, ezért ha egy kérelem gyorsítótárazott, és az első csomag a Bejárati ajtóból vagy a háttérrendszerből, akkor egy 504-es hiba kerül vissza az ügyfélnek. 
* Miután az első csomag érkezett a háttérrendszerből, a Bejárati ajtó 30 másodpercet vár egy tétlen időadatban. Ezután egy 503-as hibát ad vissza az ügyfélnek. Ez az időtúlérték nem konfigurálható.
* A hátsó tcp-munkamenet előbejárata időnyitva 90 másodperc.

### <a name="upload-and-download-data-limit"></a>Adatkorlát feltöltése és letöltése

|  | Darabolt átviteli kódolással (CTE) | HTTP-darabolás nélkül |
| ---- | ------- | ------- |
| **Letöltés** | A letöltési méret nincs korlátozva. | A letöltési méret nincs korlátozva. |
| **Feltöltés** |    Nincs korlátozás, amíg minden CTE feltöltés kevesebb, mint 2 GB. | A méret nem lehet nagyobb, mint 2 GB. |

### <a name="other-limits"></a>Egyéb korlátok
* Maximális URL-méret - 8192 bájt - A nyers URL maximális hosszát adja meg (séma + állomásnév + port + elérési út + az URL lekérdezési karakterlánca)
* Maximális lekérdezési karakterlánc méret - 4096 bájt - A lekérdezési karakterlánc maximális hosszát adja meg bájtban.
* A HTTP-válasz fejlécének maximális mérete az állapotminta URL-címéből – 4096 bájt – Az állapotminták összes válaszfejlécének maximális hosszát adta meg. 
