---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: 9a8d925e5d72500817028b913a80255a4339255c
ms.sourcegitcommit: 22c97298aa0e8bd848ff949f2886c8ad538c1473
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/14/2019
ms.locfileid: "67148428"
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
| SSL-tanúsítvány maximális mérete |V1 TERMÉKVÁLTOZATOT – 10 KB-OS<br>V2 SZINTŰ TERMÉKVÁLTOZATOT – 16 KB-OS| |
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
|Maximális WAF egyéni szabályok|100||

<sup>1</sup> WAF-kompatibilis SKU esetén azt javasoljuk, hogy az optimális teljesítmény érdekében a 40 erőforrások számának korlátozásához.
