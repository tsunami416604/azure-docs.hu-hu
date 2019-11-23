---
title: fájl belefoglalása
description: fájl belefoglalása
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 03/22/2018
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: fc945a7e9389c8aec48a6a1dba969fbf92002d3a
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/18/2019
ms.locfileid: "67179522"
---
| Erőforrás | Korlát |
| --- | --- |
| Méretezési egységek maximális száma | 10 régiónként<sup>1</sup> |
| Gyorsítótár mérete | 5 GB/egység<sup>2</sup> |
| Egyidejű háttér-kapcsolatok<sup>3</sup> /http-szolgáltató | 2 048/egységenként<sup>4</sup> |
| Gyorsítótárazott válasz maximális mérete | 2 MB |
| Házirend-dokumentum maximális mérete | 256 KB<sup>5</sup> | 
| Egyéni átjáró-tartományok maximális száma szolgáltatási példány esetén<sup>6</sup> | 20 |
| HITELESÍTÉSSZOLGÁLTATÓI tanúsítványok maximális száma egy szolgáltatási példányon | 10 | 
| Szolgáltatási példányok maximális száma/előfizetés<sup>7</sup> | 20 | 
| Előfizetések maximális száma a szolgáltatási példányban<sup>7</sup> | 500 |
| Ügyféltanúsítványok maximális száma a szolgáltatási példányban<sup>7</sup> | 50 | 
| API-k maximális száma a szolgáltatási példányok esetében<sup>7</sup> | 50 | 
| A szolgáltatási példányok legfeljebb<sup>7</sup> API-műveleteinek maximális száma | 1,000 | 
| Maximális kérelem teljes időtartama<sup>7</sup> | 30 másodperc | 
| A pufferelt tartalom maximális mérete (<sup>7</sup> ) | 2 MB | 


<sup>1</sup> A skálázási korlátok az árképzési szinttől függenek. A díjszabási szintek és a méretezési korlátok megtekintéséhez lásd: [API Management díjszabása](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> Egységenkénti gyorsítótár mérete az árképzési szinttől függ. A díjszabási szintek és a méretezési korlátok megtekintéséhez lásd: [API Management díjszabása](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> A kapcsolatok készletezése és újbóli felhasználása, kivéve, ha a háttér explicit módon lezárta.<br/>
<sup>4</sup> Ez a korlát az alapszintű, a standard és a prémium szint egységenként van. A fejlesztői szintet 1 024-re korlátozzák. Ez a korlát nem vonatkozik a felhasználás szintjére.<br/><sup>5</sup> 
ez a korlát az alapszintű, a standard és a prémium szintekre vonatkozik. A felhasználási szinten a szabályzat dokumentumának mérete legfeljebb 4 KB lehet.<br/>
<sup>6</sup> Ez az erőforrás csak a prémium szintű csomagban érhető el.<br/>
<sup>7</sup> Ez az erőforrás csak a felhasználási szintet érinti.<br/>



