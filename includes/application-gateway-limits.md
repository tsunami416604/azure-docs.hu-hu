---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: 48f4c7497583e872c89e4d8cd92dab52ab4f9239
ms.sourcegitcommit: f915d8b43a3cefe532062ca7d7dbbf569d2583d8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78305012"
---
| Erőforrás | Alapértelmezett/maximális korlát | Megjegyzés |
| --- | --- | --- |
| Azure Application Gateway |1 000/előfizetés | |
| Előtér-IP-konfigurációk |2 |1 nyilvános és 1 magán |
| Előtér-portok |100<sup>1</sup> | |
| Háttérbeli címkészlet |100<sup>1</sup> | |
| Háttér-kiszolgálók/készlet |1,200 | |
| HTTP-figyelők |200<sup>1</sup> |Legfeljebb 100 aktív figyelő, amely útválasztási forgalom. Aktív figyelők = a figyelők teljes száma – a figyelők nem aktívak.<br>Ha egy útválasztási szabályban az alapértelmezett konfiguráció a forgalom irányítására van beállítva (például egy figyelővel, egy háttér-készlettel és a HTTP-beállításokkal), akkor az is figyelőnek számít.|
| HTTP-terheléselosztási szabályok |100<sup>1</sup> | |
| Háttérbeli HTTP-beállítások |100<sup>1</sup> | |
| Átjárónkénti példányok száma |V1 SKU – 32<br>V2 SKU – 125 | |
| SSL-tanúsítványok |100<sup>1</sup> |1/HTTP-figyelő |
| SSL-tanúsítvány maximális mérete |V1 SKU – 10 KB<br>V2 SKU – 16 KB| |
| Hitelesítési tanúsítványok |100 | |
| Megbízható legfelső szintű tanúsítványok |100 | |
| Kérelem időkorlátja (minimum) |1 másodperc | |
| Kérelem időtúllépésének maximális száma |24 óra | |
| Helyek száma |100<sup>1</sup> |1/HTTP-figyelő |
| URL-térképek/figyelő |1 | |
| Elérésiút-alapú szabályok maximális elérési útja egy URL-címen|100||
| Átirányítási konfigurációk |100<sup>1</sup>| |
| Egyidejű WebSocket-kapcsolatok |Közepes átjárók 20000<br> Nagyméretű átjárók 50k| |
| URL-cím maximális hossza|32KB| |
| A fejléc maximális mérete HTTP/2 esetén |4KB| |
| Maximális fájlfeltöltés mérete, standard |2 GB | |
| Maximális fájlfeltöltés mérete WAF |V1 közepes WAF-átjárók, 100 MB<br>V1 nagyméretű WAF-átjárók, 500 MB<br>V2 WAF, 750 MB| |
| WAF-törzs mérete, fájlok nélkül|128 KB||
| Egyéni WAF-szabályok maximális száma|100||
| WAF-kizárások maximális száma|100||

<sup>1</sup> WAF-kompatibilis SKU esetén azt javasoljuk, hogy az optimális teljesítmény érdekében korlátozza a 40-as erőforrások számát.
