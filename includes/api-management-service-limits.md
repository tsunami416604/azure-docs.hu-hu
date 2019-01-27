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
ms.openlocfilehash: 646ebc79e3615b289b60e47c0a38595e7892d8cc
ms.sourcegitcommit: 58dc0d48ab4403eb64201ff231af3ddfa8412331
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/26/2019
ms.locfileid: "55084900"
---
| Erőforrás | Korlát |
| --- | --- |
| Skálázási egységek maximális száma | 10 régiónként<sup>1</sup> |
| Gyorsítótár mérete | 5 GB-os egységenként<sup>2</sup> |
| A háttérrendszer egyidejű kapcsolatok<sup>3</sup> egy HTTP-szolgáltató | egységenként 2048<sup>4</sup> |
| Gyorsítótárazott válasz maximális mérete | 2MB |
| Maximális házirend dokumentum mérete | 256KB<sup>5</sup> | 
| Szolgáltatáspéldány maximális egyéni átjáró tartományt<sup>6</sup> | 20 | 
| Maximális száma előfizetésenként szolgáltatáspéldányok<sup>7</sup> | 20 | 
| Szolgáltatás példányonként előfizetések maximális száma<sup>7</sup> | 500 |
| Ügyféltanúsítványok szolgáltatás példányonként legfeljebb<sup>7</sup> | 50 | 
| API-k maximális száma szolgáltatáspéldány<sup>7</sup> | 50 | 
| Szolgáltatáspéldány API műveletek maximális száma<sup>7</sup> | 1000 | 
| Összes kérelem maximális időtartama<sup>7</sup> | 30 másodperc | 
| Maximális pufferelt adattartalom-méretkorlát<sup>7</sup> | 2MB | 


<sup>1</sup> skálázás korlátok a tarifacsomag függenek. A díjszabás megjelenítéséhez szintek és a méretezési korlátok Ugrás [API Management díjszabása](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>2</sup> / egység gyorsítótár mérete attól függ, a tarifacsomagot. A díjszabás megjelenítéséhez szintek és a méretezési korlátok Ugrás [API Management díjszabása](https://azure.microsoft.com/pricing/details/api-management/).<br/>
<sup>3</sup> kapcsolatok készletezett és újra felhasználni, kivéve, ha explicit módon lezárta a háttérrendszerhez.<br/>
<sup>4</sup> a Basic, Standard és Premium szintű egységek. A fejlesztői csomag legfeljebb 1024. A Használatalapú csomagra nem vonatkozik.<br/> 
<sup>5</sup> a az alap, Standard és prémium szinten. A Használatalapú házirend dokumentum méret 4 KB-os korlátozva.<br/>
<sup>6</sup> csak prémium szinten elérhető.<br/>
<sup>7</sup> a Használatalapú csomag esetében érvényes.<br/>



