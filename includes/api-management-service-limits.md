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
ms.openlocfilehash: 676bd3b3a369e3f834016f626a8e69f6b41d9b23
ms.sourcegitcommit: ebf2f2fab4441c3065559201faf8b0a81d575743
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/20/2018
ms.locfileid: "52170700"
---
| Erőforrás | Korlát |
| --- | --- |
| A skálázási egység | 10 régiónként<sup>1</sup> |
| Gyorsítótár | 5 GB-os egységenként<sup>1</sup> |
| A háttérrendszer egyidejű kapcsolatok<sup>2</sup> egy HTTP-szolgáltató | egységenként 2048<sup>3</sup> |
| Gyorsítótárazott válasz maximális mérete | 2MB |
| Maximális házirend dokumentum mérete | 256KB |
| Maximális egyéni átjáró tartományok | szolgáltatáspéldány 20<sup>4</sup> |


<sup>1</sup>korlátok az API Management rendszer minden tarifacsomag különböző. A díjszabás megjelenítéséhez szintek és a méretezési korlátok Ugrás [API Management díjszabása](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> kapcsolatok készletezett és újra felhasználni, kivéve, ha explicit módon lezárta a háttérrendszerhez.
<sup>3</sup> az alapszintű, Standard és prémium szinten egységenként. Fejlesztői csomag legfeljebb 1024.
<sup>4</sup> csak prémium szinten elérhető.


