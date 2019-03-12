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
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57553540"
---
| Erőforrás | Korlát |
| --- | --- |
| Skálázási egységek maximális száma | 10 régiónként<sup>1</sup> |
| Gyorsítótár mérete | 5 GB-os egységenként<sup>2</sup> |
| Egyidejű háttér-kapcsolatok<sup>3</sup> egy HTTP-szolgáltató | a 2048 egységenként<sup>4</sup> |
| Gyorsítótárazott válasz maximális mérete | 2 MB |
| Maximális házirend dokumentum mérete | 256 KB<sup>5</sup> | 
| Szolgáltatáspéldány maximális egyéni átjáró tartományt<sup>6</sup> | 20 |
| Szolgáltatás példányonként CA-tanúsítványok maximális száma | 10 | 
| Maximális száma előfizetésenként szolgáltatáspéldányok<sup>7</sup> | 20 | 
| Szolgáltatás példányonként előfizetések maximális száma<sup>7</sup> | 500 |
| Ügyféltanúsítványok szolgáltatás példányonként legfeljebb<sup>7</sup> | 50 | 
| API-k maximális száma szolgáltatáspéldány<sup>7</sup> | 50 | 
| Szolgáltatáspéldány API műveletek maximális száma<sup>7</sup> | 1,000 | 
| Összes kérelem maximális időtartama<sup>7</sup> | 30 másodperc | 
| Maximális pufferelt adattartalom-méretkorlát<sup>7</sup> | 2 MB | 


<sup>1</sup>skálázás korlátok a tarifacsomag függenek. A tarifacsomagok és azok méretezési korlátok megtekintéséhez lásd: [az API Management díjszabása](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup>/ egység gyorsítótár mérete attól függ, a tarifacsomagot. A tarifacsomagok és azok méretezési korlátok megtekintéséhez lásd: [az API Management díjszabása](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup>kapcsolatok készletezett és újra felhasználható, ha explicit módon lezárta a háttérben.<br/>
<sup>4</sup>ezt a korlátot, az alapszintű, Standard és prémium szinten egységenként van. A fejlesztői csomag legfeljebb 1024. Ez a korlátozás nem vonatkozik a Használatalapú csomag.<br/> 
<sup>5</sup>Ez a korlátozás vonatkozik a Basic, Standard és prémium szinten. A használat rétegében házirend dokumentum mérete 4 KB-os korlátozva.<br/>
<sup>6</sup>csak prémium szinten érhető el ehhez az erőforráshoz.<br/>
<sup>7</sup>ehhez az erőforráshoz csak a Használatalapú csomag vonatkozik.<br/>



