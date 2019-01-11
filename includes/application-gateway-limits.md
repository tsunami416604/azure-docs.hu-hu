---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 11/09/2018
ms.author: victorh
ms.openlocfilehash: 66dea07a1ff725c6707b19bc6ebdc5563f1b158b
ms.sourcegitcommit: d4f728095cf52b109b3117be9059809c12b69e32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/10/2019
ms.locfileid: "54211854"
---
| Erőforrás | Alapértelmezett korlát | Megjegyzés |
| --- | --- | --- |
| Application Gateway |előfizetésenként 1000 | |
| Előtérbeli IP-konfigurációk |2 |1 nyilvános és 1 magán |
| Előtérrendszer portjai |100<sup>1</sup> | |
| Háttér-címkészletek |100<sup>1</sup> | |
| Készletenkénti háttérkiszolgálók |1200 | |
| HTTP-figyelők |100<sup>1</sup> | |
| HTTP-terheléselosztási szabályok |100<sup>1</sup> | |
| Háttér-HTTP-beállítások |100<sup>1</sup> | |
| Átjárónkénti példányok száma |32 | |
| SSL-tanúsítványok |100<sup>1</sup> |HTTP-figyelőnként 1 |
| Hitelesítési tanúsítványok |100 | |
| Megbízható legfelső szintű tanúsítványok |100 | |
| Kérelem időkorlátja min |1 másodperc | |
| Kérelem maximális időtúllépése |24 óra | |
| Helyek száma |100<sup>1</sup> |HTTP-figyelőnként 1 |
| URL-címleképezések száma figyelő |1 | |
| URL-cím maximális-alapú szabályt leképezése|100||
| Az átirányítási konfigurációk |100<sup>1</sup>| |
| WebSocket egyidejű kapcsolat |5000| |
| URL-cím maximális hossza|8000||
| Maximális feltöltési méretének Standard |2 GB | |
| Maximális feltöltési méretének WAF |Közepes WAF átjárók – 100 MB-ra<br>Nagy WAF átjárók – 500 MB-tal| |
| WAF méretének korlátja (fájlok) nélkül|128 KB||

<sup>1</sup> esetén a WAF-kompatibilis SKU-k javasoljuk, hogy az optimális teljesítmény érdekében a 40 erőforrások számának korlátozásához.
