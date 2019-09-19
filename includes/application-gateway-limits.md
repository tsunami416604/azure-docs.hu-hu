---
author: vhorne
ms.service: application-gateway
ms.topic: include
ms.date: 6/5/2019
ms.author: victorh
ms.openlocfilehash: cd0ec07ebfffc839fedcdc76c9aff8e86556a7de
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "70130090"
---
| Resource | Alapértelmezett/maximális korlát | Megjegyzés |
| --- | --- | --- |
| Azure Application Gateway |1 000/előfizetés | |
| Előtér-IP-konfigurációk |2 |1 nyilvános és 1 magán |
| Előtér-portok |100<sup>1</sup> | |
| Háttérbeli címkészlet |100<sup>1</sup> | |
| Háttér-kiszolgálók/készlet |1,200 | |
| HTTP-figyelők |100<sup>1</sup> | |
| HTTP-terheléselosztási szabályok |100<sup>1</sup> | |
| Háttérbeli HTTP-beállítások |100<sup>1</sup> | |
| Átjárónkénti példányok száma |32 | |
| SSL-tanúsítványok |100<sup>1</sup> |1/HTTP-figyelők |
| SSL-tanúsítvány maximális mérete |V1 SKU – 10 KB<br>V2 SKU – 16 KB| |
| Hitelesítési tanúsítványok |100 | |
| Megbízható legfelső szintű tanúsítványok |100 | |
| Kérelem időkorlátja (minimum) |1 másodperc | |
| Kérelem időtúllépésének maximális száma |24 óra | |
| Webhelyek száma |100<sup>1</sup> |1/HTTP-figyelők |
| URL-térképek/figyelő |1 | |
| Elérésiút-alapú szabályok maximális elérési útja egy URL-címen|100||
| Átirányítási konfigurációk |100<sup>1</sup>| |
| Egyidejű WebSocket-kapcsolatok |Közepes átjárók 20000<br> Nagyméretű átjárók 50k| |
| URL-cím maximális hossza|8,000||
| Maximális fájlfeltöltés mérete, standard |2 GB | |
| Maximális fájlfeltöltés mérete WAF |Közepes WAF-átjárók, 100 MB<br>Nagyméretű WAF-átjárók, 500 MB| |
| WAF-törzs mérete, fájlok nélkül|128 KB||
| Egyéni WAF-szabályok maximális száma|100||
| WAF-kizárások maximális száma|100||

<sup>1</sup> WAF-kompatibilis SKU esetén azt javasoljuk, hogy az optimális teljesítmény érdekében korlátozza a 40-as erőforrások számát.
