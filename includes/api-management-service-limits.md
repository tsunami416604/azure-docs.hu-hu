---
title: fájl belefoglalása
description: fájl belefoglalása
services: api-management
author: vladvino
ms.assetid: 1b813833-39c8-46be-8666-fd0960cfbf04
ms.service: api-management
ms.topic: include
ms.date: 01/10/2020
ms.author: vlvinogr
ms.custom: include file
ms.openlocfilehash: 2d4c8c2c831bd6ef16f60c34a6353f4a742798f7
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76159223"
---
| Erőforrás | Korlát |
| ---------------------------------------------------------------------- | -------------------------- |
| A skálaegységek maximális száma | 10 régiónként<sup>1</sup> |
| Gyorsítótár mérete | 5 GiB egységenként<sup>2</sup> |
| Egyidejű háttérkapcsolatok HTTP-szolgáltatónként<sup>3</sup> | 2,048 egységenként<sup>4</sup> |
| Gyorsítótárazott válasz maximális mérete | 2 MiB |
| Házirenddokumentum maximális mérete | 256 KiB<sup>5</sup> |
| Egyéni átjárótartományok maximális kiszolgálópéldányonkénti<sup>6</sup> | 20 |
| Hitelesítésszolgáltatói tanúsítványok maximális száma szolgáltatáspéldányonként | 10 |
| Szolgáltatási példányok maximális száma előfizetésenként<sup>7</sup> | 20 |
| Előfizetések maximális száma szolgáltatáspéldányonként<sup>7</sup> | 500 |
| Az ügyféltanúsítványok maximális száma szolgáltatáspéldányonként<sup>7</sup> | 50 |
| Api-k maximális száma szolgáltatáspéldányonként<sup>7</sup> | 50 |
| Az API-műveletek maximális száma szolgáltatáspéldányonként<sup>7</sup> | 1,000 |
| A kérelem maximális teljes időtartama<sup>7</sup> | 30 másodperc |
| Maximális pufferelt hasznos teher mérete<sup>7</sup> | 2 MiB |
| A kérelem URL-mérete<sup>8</sup> | 4096 bájt |

<sup>1 1</sup> A skálázási korlátok a tarifacsomagtól függenek. A tarifacsomagok és azok méretezési korlátainak megtekintéséhez tekintse meg [az API Management díjszabását.](https://azure.microsoft.com/pricing/details/api-management/)<br/>
<sup>2.</sup> Egységnyi gyorsítótár mérete a tarifacsomagtól függ. A tarifacsomagok és azok méretezési korlátainak megtekintéséhez tekintse meg [az API Management díjszabását.](https://azure.microsoft.com/pricing/details/api-management/)<br/>
<sup>3. 20 0</sup> A kapcsolatok összevonása és újrafelhasználható, kivéve, ha a háttérrendszer kifejezetten bezárja azokat.<br/>
<sup>4.</sup> Ez a korlát az alapszintű, standard és prémium szintű egységek egysége. A fejlesztői szint legfeljebb 1024. Ez a korlát nem vonatkozik a felhasználási szintre.<br/>
<sup>5.</sup> Ez a korlát az alapszintű, standard és prémium csomagokra vonatkozik. A felhasználási szintben a házirenddokumentum mérete 4 KiB-re korlátozódik.<br/>
<sup>6.</sup> Ez az erőforrás csak a prémium szinten érhető el.<br/>
<sup>7.</sup> Ez az erőforrás csak a Felhasználás szintre vonatkozik.<br/>
<sup>8.</sup> Csak a Felhasználás szintre vonatkozik. Legfeljebb 2048 bájt hosszú lekérdezési karakterláncot tartalmaz.<br/>
