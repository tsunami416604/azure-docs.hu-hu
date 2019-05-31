---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 3/26/2019
ms.author: victorh
ms.openlocfilehash: 65ed28c967164be4d239cd4d59b6b36f06caeced
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66238407"
---
| Resource | Alapértelmezett/maximális korlát | Megjegyzés |
| --- | --- | --- |
| Azure Application Gateway |előfizetésenként 1000 | |
| Előtérbeli IP-konfigurációk |2 |1 nyilvános és 1 magán |
| Előtérrendszer portjai |100<sup>1</sup> | |
| Háttér-címkészletek |100<sup>1</sup> | |
| Készletenkénti háttérkiszolgálók |1,200 | |
| HTTP-figyelők |100<sup>1</sup> | |
| HTTP-terheléselosztási szabályok |100<sup>1</sup> | |
| Háttér-HTTP-beállítások |100<sup>1</sup> | |
| Átjárónkénti példányok száma |32 | |
| SSL-tanúsítványok |100<sup>1</sup> |HTTP-figyelőnként 1 |
| SSL-tanúsítvány maximális mérete |V1 TERMÉKVÁLTOZATOT – 10 KB-OS<br>V2 SZINTŰ TERMÉKVÁLTOZATOT – 25 KB| |
| Hitelesítési tanúsítványok |100 | |
| Megbízható legfelső szintű tanúsítványok |100 | |
| Minimális kérés időtúllépése |1 másodperc | |
| Kérelem maximális időtúllépése |24 óra | |
| Helyek száma |100<sup>1</sup> |HTTP-figyelőnként 1 |
| URL-címleképezések száma figyelő |1 | |
| URL-cím maximális-alapú szabályt leképezése|100||
| Az átirányítási konfigurációk |100<sup>1</sup>| |
| WebSocket egyidejű kapcsolat |Medium gateways 20k<br> Nagy átjárók 50k| |
| URL-cím maximális hossza|8,000||
| Maximális fájlfeltöltési méretet, Standard |2 GB | |
| Maximális feltöltési méretének WAF |Közepes WAF átjárók, 100 MB-ra<br>Nagy WAF átjárók, 500 MB-tal| |
| WAF méretének korlátja, fájlok nélkül|128 KB||

<sup>1</sup> WAF-kompatibilis SKU esetén azt javasoljuk, hogy az optimális teljesítmény érdekében a 40 erőforrások számának korlátozásához.
