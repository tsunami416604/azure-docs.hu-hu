---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 03/04/2020
ms.author: victorh
ms.openlocfilehash: a3fb3dbf026a696b9d472efcba139c371ff1e587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334921"
---
| Erőforrás | Korlát | Megjegyzés |
| --- | --- | --- |
| Azure Application Gateway |1000 előfizetésenként | |
| Előtér-IP-konfigurációk |2 |1 nyilvános és 1 magán |
| Előtér-portok |100<sup>1</sup> | |
| Háttércímkészletek |100<sup>1</sup> | |
| Háttérkiszolgálók készletenként |1200 | |
| HTTP-hallgatók |200<sup>1.</sup> |Legfeljebb 100 aktív figyelők, amelyek a forgalom útválasztási. Aktív figyelők = a figyelők teljes száma - a figyelők nem aktívak.<br>Ha egy útválasztási szabályon belüli alapértelmezett konfiguráció úgy van beállítva, hogy irányítsa a forgalmat (például rendelkezik egy figyelővel, egy háttérkészlet-készlet-el és HTTP-beállításokkal), akkor ez is figyelőnek számít.|
| HTTP terheléselosztási szabályok |100<sup>1</sup> | |
| Háttérrendszer HTTP-beállításai |100<sup>1</sup> | |
| Átjárónkénti példányok száma |V1 Termékváltozat – 32<br>2 termékváltozat – 125 | |
| SSL-tanúsítványok |100<sup>1</sup> |1 HTTP-hallgatónként |
| Az SSL-tanúsítvány maximális mérete |V1 Termékváltozat – 10 KB<br>V2 Termékváltozat – 16 KB| |
| Hitelesítési tanúsítványok |100 | |
| Megbízható főtanúsítványok |100 | |
| Kérelem időmegegyszeri |1 másodperc | |
| Időkérés maximális ideje |24 óra | |
| Helyek száma |100<sup>1</sup> |1 HTTP-hallgatónként |
| URL-leképezések hallgatónként |1 | |
| Az elérési úton alapuló szabályok maximális ára URL-leképezésenként|100||
| Konfigurációk átirányítása |100<sup>1</sup>| |
| Egyidejű WebSocket-kapcsolatok |Közepes átjárók 20k<br> Nagy átjárók 50k| |
| Az URL maximális hossza|32 KB| |
| A HTTP/2 maximális fejlécmérete |4 KB| |
| Maximális fájlfeltöltési méret, Normál |2 GB | |
| Maximális fájlfeltöltési méret WAF |V1 közepes WAF átjárók, 100 MB<br>V1 nagy WAF átjárók, 500 MB<br>V2 WAF, 750 MB| |
| WAF testméret-korlát, fájlok nélkül|128 KB||
| Maximális WAF egyéni szabályok|100||
| Maximális WAF-kizárások|100||

<sup>1</sup> WaF-kompatibilis ski-k esetén azt javasoljuk, hogy az optimális teljesítmény érdekében korlátozza az erőforrások számát 40-re.
