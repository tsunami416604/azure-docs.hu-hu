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
ms.openlocfilehash: bee289da3f18edd0cb425f3d9acde084567a3b13
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
| Erőforrás | Korlát |
| --- | --- |
| A méretezési egységek | régiónként eltérő 10<sup>1</sup> |
| Gyorsítótár | 5 GB / egység<sup>1</sup> |
| Egyidejű háttér kapcsolatok<sup>2</sup> egy HTTP-szolgáltató | egységenként 2048<sup>3</sup> |
| A gyorsítótárazott válasz maximális mérete | 10MB |
| Maximális egyéni átjáró tartományok | szolgáltatáspéldány 20<sup>4</sup> |


<sup>1</sup>API Management korlátok eltérőek az egyes tarifacsomagok. A árképzési szinteket, és a méretezési korlátok válassza [API Management árképzési](https://azure.microsoft.com/pricing/details/api-management/).
<sup>2</sup> kapcsolatok készletezett, és újból felhasználni, kivéve, ha explicit módon a háttérrendszer által lezárt.
<sup>3</sup> a Basic, Standard és Premium rétegek egységenként. Fejlesztői réteg 1024 korlátozódik.
<sup>4</sup> a prémium csomagban csak akkor érhető el.


